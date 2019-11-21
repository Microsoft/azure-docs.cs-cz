---
title: Azure Services that support managed identities - Azure AD
description: List of services that support managed identities for Azure resources and Azure AD authentication
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ead9b53f530a309d6bdb3bd384c29650bf5c8e6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224294"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services that support managed identities for Azure resources

Managed identities for Azure resources provide Azure services with an automatically managed identity in Azure Active Directory. Using a managed identity, you can authenticate to any service that supports Azure AD authentication without having credentials in your code. We are in the process of integrating managed identities for Azure resources and Azure AD authentication across Azure. Check back often for updates.

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Služby Azure, které podporují spravované identity pro prostředky Azure

The following Azure services support managed identities for Azure resources:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostupné | Preview | Preview | Preview | 
| User assigned | Dostupné | Preview | Preview | Preview |

Refer to the following list to configure managed identity for Azure Virtual Machines (in regions where available):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager templates](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostupné | Preview | Preview | Preview |
| User assigned | Dostupné | Preview | Preview | Preview |

Refer to the following list to configure managed identity for Azure Virtual Machine Scale Sets (in regions where available):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager templates](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostupné | Dostupné | Dostupné | Dostupné |
| User assigned | Dostupné | Není k dispozici | Není k dispozici | Není k dispozici |

Refer to the following list to configure managed identity for Azure App Service (in regions where available):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manageru](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostupné | Dostupné | Není k dispozici | Není k dispozici |
| User assigned | Dostupné | Dostupné | Není k dispozici | Není k dispozici |

Refer to the following list to use a managed identity with [Azure Blueprints](../../governance/blueprints/overview.md):

- [Azure portal - blueprint assignment](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - blueprint assignment](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funkce Azure

Managed identity type |All Generally Available<br>Global Azure Regions | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostupné | Dostupné | Dostupné | Dostupné |
| User assigned | Dostupné | Není k dispozici | Není k dispozici | Není k dispozici |

Refer to the following list to configure managed identity for Azure Functions (in regions where available):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manageru](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Preview | Preview | Není k dispozici | Preview |
| User assigned | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Refer to the following list to configure managed identity for Azure Logic Apps (in regions where available):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal-system-logic-app)
- [Šablona Azure Resource Manageru](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostupné | Není k dispozici | Není k dispozici | Není k dispozici |
| User assigned | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Refer to the following list to configure managed identity for Azure Data Factory V2 (in regions where available):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Sada SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostupné | Dostupné | Není k dispozici | Není k dispozici |
| User assigned | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Refer to the following list to configure managed identity for Azure API Management (in regions where available):

- [Šablona Azure Resource Manageru](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Linux: Preview<br>Windows: Not available | Není k dispozici | Není k dispozici | Není k dispozici |
| User assigned | Linux: Preview<br>Windows: Not available | Není k dispozici | Není k dispozici | Není k dispozici |

Refer to the following list to configure managed identity for Azure Container Instances (in regions where available):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Šablona Azure Resource Manageru](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Úlohy Azure Container Registry

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Dostupné | Není k dispozici | Není k dispozici | Není k dispozici |
| User assigned | Preview | Není k dispozici | Není k dispozici | Není k dispozici |

Refer to the following list to configure managed identity for Azure Container Registry Tasks (in regions where available):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure services that support Azure AD authentication

The following services support Azure AD authentication, and have been tested with client services that use managed identities for Azure resources.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Refer to the following list to configure access to Azure Resource Manager:

- [Assign access via Azure portal](howto-assign-access-portal.md)
- [Assign access via Powershell](howto-assign-access-powershell.md)
- [Assign access via Azure CLI](howto-assign-access-CLI.md)
- [Assign access via Azure Resource Manager template](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Dostupné |
| Azure Government | `https://management.usgovcloudapi.net/` | Dostupné |
| Azure (Německo) | `https://management.microsoftazure.de/` | Dostupné |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Dostupné |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Dostupné |
| Azure Government | `https://vault.usgovcloudapi.net` | Dostupné |
| Azure (Německo) |  `https://vault.microsoftazure.de` | Dostupné |
| Azure China 21Vianet | `https://vault.azure.cn` | Dostupné |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Dostupné |
| Azure Government |  | Není k dispozici |
| Azure (Německo) |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |

### <a name="azure-sql"></a>Azure SQL 

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Dostupné |
| Azure Government | `https://database.usgovcloudapi.net/` | Dostupné |
| Azure (Německo) | `https://database.cloudapi.de/` | Dostupné |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Dostupné |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Dostupné |
| Azure Government |  | Není k dispozici |
| Azure (Německo) |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Dostupné |
| Azure Government |  | Dostupné |
| Azure (Německo) |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobs and queues

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Dostupné |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Dostupné |
| Azure (Německo) | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Dostupné |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Dostupné |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Dostupné |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Dostupné |
| Azure (Německo) | `https://*.asazure.cloudapi.de` | Dostupné |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Dostupné |
