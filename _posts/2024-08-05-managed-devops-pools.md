---
layout: post
title: Managed DevOps Pools in Bicep
date: 2024-08-05 22:00:00
description: This blog explains how you can deploy Managed DevOps Pools with Bicep
#tags: Testing
categories: DevOps, AzureDevOps, Azure, MDP, Pools
typograms: false
---

Today, I played around with the new preview feature of Azure and Azure DevOps called 'Managed DevOps Pools.'
Finally, the burden of managing agents is over. As platform engineers, we were already somewhat relieved with the VMMS agent pools where automatic scaling was an option. Still, there was a big hassle of managing images. In most companies I supported, we created our own image based on Microsoft's Hosted Agents. Yup, every week, failing pipelines because the image creation failed... again and again.

Those days are now over with the Managed DevOps Pools. I could list all the advantages, but Microsoft's documentation is really good on the Managed DevOps Pools. You can read more on the [Managed DevOps Pools learn page](https://learn.microsoft.com/en-us/azure/devops/managed-devops-pools/?view=azure-devops). Still, I need to mention VNET integration. FTW!.. w00t!

Please note: This feature is still in preview.

I already saw blogs on the internet but what I am missing is how to deploy the Managed DevOps Pools with bicep. Microsoft seems not yet documented the API. Based on the documentation and the ARM templates on the learn page I did some engineering.

## Notes

- It seems that SystemAssigned Managed Identities are not supported. You need to create UserAssigned Identities if you need them.
- I could not get this working in the 'West Europe' region. I get the error message that there are no subscriptions available. This is the reason why I choose 'East US' as region
- At this moment the scaling plane is broken in the Azure portal

## Preperations

