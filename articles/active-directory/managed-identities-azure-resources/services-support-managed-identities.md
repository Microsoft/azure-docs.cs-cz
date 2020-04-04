---
title: Služby Azure, které podporují spravované identity – Azure AD
description: Seznam služeb, které podporují spravované identity pro prostředky Azure a ověřování Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c7a65df100cd58561ce12ac2ae01281eebd419a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656046"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Služby, které podporují spravované identity pro prostředky Azure

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Pomocí spravované identity můžete ověřit na libovolné službě, která podporuje ověřování Azure AD bez nutnosti pověření ve vašem kódu. Právě integrujeme spravované identity pro prostředky Azure a ověřování Azure AD napříč Azure. Často kontrolujte aktualizace.

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Služby Azure, které podporují spravované identity pro prostředky Azure

Následující služby Azure podporují spravované identity pro prostředky Azure:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Typ spravované identity | Všechny obecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systém přiřazen | ![K dispozici][check] | ![K dispozici][check] | Preview | Preview | 
| Přiřazený uživatel | ![K dispozici][check] | ![K dispozici][check] | Preview | Preview |

V následujícím seznamu najdete konfiguraci spravované identity pro virtuální počítače Azure (v oblastech, kde je k dispozici):

- [portál Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Typ spravované identity | Všechny obecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systém přiřazen | ![K dispozici][check] | Preview | Preview | Preview |
| Přiřazený uživatel | ![K dispozici][check] | Preview | Preview | Preview |

Ke konfiguraci spravované identity pro škálovací sady virtuálních strojů Azure (v oblastech, kde je k dispozici), najdete v následujícím seznamu:

- [portál Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Typ spravované identity | Všechny obecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systém přiřazen | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] |
| Přiřazený uživatel | ![K dispozici][check] | ![K dispozici][check]  | ![K dispozici][check]  | ![K dispozici][check] |

Nawebu na následující seznam nakonfigurujte spravovanou identitu pro azure app službu (v oblastech, kde je k dispozici):

- [portál Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Typ spravované identity | Všechny obecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systém přiřazen | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici. | Není k dispozici. |
| Přiřazený uživatel | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici. | Není k dispozici. |

Použití spravované identity s [azure blueprinty](../../governance/blueprints/overview.md)naleznete v následujícím seznamu :

- [Azure portal – přiřazení podrobného plánu](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - přiřazení podrobného plánu](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Typ spravované identity |Všechny obecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systém přiřazen | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] |
| Přiřazený uživatel | ![K dispozici][check] | ![K dispozici][check]  | ![K dispozici][check]  | ![K dispozici][check]  |

Ke konfiguraci spravované identity pro funkce Azure (v oblastech, kde je k dispozici), najdete v následujícím seznamu:

- [portál Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ spravované identity | Všechny obecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systém přiřazen | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici. | ![K dispozici][check] |
| Přiřazený uživatel | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici. | ![K dispozici][check] |


Ke konfiguraci spravované identity pro Azure Logic Apps (v oblastech, kde je k dispozici), najdete v následujícím seznamu:

- [portál Azure](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Šablona Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ spravované identity | Všechny obecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systém přiřazen | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici. | ![K dispozici][check] |
| Přiřazený uživatel | Není k dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |

Nawebu spravovanou identitu pro Azure Data Factory V2 (v oblastech, kde je k dispozici), najdete v následujícím seznamu:

- [portál Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Sada SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Typ spravované identity | Všechny obecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systém přiřazen | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici. | ![K dispozici][check] |
| Přiřazený uživatel | Preview | Preview | Není k dispozici. | Preview |

Ke konfiguraci spravované identity pro Azure API Management (v oblastech, kde je k dispozici), najdete v následujícím seznamu:

- [Šablona Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Typ spravované identity | Všechny obecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systém přiřazen | Linux: Náhled<br>Windows: Není k dispozici | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| Přiřazený uživatel | Linux: Náhled<br>Windows: Není k dispozici | Není k dispozici. | Není k dispozici. | Není k dispozici. |

Odkazovat na následující seznam pro konfiguraci spravované identity pro instance kontejnerů Azure (v oblastech, kde je k dispozici):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Šablona Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Úlohy Azure Container Registry

Typ spravované identity | Všechny obecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systém přiřazen | ![K dispozici][check] | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| Přiřazený uživatel | Preview | Není k dispozici. | Není k dispozici. | Není k dispozici. |

V následujícím seznamu nakonfigurujte spravovanou identitu pro úlohy registru kontejnerů Azure (v oblastech, kde je k dispozici):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Spravovaná identita pro aplikace Service Fabric](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) je ve verzi Preview a je k dispozici ve všech oblastech.

Typ spravované identity | Všechny obecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systém přiřazen | ![K dispozici][check] | Není k dispozici | Není k dispozici | není k dispozici |
| Přiřazený uživatel | ![K dispozici][check] | Není k dispozici | Není k dispozici |Není k dispozici |

Na webu nakonfigurujete spravovanou identitu pro aplikace Azure Service Fabric ve všech oblastech:
- [Šablona Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Služby Azure, které podporují ověřování Azure AD

Následující služby podporují ověřování Azure AD a byly testovány s klientskými službami, které používají spravované identity pro prostředky Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Chcete-li nakonfigurovat přístup ke Správci prostředků Azure, najdete v následujícím seznamu:

- [Přiřazení přístupu přes portál Azure](howto-assign-access-portal.md)
- [Přiřazení přístupu přes PowerShell](howto-assign-access-powershell.md)
- [Přiřazení přístupu přes Azure CLI](howto-assign-access-CLI.md)
- [Přiřazení přístupu prostřednictvím šablony Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Azure Globální | `https://management.azure.com/`| ![K dispozici][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![K dispozici][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![K dispozici][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![K dispozici][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Azure Globální | `https://vault.azure.net`| ![K dispozici][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![K dispozici][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![K dispozici][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![K dispozici][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Azure Globální | `https://datalake.azure.net/` | ![K dispozici][check] |
| Azure Government |  | Není k dispozici |
| Azure Germany |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |

### <a name="azure-sql"></a>Azure SQL 

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Azure Globální | `https://database.windows.net/` | ![K dispozici][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![K dispozici][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![K dispozici][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![K dispozici][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Azure Globální | `https://eventhubs.azure.net` | ![K dispozici][check] |
| Azure Government |  | Není k dispozici |
| Azure Germany |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Azure Globální | `https://servicebus.azure.net`  | ![K dispozici][check] |
| Azure Government |  | ![K dispozici][check] |
| Azure Germany |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |









### <a name="azure-storage-blobs-and-queues"></a>Objekty BLOB a fronty azure storage

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Azure Globální | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![K dispozici][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![K dispozici][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![K dispozici][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![K dispozici][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Azure Globální | `https://*.asazure.windows.net` | ![K dispozici][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![K dispozici][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![K dispozici][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![K dispozici][check] |


[check]: media/services-support-managed-identities/check.png "K dispozici"
