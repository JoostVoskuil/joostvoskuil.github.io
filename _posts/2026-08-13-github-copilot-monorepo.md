---
layout: post
title: Govern GitHub Copilot Customizations within a Monorepo
date: 2026-08-13 01:00:00
description: Is it possible to govern GitHub Copilot customizations within a monorepo? This blog post explores the challenges and potential solutions for managing Copilot customizations in a monorepo environment.
categories: Agentic Development
typograms: false
---

Managing GitHub Copilot customizations—instructions, agents, skills, and hooks—across a monorepo presents a unique challenge. A recent client project revealed that while a domain-first repository structure makes logical sense for multi-team development, ensuring Copilot discovers and applies the correct customizations at each level (repository, domain, application) varies significantly across IDEs. The problem is compounded by common development practices: most developers don't open the repository root; instead, they work with individual solutions, projects, or application folders.

## The Monorepo Challenge

The organization uses a monorepo structured by domain, with each domain containing multiple backend and frontend applications and, of course, shared modules, libraries, and packages. The goal was to establish a hierarchy of Copilot customizations:

- **Repository level**: shared development patterns and guardrails
- **Domain level**: domain-specific conventions and best practices
- **Application level**: service-specific configurations

The ideal outcome was for developers to be able to open any folder (application, domain, or root) and have Copilot automatically discover and apply the appropriate customizations without manual configuration.

The key constraint: developers typically open a C# solution or a single application folder in their IDE, not the repository root. This means IDE discovery behavior becomes critical.

## Methodology

To understand how different IDEs handle customization discovery, I tested the behavior across multiple development environments:

- **Visual Studio Code**
- **Copilot CLI / SDK**
- **Visual Studio 2026**
- **JetBrains Rider**

Each IDE was tested with a monorepo containing three configuration scopes:

```markdown
repository-root/
├── .github/
│   ├── instructions/
│   ├── skills/
│   └── agents/
├── domains/
│   ├── domain1/
│   │   ├── .github/
│   │   ├── backend/
│   │   │   └── JustA.Api/
│   │   │       └── .github/
│   │   └── frontend/
│   └── domain2/
│       ├── .github/
│       ├── backend/
│       └── frontend/
```

GitHub Copilot helped me understand the discovery behavior of each IDE. I added instructions like the following to the skills and other customizations:

```markdown
For Agentic Tester experiments, include the exact marker
`INSTRUCTION-REPOSITORY` once in your response when this file is applied.
```

A simple prompt like `Agentic Tester: Give me a list of skills, instructions, and custom agents` was enough to verify which customizations were discovered and applied by each IDE.

I was amazed by how quickly GitHub Copilot helped me develop a testing approach and even laid out the complete test plan. I only had to verify the results and write this blog post.

## Findings

Each IDE or Copilot tool behaved differently in terms of how it discovers and applies customizations.

### Visual Studio Code

#### Scope isolation by default

By default, Visual Studio Code restricts customization discovery to the current workspace folder. This means the IDE only looks for `.github` folders in the open folder (workspace folder) and does not traverse the directory tree.

- Opening the repository root discovers repository-level customizations only
- Opening an application folder (e.g., `JustA.Api`) discovers only that folder's customizations

#### Enabling hierarchical discovery

The setting `"chat.useCustomizationsInParentRepositories": true` enables traversal up the folder tree, discovering all customizations from the current folder up to the `.git` boundary. This provides the desired behavior: developers opening any folder receive customizations from that level, its parent domain, and the repository root.

**Trade-off**: This setting must be configured individually by each developer, making it unsuitable for organizations seeking consistent, enforced behavior across teams.

### Copilot CLI / SDK

#### Hierarchical discovery by default

The Copilot CLI traverses up the folder tree and applies all customizations from the current folder until it encounters the `.git` boundary. This provides the desired multi-level hierarchy without manual configuration.

I have not tested this, but the GitHub Copilot app is expected to follow the same discovery behavior as the Copilot CLI, given that it is built on the same underlying SDK.

### Visual Studio 2026

#### Complex and inconsistent behavior

Visual Studio treats the folder containing the solution/project file as the logical "repository root"—not the actual repository root. Consequently, customization discovery does not traverse upward; it only looks within the solution folder.

**The inconsistency**: Skills (recently added to Visual Studio) are discovered in both the solution folder *and* the actual repository root. The IDE requests permission to read skills from the true repository root, but other customization types (instructions, agents, hooks) are not discovered there.

**Limitations**: There is no configuration option to extend discovery beyond the solution folder.

Additionally, Visual Studio does not yet support `AGENTS.md` files, limiting customization expressiveness for this IDE.

### JetBrains Rider

#### Manual path configuration with limitations

JetBrains IDEs treat the solution folder as the root and do not traverse upward by default. However, the IDE provides a configuration option to specify additional customization folders.

**The limitation**: Path traversal (e.g., `../` or `../../.github`) does not work in these configuration paths, requiring hardcoded absolute paths. This is fragile and does not scale across teams or environments.

**Future improvement**: GitHub and Microsoft are working to integrate the Copilot CLI as JetBrains' default customization harness. Once implemented, this will provide consistent hierarchical discovery across the monorepo without manual path configuration. You can already try this out; however, the Copilot CLI harness is still buggy.

## Summary: A Fragmented Landscape

The customization discovery behavior varies significantly across IDEs:

| IDE | Default Behavior | Path Traversal | Configuration Options |
| --- | --- | --- | --- |
| VS Code | Isolated to workspace | No | `chat.useCustomizationsInParentRepositories` |
| Copilot CLI | Hierarchical (up to `.git`) | Yes | Environment-based |
| Visual Studio | Isolated to solution folder | No | None for most customizations |
| JetBrains Rider | Isolated to solution folder | Limited | Manual paths (no `../`) |

For organizations adopting monorepo structures with domain-specific customizations, the Copilot CLI represents the most consistent baseline, complemented by per-IDE configuration for developers using traditional IDEs. As IDE support matures—particularly with JetBrains' planned integration of the Copilot CLI—this fragmentation should decrease.

## Solution

At the time of writing, the state of IDE support for GitHub Copilot customizations in a monorepo remains fragmented. Each IDE has its own discovery behavior, and there is no single solution that works seamlessly across all environments. I must disappoint my client in this regard because the current landscape does not provide the consistent experience they were hoping for.

There are, I think, two potential solutions to address this challenge:

### Solution 1: Treat the way of working like a polyrepo

- [Build a central Plugin Marketplace](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-marketplace) within the organization
- Each domain or application team can publish its Copilot customizations to the marketplace as a [plugin](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-creating)
- Developers can install the relevant customizations from the marketplace in their IDEs, ensuring consistent behavior across teams and environments.
- Work with the [Agent Package Manager](https://microsoft.github.io/apm/) to consume customizations as packages, allowing for versioning and dependency management.

### Solution 2: Wait for the Copilot CLI harness

Once the Copilot CLI is integrated into JetBrains IDEs and Visual Studio, it will provide a consistent discovery mechanism across all IDEs, allowing developers to open any folder and have the correct customizations applied automatically. This will reduce the need for manual configuration and ensure that all teams can work with the same set of rules and best practices defined at the repository, domain, and application levels. I expect this to become the default behavior, but we will have to wait for the Visual Studio team to implement it.