1. Create a Service Principal in Azure and use this Service Principal with secret to create an ARM Service Connection in Azure DevOps to Azure. You also can use the workflow identity option however I am not sure if you can add this identity
2. Add the Service Principal that you created to Azure DevOps as an user and give it the correct permissions. Microsoft explains this on this [page](https://learn.microsoft.com/en-us/azure/devops/managed-devops-pools/prerequisites?view=azure-devops&tabs=powershell#verify-azure-devops-permissions).

### The Pipeline

The Azure DevOps pipeline is pretty straightforward. We need to register the `Microsoft.DevOpsInfrastructure` provider first and then we can deploy through Bicep.

**azure-pipelines.yml**:

```yaml filename="azure-pipelines.yml"
steps:
  - task: AzurePowerShell@5
    displayName: "Register DevOpsInfrastructure"
    inputs:
      azureSubscription: $(ARMServiceConnection)
      ScriptType: "InlineScript"
      Inline: "Register-AzResourceProvider -ProviderNamespace 'Microsoft.DevOpsInfrastructure'"
      azurePowerShellVersion: "LatestVersion"
      pwsh: true

  - task: AzureResourceManagerTemplateDeployment@3
    displayName: "Deploy Managed DevOps Pools"
    inputs:
      deploymentScope: "Subscription"
      azureResourceManagerConnection: $(ARMServiceConnection)
      subscriptionId: $(SubscriptionId)
      location: "West Europe"
      templateLocation: "Linked artifact"
      csmFile: "main.bicep"
      deploymentMode: "Incremental"
```

### Supporting Resources

**devCenter.bicep**:

```ts filename="devCenter.bicep"
param devCenterName string
param location string = resourceGroup().location

resource devCenter 'Microsoft.DevCenter/devcenters@2024-02-01' = {
  name: 'dc-${devCenterName}'
  location: location
}

resource devCenterProject 'Microsoft.DevCenter/projects@2024-02-01' = {
  name: 'dcp-${devCenterName}'
  location: location
  properties: {
    description: 'Azure DevOps Managed DevOps Pools'
    devCenterId: devCenter.id
  }
}
```

**vnet.bicep**:

```ts filename="vnet.bicep"
param virtualNetworkName string
param subnetName string
param location string = resourceGroup().location

resource virtualNetwork 'Microsoft.Network/virtualNetworks@2023-09-01' = {
  name: 'vnet-${virtualNetworkName}'
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
    subnets: [
      {
        name: 'sn-${subnetName}'
        properties: {
          addressPrefix: '10.0.1.0/24'
        }
      }
    ]
  }
}
```

**managedIdentity.bicep**:

```ts filename="managedIdentity.bicep"
param location string = resourceGroup().location
param userAssignedIdentityName string

resource userAssignedIdentity 'Microsoft.ManagedIdentity/userAssignedIdentities@2023-01-31' = {
  name: 'mi-${userAssignedIdentityName}'
  location: location
}
```

### The real stuff

**main.bicep**:

```ts filename="main.bicep"
param location string = 'eastus'
param devCenterName string = 'azuredevops'
param resourceGroupName string = 'azuredevops'
param managedDevOpsPoolName string = 'foxholenl'
param userAssignedIdentityName string = 'azuredevops'
param azureDevOpsOrganizationName string = 'foxholenl'
param virtualNetworkName string = 'myvnet'
param subnetName string = 'azuredevops'

targetScope = 'subscription'

resource resourceGroup 'Microsoft.Resources/resourceGroups@2024-03-01' = {
  name: 'rg-${resourceGroupName}'
  location: location
}

module  vnet './vnet.bicep' = {
  name: 'vnet-${virtualNetworkName}'
  scope: resourceGroup
  params: {
    virtualNetworkName: virtualNetworkName
    subnetName: subnetName
  }
}

module userAssignedIdentity 'managedIdentity.bicep' = {
  name: 'mi-${userAssignedIdentityName}'
  scope: resourceGroup
  params: {
    userAssignedIdentityName: userAssignedIdentityName
  }
}

module devCenter './devCenter.bicep' = {
  name: 'dc-${devCenterName}'
  scope: resourceGroup
  params: {
    devCenterName: devCenterName
  }
}

module managedDevOpsPool './managedDevOpsPool.bicep' = {
  name: 'mdp-${managedDevOpsPoolName}'
  scope: resourceGroup
  params: {
    managedDevOpsPoolName: managedDevOpsPoolName
    userAssignedIdentityName: userAssignedIdentityName
    devCenterName: devCenter.name
    azureDevOpsOrganizationName: azureDevOpsOrganizationName
    subnetName: subnetName
    virtualNetworkName: virtualNetworkName
  }
}
```

### What you really want to know

**managedDevopsPool.bicep**:

```ts filename="managedDevopsPool.bicep"
param location string = resourceGroup().location
param managedDevOpsPoolName string
param devCenterName string
param azureDevOpsOrganizationName string
param userAssignedIdentityName string
param virtualNetworkName string
param subnetName string

resource devCenterProject 'Microsoft.DevCenter/projects@2024-02-01' existing = {
  name: 'dcp-${devCenterName}'
}

resource userAssignedIdentity 'Microsoft.ManagedIdentity/userAssignedIdentities@2023-01-31' existing = {
  name: 'mi-${userAssignedIdentityName}'
}

resource vnet 'Microsoft.Network/virtualNetworks@2024-01-01' existing = {
  name: virtualNetworkName
}

resource subnet 'Microsoft.Network/virtualNetworks/subnets@2024-01-01' existing = {
  name: subnetName
  parent: vnet
}

var userAssignedIdentityId = userAssignedIdentity.id

resource managedDevOpsPool 'Microsoft.DevOpsInfrastructure/pools@2024-04-04-preview' = {
  name: 'mdp-${managedDevOpsPoolName}'
  location: location
  identity: {
    type: 'UserAssigned'
    userAssignedIdentities: {
      '${userAssignedIdentityId}': {}
    }
  }
  properties: {
    organizationProfile: {
      organizations: [
        {
          url: 'https://dev.azure.com/${azureDevOpsOrganizationName}'
          // If you want to add the pool only to a subset of projects, uncomment below
          //projects: [
          //  'MyProject'
          //]
          parallelism: 1
        }
      ]
      permissionProfile: {
        kind: 'CreatorOnly'
      }
      kind: 'AzureDevOps'
    }
    devCenterProjectResourceId: devCenterProject.id
    maximumConcurrency: 1
    agentProfile: {
      kind: 'Stateless' // Or Stateful
      //maxAgentLifetime: '7.00:00:00'    // Only allowed if kind is Stateful
      //gracePeriodTimeSpan: '00:30:00'  // Only allowed if kind is Stateful
      resourcePredictionsProfile: {
        kind: 'Automatic' // Or 'Manual'
        predictionPreference: 'Balanced'
      }
    }
    fabricProfile: {
      sku: {
        name: 'Standard_D2ads_v5'
      }
      images: [
        {
          aliases: [
            'ubuntu-22.04'
            'ubuntu-22.04/latest'
          ]
          wellKnownImageName: 'ubuntu-22.04'
        }
        {
          aliases: [
            'windows-2022'
            'windows-2022/latest'
          ]
          wellKnownImageName: 'windows-2022'
        }
      ]
      osProfile: {
        logonType: 'Service' // Or Interactive
      }
      storageProfile: {
        osDiskStorageAccountType: 'StandardSSD' // StandardSSD, Standard or Premium
      }

      // Leave this empty if you want to use 'Isolated Virtual Network'
      networkProfile: {
        subnetId: subnet.id
      }
      kind: 'Vmss'
    }
  }
}

```

As you can see in the `managedDevopsPool.bicep` file, there are some options that you can pick. I haven't experimented yet with the clever scaling options.
