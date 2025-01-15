---
layout: post
title: Multiple Azure Boards organizations / one GitHub organization integration
date: 2025-01-09 01:00:00
description: This blog explains how to set-up an advanced way configuring Azure Boards / GitHub integration with multiple Azure DevOps organizations backed by different Entra ID's.
categories: AzureDevOps GitHub
typograms: false
---

One of our customers faced a challenge in their GitHub adoption journey. The company operates multiple Azure DevOps organizations, each backed by different Entra ID tenants. This structure evolved organically due to the company's nature—frequent acquisitions and sales of businesses, which, as we know, often complicates IT integration efforts.

The company had a critical requirement: use Azure Boards from all their Azure DevOps organizations from single GitHub organization.

## Connecting the first Azure DevOps organization

Connecting the first Azure DevOps organization was straightforward. You simply install the [Azure Boards App](https://learn.microsoft.com/en-us/azure/devops/boards/github/install-github-app?view=azure-devops) in the GitHub organization, and it prompts you for the necessary permissions. It first asks if you want to select a selection of repositories or all repositories.

{% include figure.liquid loading="eager" path="assets/img/azureboards1.png" class="img-fluid rounded z-depth-0" %}

After your choice, a pop-up prompts you to select your Azure DevOps organization and project, and just like that, the GitHub connection is established. Easy-peasy.

{% include figure.liquid loading="eager" path="assets/img/azureboards2.png" class="img-fluid rounded z-depth-0" %}

## The problem – Connecting other Azure DevOps organizations

But now it is unclear how to connect another Azure DevOps organization. Several blog posts highlight that if you choose the "Only select repositories" option, add a repository and press Save, the Azure DevOps pop-up will appear, allowing you to select a different Azure DevOps organization and/or project.

{% include figure.liquid loading="eager" path="assets/img/azureboards3.png" class="img-fluid rounded z-depth-0" width="50%" height="50%" %}

However, how do you switch accounts? In several setups this complete flow goes w00sh. 404 and 403 pages, if you are lucky, you can press the ‘switch tenant’ button and you return to Azure DevOps but no GitHub connection. Retry and your token is expired. No way you can make this flow work.

It almost seems that authorize with a GitHub PAT is the only way forward. However, we don’t want to use a PAT since it is personal bound and should not be used for generic integration services.

## The Solution – Simpler Than You Think

Microsoft designed the Azure Boards App for GitHub to offer a seamless installation process with redirects and a single, straightforward flow. However, this predefined flow can sometimes be problematic. To address these issues, we need to bypass the automated flow and manually establish the connection.

### Install the Azure Boards App

Choose the option for “All repositories” during installation of the Azure Boards app. This grants the app access to all repositories without requiring specific selections. Unfortunately, this appears to be the only viable option, as attempting to add or remove repositories later triggers the automated flow back to Azure DevOps, which disrupts the setup. I need to investigate this further to determine whether this behavior can be adjusted or customized to avoid these complications.

{% include figure.liquid loading="eager" path="assets/img/azureboards4.png" class="img-fluid rounded z-depth-0" %}

Within Azure DevOps, select the repositories you want to connect to the project. This ensures the proper linkage between the repositories and Azure Boards.

### How to add additional Azure DevOps organizations/projects

An additional Azure DevOps organisation can be set-up with these steps below:

1. Start with a Private Browser Session
   - Open a private/incognito browser window to avoid conflicts with existing sessions.
2. Log in to Azure DevOps
   - Navigate to the Azure DevOps project you want to connect.
   - Since you're in a private session, you'll be prompted to log in.
   - Select the option to "Stay signed in" for the session.
3. Access GitHub Connections
   - In the Azure DevOps project settings, locate and open the GitHub Connections page.
4. Connect Your GitHub Account
   - Click Connect with your GitHub Account.
   - Follow the prompts to log in to your GitHub account.
5. Authorize the GitHub Organization
   - Select the GitHub organization you want to authorize.
   - If the organization uses Single Sign-On (SSO), log in using the account associated with your GitHub organization.
6. Authorize Microsoft
   - A pop-up will appear asking you to Authorize Microsoft.
   - Ensure you grant access to the correct GitHub organization to complete the connection.
7. Handle the Redirection Issue
   - After authorizing Microsoft, you'll be redirected back to the Azure DevOps GitHub Connections screen.
   - You may notice that no connection has been established yet.
8. Reconnect and Select Repositories
   - Click Connect with your GitHub Account once more.
   - Choose the appropriate GitHub organization again. This time, select the repositories you want to connect manually to complete the integration.

{% include figure.liquid loading="eager" path="assets/img/azureboards5.png" class="img-fluid rounded z-depth-0" width="50%" height="50%" %}

### User Authorization

The Azure Boards app facilitates the authentication from Azure DevOps towards GitHub. It creates a webhook in the GitHub repository that will send the information towards Azure DevOps.

When adding or removing repositories, take note of step 6, "Authorize Microsoft." This authorization is specifically required to access the organization and repository list from GitHub. Any user with Project Administrator permissions who adds or removes GitHub repositories in Azure DevOps will encounter this authorization pop-up. It is unrelated to the Azure Boards App.

Once authorized, the approval is registered as an OAuth app in your personal GitHub settings. Each user must authorize the app individually to add or remove repositories in Azure DevOps. However, this authorization is solely for browsing repositories during the add/remove process and does not affect the connection flow.

{% include figure.liquid loading="eager" path="assets/img/azureboards6.png" class="img-fluid rounded z-depth-0" %}

## Azure Boards Permissions on Area Paths

There’s some behind-the-scenes functionality that comes into play when GitHub interacts with Azure Boards to update PBIs.
When you link GitHub to Azure DevOps, a new account named ‘Azure Boards’ is automatically created. Microsoft describes this account as follows:

"Added when Azure Boards is connected to GitHub. You shouldn't have to manage members of this group. Responsible for managing the link creation between GitHub and Azure Boards."

You can find this at the [permission](https://learn.microsoft.com/en-us/azure/devops/organizations/security/permissions?view=azure-devops&tabs=preview-page) page.

{% include figure.liquid loading="eager" path="assets/img/azureboards7.png" class="img-fluid rounded z-depth-0" width="50%" height="50%" %}

It’s important to note that this account is granted permissions only at the root level of your Area Paths. If you disable inheritance for lower-level Area Paths, the ‘Azure Boards’ account will not have the necessary permissions to update user stories in those specific areas.

Happy AB#ing ;)
