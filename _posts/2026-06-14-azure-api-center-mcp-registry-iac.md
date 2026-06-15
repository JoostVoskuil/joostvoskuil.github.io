---
layout: post
title: How to manage your private MCP registry on Azure API Center with Infrastructure as Code
date: 2026-06-14 01:00:00
description: Infrastructure as Code for Your Private MCP Registry on Azure API Center
categories: GitHub Copilot, MCP
typograms: false
---

In December 2025, Tajinder Singh published an excellent guide on [creating a private MCP registry using Azure API Center](https://devblogs.microsoft.com/all-things-azure/locking-down-mcp-create-a-private-registry-on-azure-api-center-and-enforce-it-in-github-copilot-and-vs-code/). That post walks you through the portal experience: clicking buttons, filling forms, and wiring the registry URL into GitHub Enterprise settings.

But there is one big gap: **how do you manage MCP servers as code?**

The portal is fine for a quick demo. For production adoption, you need repeatable deployments, version control, pull request reviews, and CI/CD. This post fills that gap with a complete Bicep + PowerShell solution that deploys your entire MCP registry from code, including the undocumented API properties that the Azure documentation does not cover.

## Why Infrastructure as Code

The portal works for adding servers manually. But when you have 10+ servers, multiple environments, and a team that needs to review changes, you need:

- **Pull request workflow** - changes to the registry go through code review
- **Drift detection** - deployed state matches source control
- **Automated pruning** - servers removed from code get removed from Azure
- **Repeatable deployments** - spin up identical registries for dev/test/prod
- **Disaster recovery** - rebuild the entire registry from a single `git clone`

## The Problem: Undocumented API Surface

If you open the Azure documentation for API Center's MCP support, you will find instructions for the portal UI. What you will _not_ find is:

- The full set of properties the `Microsoft.ApiCenter/services/workspaces/apis` resource accepts for MCP-type APIs
- How `remote`, `remoteType`, `securitySchemes`, `packages`, `authSchemas`, and `audience` map to the registry response
- How deployments with `runtimeUri` connect to the `/v0.1/servers` output
- The fact that an empty `description` field makes the server **invisible** to VS Code (a known bug confirmed by the product team)

The [Azure MCP partner schema](https://github.com/Azure/MCP/blob/main/partners/server-schema.json) defines the JSON structure for MCP servers, but the Azure Resource Manager (ARM) API for API Center does not have a 1:1 mapping with that schema. Some fields are accepted by the ARM API but not defined in the Bicep type system, which leads to compile-time errors if you try to use them directly.

## Architecture Overview

```md
api-center-mcp-registry/
├── infra/
│ ├── workspace.bicep # Reusable module: workspace + env + MCP servers
│ └── workspaces/
│ ├── default.bicep # Default workspace (loads JSON server defs)
│ └── mcp-servers/ # One JSON file per MCP server
│ ├── azure-devops-mcp-remote.json
│ ├── figma-mcp-remote.json
│ └── playwright-mcp.json
├── deploy-workspace.ps1 # Deploy workspace + auto-prune
└── common.psm1 # Shared PowerShell helpers
```

The key design decisions:

1. **One JSON file per server** - easy to add/remove servers via PR
2. **`loadJsonContent()` at compile time** - Bicep reads JSON at build, no parameter files needed
3. **Automatic pruning** - after deployment, servers not in the Bicep file get deleted from Azure
4. **Reusable workspace module** - same Bicep works for any workspace name

## Step 1: Deploy API Center

I assume you already have an Azure API Center service deployed. It is pretty easy to do it through Bicep.

## Step 2: Define MCP Servers as JSON

Each MCP server is a single JSON file. Here is a complete example for Figma:

```json
{
  "name": "figma-mcp",
  "title": "Figma MCP Server",
  "summary": "Brings Figma design context directly into your AI workflow.",
  "description": "The Figma MCP server brings Figma directly into your workflow by providing design information and context to AI agents generating code from Figma design files.",
  "kind": "mcp",
  "vendor": "Partner",
  "externalDocumentation": {
    "title": "Figma MCP Server Guide",
    "url": "https://developers.figma.com/docs/figma-mcp-server/"
  },
  "remote": "https://mcp.figma.com/mcp",
  "remoteType": "streamable-http",
  "supportContactInfo": {
    "name": "Figma Support",
    "url": "https://help.figma.com/hc/en-us/articles/32132100833559"
  },
  "license": {
    "name": "Figma Developer Terms",
    "url": "https://www.figma.com/legal/developer-terms/"
  },
  "categories": "Developer Tools",
  "icon": "https://avatars.githubusercontent.com/u/5155369?v=4",
  "useCases": [
    {
      "name": "Read designs",
      "description": "Retrieve objects and metadata from Figma files for AI code generation."
    },
    {
      "name": "Write to canvas",
      "description": "Create and modify Figma content directly from your MCP client."
    }
  ],
  "securitySchemes": {
    "figmaoauth2": {
      "type": "oauth2",
      "description": "Authenticate with Figma using OAuth2.",
      "flows": ["authorizationCode"],
      "authorizationUrl": "https://www.figma.com/oauth",
      "tokenUrl": "https://api.figma.com/v1/oauth/token",
      "refreshUrl": "https://api.figma.com/v1/oauth/refresh",
      "scopes": ["files:read", "file_dev_resources:read"]
    }
  },
  "authSchemas": ["OAuth2"],
  "audience": "https://api.figma.com",
  "versionName": "1-0-3",
  "customProperties": { "x-ms-preview": true }
}
```

A second example for playwright without remote fields but with packages:

```json
{
  "name": "playwright-mcp",
  "title": "Playwright MCP Server",
  "summary": "Browser automation using accessibility trees for testing and data extraction via MCP.",
  "description": "A Model Context Protocol server that provides browser automation capabilities using Playwright. Enables LLMs to interact with web pages through structured accessibility snapshots, bypassing the need for screenshots or visually-tuned models. Fast, lightweight, and LLM-friendly with deterministic tool application.",
  "kind": "mcp",
  "vendor": "Microsoft",
  "externalDocumentation": {
    "title": "Playwright MCP on npm",
    "url": "https://www.npmjs.com/package/@playwright/mcp"
  },
  "packages": [
    {
      "registry_name": "npm",
      "name": "@playwright/mcp",
      "version": "latest",
      "runtime_hint": "npx",
      "runtime_arguments": [],
      "package_arguments": [],
      "environment_variables": []
    }
  ],
  "supportContactInfo": {
    "name": "Microsoft Playwright Team",
    "url": "https://github.com/microsoft/playwright-mcp/issues"
  },
  "license": {
    "name": "Apache-2.0",
    "url": "https://github.com/microsoft/playwright-mcp/blob/main/LICENSE"
  },
  "categories": "Developer Tools",
  "icon": "https://avatars.githubusercontent.com/u/6154722?v=4",
  "useCases": [
    {
      "name": "Browser automation for LLMs",
      "description": "Automate web browser interactions through structured accessibility tree data, enabling AI agents to navigate, click, fill forms, and extract content without vision models."
    },
    {
      "name": "Web testing",
      "description": "Create and run browser-based tests using accessibility snapshots for deterministic, repeatable web application testing driven by AI agents."
    },
    {
      "name": "Data extraction",
      "description": "Extract structured data from web pages by navigating and reading accessibility tree information, enabling AI-powered web scraping and content analysis."
    },
    {
      "name": "Exploratory automation",
      "description": "Support autonomous workflows that benefit from persistent browser state and iterative reasoning over page structure for self-healing tests or long-running automation."
    }
  ],
  "versionName": "latest",
  "customProperties": { "x-ms-preview": false }
}
```

Now this is allot of information. You can peek at the formal [GitHub MCP Registry](https://api.mcp.github.com/v0/servers) to get inspired.

### Required Fields

| Field                   | Purpose                                                 |
| ----------------------- | ------------------------------------------------------- |
| `name`                  | Unique identifier (becomes the API resource name)       |
| `title`                 | Display name in VS Code                                 |
| `summary`               | Short one-liner shown in extension list                 |
| `description`           | **Must not be empty** or server is invisible to VS Code |
| `kind`                  | Always `"mcp"`                                          |
| `externalDocumentation` | Link shown to users                                     |
| `versionName`           | Version label (e.g. `"original"` or `"1-0-3"`)          |
| `customProperties`      | Required object, can be `{}`                            |

### Remote Server Fields

For servers hosted externally (most MCP servers today):

| Field        | Purpose                        |
| ------------ | ------------------------------ |
| `remote`     | The MCP endpoint URL           |
| `remoteType` | `"streamable-http"` or `"sse"` |

When `remote` is set, the Bicep automatically creates a deployment resource with `runtimeUri` pointing to that URL. This is what makes the server appear in the `/v0.1/servers` response with a working `remotes` array.

### Package-based Server Fields

For servers distributed as runnable packages instead of hosted endpoints:

| Field      | Purpose                                             |
| ---------- | --------------------------------------------------- |
| `packages` | Array of package descriptors used by the MCP client |

Each package entry can include values such as:

- `registry_name` - package registry identifier such as `npm`
- `name` - package name, for example `@playwright/mcp`
- `version` - package version or tag such as `latest`
- `runtime_hint` - how the client should launch it, for example `npx`
- `runtime_arguments`, `package_arguments`, `environment_variables` - optional execution metadata

### Optional Enrichment Fields

| Field                | Purpose                                    |
| -------------------- | ------------------------------------------ |
| `vendor`             | `"Partner"` or organization name           |
| `icon`               | URL to avatar/icon image                   |
| `useCases`           | Array of `{name, description}` objects     |
| `categories`         | Category string (e.g. `"Developer Tools"`) |
| `supportContactInfo` | `{name, url}` for support                  |
| `license`            | `{name, url}` for license info             |
| `securitySchemes`    | OAuth2/API key definitions                 |
| `authSchemas`        | Array like `["OAuth2"]`                    |
| `audience`           | Token audience URL                         |

## Step 3: The Workspace Bicep Module

This is the core module that turns JSON definitions into Azure resources. The key challenge: **Bicep's type system does not know about all MCP properties**. The ARM API accepts them, but Bicep validation rejects unknown fields.

The solution: wrap properties in `any(union(...))` to bypass compile-time type checks:

```bicep
param serviceName string
param workspaceName string = 'default'
param workspaceTitle string = 'Default workspace'
param workspaceDescription string = 'Default workspace'
param environmentName string = 'default-mcp-env'
param environmentTitle string = 'Default MCP Environment'
param environmentKind string = 'Production'
param mcpServers array = []

resource apiCenterService 'Microsoft.ApiCenter/services@2024-06-01-preview' existing = {
  name: serviceName
}

resource workspace 'Microsoft.ApiCenter/services/workspaces@2024-06-01-preview' = {
  parent: apiCenterService
  name: workspaceName
  properties: {
    title: workspaceTitle
    description: workspaceDescription
  }
}

resource mcpEnvironment 'Microsoft.ApiCenter/services/workspaces/environments@2024-06-01-preview' = {
  parent: workspace
  name: environmentName
  properties: {
    title: environmentTitle
    kind: environmentKind
    description: '${workspaceName} MCP environment'
    customProperties: {}
  }
}

resource mcpServerApis 'Microsoft.ApiCenter/services/workspaces/apis@2024-06-01-preview' = [for server in mcpServers: {
  parent: workspace
  name: server.name
  properties: any(union(
    {
      title: server.title
      summary: server.summary
      description: server.description
      kind: server.kind
      externalDocumentation: [server.externalDocumentation]
      customProperties: server.customProperties
    },
    contains(server, 'packages') ? { packages: server.packages } : {},
    contains(server, 'vendor') ? { vendor: server.vendor } : {},
    contains(server, 'icon') ? { icon: server.icon } : {},
    contains(server, 'useCases') ? { useCases: server.useCases } : {},
    contains(server, 'categories') ? { categories: server.categories } : {},
    contains(server, 'supportContactInfo') ? { supportContactInfo: server.supportContactInfo } : {},
    contains(server, 'license') ? { license: server.license } : {},
    contains(server, 'remote') ? { remote: server.remote } : {},
    contains(server, 'remoteType') ? { remoteType: server.remoteType } : {},
    contains(server, 'securitySchemes') ? { securitySchemes: server.securitySchemes } : {},
    contains(server, 'authSchemas') ? { authSchemas: server.authSchemas } : {},
    contains(server, 'audience') ? { audience: server.audience } : {}
  ))
}]

resource mcpServerVersions 'Microsoft.ApiCenter/services/workspaces/apis/versions@2024-06-01-preview' = [for (server, index) in mcpServers: {
  parent: mcpServerApis[index]
  name: server.versionName
  properties: {
    title: server.versionName
    lifecycleStage: 'production'
  }
}]

resource mcpServerDefinitions 'Microsoft.ApiCenter/services/workspaces/apis/versions/definitions@2024-06-01-preview' = [for (server, index) in mcpServers: {
  parent: mcpServerVersions[index]
  name: 'default-definition'
  properties: {
    title: 'Definition for ${server.title}'
    description: 'Auto-generated definition for ${server.title}'
  }
}]

resource mcpServerDeployments 'Microsoft.ApiCenter/services/workspaces/apis/deployments@2024-06-01-preview' = [for (server, index) in mcpServers: if (contains(server, 'remote')) {
  parent: mcpServerApis[index]
  name: 'default-deployment'
  dependsOn: [
    mcpEnvironment
    mcpServerDefinitions[index]
  ]
  properties: {
    title: 'Deployment to ${environmentName}'
    environmentId: '/workspaces/${workspaceName}/environments/${environmentName}'
    definitionId: '/workspaces/${workspaceName}/apis/${server.name}/versions/${server.versionName}/definitions/default-definition'
    server: {
      runtimeUri: [server.remote]
    }
    customProperties: server.customProperties
  }
}]
```

### Why `any(union(...))`?

The `Microsoft.ApiCenter/services/workspaces/apis` Bicep type definition only knows about a subset of properties. Fields like `remote`, `remoteType`, `securitySchemes`, and `authSchemas` are accepted by the ARM API but not in the Bicep type. Without `any()`, you get compile errors. Without `union()` with conditional merges, you pass `null` values that the API rejects.

This pattern:

```bicep
contains(server, 'remote') ? { remote: server.remote } : {}
```

Only includes the property when the JSON file defines it. Empty objects merge cleanly with `union()`.

## Step 4: Wire It Up with loadJsonContent

The workspace-specific Bicep file loads all server JSONs at compile time:

```bicep
param serviceName string
param environmentName string = 'default-mcp-env'
param environmentTitle string = 'Default MCP Environment'
param environmentKind string = 'Production'

var workspaceName = 'default'
var workspaceTitle = 'Default workspace'
var workspaceDescription = 'Default workspace'

var mcpServers = [
  loadJsonContent('./mcp-servers/azure-devops-mcp-remote.json')
  loadJsonContent('./mcp-servers/datadog-mcp-remote.json')
  loadJsonContent('./mcp-servers/figma-mcp-remote.json')
  loadJsonContent('./mcp-servers/playwright-mcp.json')
]

module workspace '../workspace.bicep' = {
  name: 'workspace-${workspaceName}'
  params: {
    serviceName: serviceName
    workspaceName: workspaceName
    workspaceTitle: workspaceTitle
    workspaceDescription: workspaceDescription
    environmentName: environmentName
    environmentTitle: environmentTitle
    environmentKind: environmentKind
    mcpServers: mcpServers
  }
}
```

Adding a new server: create the JSON file, add one line here, deploy.

## Step 5: Automatic Pruning

When you remove a server from code, you also want it removed from Azure. The `deploy-workspace.ps1` script handles this after deployment:

```powershell
# Parse which servers are referenced in the Bicep file
$desiredServerIds = @(
    Select-String -Path $templateFile -Pattern "loadJsonContent\('([^']+)'\)" -AllMatches |
        ForEach-Object { $_.Matches } |
        ForEach-Object {
            $jsonPath = Join-Path (Split-Path $templateFile) $_.Groups[1].Value
            if (Test-Path $jsonPath) {
                (Get-Content $jsonPath -Raw | ConvertFrom-Json).name
            }
        } | Where-Object { $_ }
)

```

The pruning function lists all APIs in the workspace via the ARM REST API, filters for `kind: mcp`, and deletes any that are not in the desired set. This means your code is the single source of truth.
