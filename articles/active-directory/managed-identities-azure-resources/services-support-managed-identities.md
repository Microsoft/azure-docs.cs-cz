---
title: Služby Azure, které podporují spravované identity – Azure AD
description: Seznam služeb, které podporují spravované identity pro prostředky Azure a ověřování Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 05/12/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 550c025807b1ec3d0d5b8fb54f4b358c9f1ec6d3
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846031"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Služby, které podporují spravované identity prostředků Azure

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Pomocí spravované identity se můžete ověřit u jakékoli služby, která podporuje ověřování Azure AD, aniž byste museli mít přihlašovací údaje ve svém kódu. V procesu integruje spravované identity pro prostředky Azure a ověřování Azure AD v Azure. Aktualizace se často kontrolují.

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Služby Azure, které podporují spravované identity pro prostředky Azure

Spravované identity pro prostředky Azure podporují následující služby Azure:


### <a name="azure-api-management"></a>Azure API Management

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazeno uživatelem | Preview | Preview | Není k dispozici | Preview |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure API Management (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [Šablona Azure Resource Manageru](/azure/api-management/api-management-howto-use-managed-service-identity)


### <a name="azure-app-service"></a>Azure App Service

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] |
| Přiřazeno uživatelem | ![K dispozici][check] | ![K dispozici][check]  | ![K dispozici][check]  | ![K dispozici][check] |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure App Service (v oblastech, kde je dostupná), použijte následující seznam:

- [portál Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manageru](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)


### <a name="azure-blueprints"></a>Azure Blueprint

|Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | Není k dispozici |
| Přiřazeno uživatelem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | Není k dispozici |

Pokud chcete použít spravovanou identitu s podrobnými [plány Azure](../../governance/blueprints/overview.md), přečtěte si následující seznam:

- [Azure Portal – přiřazení podrobného plánu](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API – přiřazení podrobného plánu](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-container-instances"></a>Azure Container Instances

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | Linux: verze Preview<br>Windows: není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazeno uživatelem | Linux: verze Preview<br>Windows: není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Container Instances (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Šablona Azure Resource Manageru](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Úlohy Azure Container Registry

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazeno uživatelem | Preview | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro úlohy Azure Container Registry (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)


### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazeno uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Data Factory v2 (v oblastech, kde je dostupná), použijte následující seznam:

- [portál Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SADĚ](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)


### <a name="azure-functions"></a>Azure Functions

Typ spravované identity |Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] | ![K dispozici][check] |
| Přiřazeno uživatelem | ![K dispozici][check] | ![K dispozici][check]  | ![K dispozici][check]  | ![K dispozici][check]  |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Functions (v oblastech, kde je dostupná), použijte následující seznam:

- [portál Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manageru](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazeno uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Data Factory v2 (v oblastech, kde je dostupná), použijte následující seznam:

- [portál Azure](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | - | - | - | 
| Přiřazeno uživatelem | ![K dispozici][check] | - | - | - |


Další informace najdete v tématu [použití spravovaných identit ve službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/use-managed-identity).


### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |
| Přiřazeno uživatelem | ![K dispozici][check] | ![K dispozici][check] | Není k dispozici | ![K dispozici][check] |


Pokud chcete nakonfigurovat spravovanou identitu pro Azure Logic Apps (v oblastech, kde je dostupná), použijte následující seznam:

- [portál Azure](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Šablona Azure Resource Manageru](https://docs.microsoft.com/azure/logic-apps/logic-apps-azure-resource-manager-templates-overview)


### <a name="azure-service-fabric"></a>Azure Service Fabric
[Spravovaná identita pro aplikace Service Fabric](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) je ve verzi Preview a dostupná ve všech oblastech.

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | Není k dispozici | Není k dispozici | není k dispozici |
| Přiřazeno uživatelem | ![K dispozici][check] | Není k dispozici | Není k dispozici |Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro aplikace Service Fabric Azure ve všech oblastech, přečtěte si následující seznam:
- [Šablona Azure Resource Manageru](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)



### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | Preview | Preview | Preview |
| Přiřazeno uživatelem | ![K dispozici][check] | Preview | Preview | Preview |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Virtual Machine Scale Sets (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [portál Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Přiřazeno systémem | ![K dispozici][check] | ![K dispozici][check] | Preview | Preview | 
| Přiřazeno uživatelem | ![K dispozici][check] | ![K dispozici][check] | Preview | Preview |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Virtual Machines (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [portál Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)




## <a name="azure-services-that-support-azure-ad-authentication"></a>Služby Azure, které podporují ověřování Azure AD

Následující služby podporují ověřování Azure AD a byly testovány pomocí služeb klienta, které využívají spravované identity pro prostředky Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Pokud chcete nakonfigurovat přístup k Azure Resource Manager, přečtěte si následující seznam:

- [Přiřazení přístupu přes Azure Portal](howto-assign-access-portal.md)
- [Přiřazení přístupu přes PowerShell](howto-assign-access-powershell.md)
- [Přiřazení přístupu přes rozhraní příkazového řádku Azure](howto-assign-access-CLI.md)
- [Přiřazení přístupu prostřednictvím šablony Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://management.azure.com/`| ![K dispozici][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![K dispozici][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![K dispozici][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![K dispozici][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://vault.azure.net`| ![K dispozici][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![K dispozici][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![K dispozici][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![K dispozici][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://datalake.azure.net/` | ![K dispozici][check] |
| Azure Government |  | Není k dispozici |
| Azure Germany |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |

### <a name="azure-sql"></a>Azure SQL 

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://database.windows.net/` | ![K dispozici][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![K dispozici][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![K dispozici][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![K dispozici][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://eventhubs.azure.net` | ![K dispozici][check] |
| Azure Government |  | Není k dispozici |
| Azure Germany |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://servicebus.azure.net`  | ![K dispozici][check] |
| Azure Government |  | ![K dispozici][check] |
| Azure Germany |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage objekty BLOB a fronty

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![K dispozici][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![K dispozici][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![K dispozici][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![K dispozici][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID prostředku | Status |
|--------|------------|:-:|
| Globální Azure | `https://*.asazure.windows.net` | ![K dispozici][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![K dispozici][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![K dispozici][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![K dispozici][check] |

> [!Note]
> Microsoft Power BI také [podporuje spravované identity](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity).


[check]: media/services-support-managed-identities/check.png "K dispozici"
