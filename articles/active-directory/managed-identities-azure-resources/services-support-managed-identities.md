---
title: Služby Azure, které podporují spravované identity – Azure AD
description: Seznam služeb, které podporují spravované identity pro prostředky Azure a ověřování Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b91d3bdf2ba4b6b30e7b3d5b748fd90921e2025
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841161"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Služby, které podporují spravované identity prostředků Azure

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Pomocí spravované identity se můžete ověřit u jakékoli služby, která podporuje ověřování Azure AD, aniž byste museli mít přihlašovací údaje ve svém kódu. V procesu integruje spravované identity pro prostředky Azure a ověřování Azure AD v Azure. Aktualizace se často kontrolují.

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Služby Azure, které podporují spravované identity pro prostředky Azure

Spravované identity pro prostředky Azure podporují následující služby Azure:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Dostupné | Preview | Preview | Preview | 
| Přiřazeno uživatelem | Dostupné | Preview | Preview | Preview |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Virtual Machines (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets Azure

|Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Dostupné | Preview | Preview | Preview |
| Přiřazeno uživatelem | Dostupné | Preview | Preview | Preview |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Virtual Machine Scale Sets (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Dostupné | Dostupné | Dostupné | Dostupné |
| Přiřazeno uživatelem | Dostupné | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure App Service (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manageru](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Dostupné | Dostupné | Není k dispozici | Není k dispozici |
| Přiřazeno uživatelem | Dostupné | Dostupné | Není k dispozici | Není k dispozici |

Pokud chcete použít spravovanou identitu s podrobnými [plány Azure](../../governance/blueprints/overview.md), přečtěte si následující seznam:

- [Azure Portal – přiřazení podrobného plánu](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API – přiřazení podrobného plánu](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funkce Azure

Typ spravované identity |Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Dostupné | Dostupné | Dostupné | Dostupné |
| Přiřazeno uživatelem | Dostupné | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Functions (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manageru](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Preview | Preview | Není k dispozici | Preview |
| Přiřazeno uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Logic Apps (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Šablona Azure Resource Manageru](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Dostupné | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazeno uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Data Factory v2 (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Sada SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Dostupné | Dostupné | Není k dispozici | Není k dispozici |
| Přiřazeno uživatelem | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure API Management (v oblastech, kde je dostupná), podívejte se na následující seznam:

- [Šablona Azure Resource Manageru](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Linux: verze Preview<br>Windows: není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazeno uživatelem | Linux: verze Preview<br>Windows: není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro Azure Container Instances (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Šablona Azure Resource Manageru](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Úlohy Azure Container Registry

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Dostupné | Není k dispozici | Není k dispozici | Není k dispozici |
| Přiřazeno uživatelem | Preview | Není k dispozici | Není k dispozici | Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro úlohy Azure Container Registry (v oblastech, kde je dostupná), použijte následující seznam:

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Spravovaná identita pro aplikace Service Fabric](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) je ve verzi Preview a dostupná ve všech oblastech.

Typ spravované identity | Vše je všeobecně dostupné<br>Globální oblasti Azure | Azure Government | Azure (Německo) | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Dostupné | Není k dispozici | Není k dispozici | není k dispozici |
| Přiřazeno uživatelem | Dostupné | Není k dispozici | Není k dispozici |Není k dispozici |

Pokud chcete nakonfigurovat spravovanou identitu pro aplikace Service Fabric Azure ve všech oblastech, přečtěte si následující seznam:
- [Šablona Azure Resource Manageru](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Služby Azure, které podporují ověřování Azure AD

Následující služby podporují ověřování Azure AD a byly testovány pomocí služeb klienta, které využívají spravované identity pro prostředky Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Pokud chcete nakonfigurovat přístup k Azure Resource Manager, přečtěte si následující seznam:

- [Přiřazení přístupu přes Azure Portal](howto-assign-access-portal.md)
- [Přiřazení přístupu přes PowerShell](howto-assign-access-powershell.md)
- [Přiřazení přístupu přes rozhraní příkazového řádku Azure](howto-assign-access-CLI.md)
- [Přiřazení přístupu prostřednictvím šablony Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Globální Azure | `https://management.azure.com/`| Dostupné |
| Azure Government | `https://management.usgovcloudapi.net/` | Dostupné |
| Azure (Německo) | `https://management.microsoftazure.de/` | Dostupné |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Dostupné |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Globální Azure | `https://vault.azure.net`| Dostupné |
| Azure Government | `https://vault.usgovcloudapi.net` | Dostupné |
| Azure (Německo) |  `https://vault.microsoftazure.de` | Dostupné |
| Azure China 21Vianet | `https://vault.azure.cn` | Dostupné |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Globální Azure | `https://datalake.azure.net/` | Dostupné |
| Azure Government |  | Není k dispozici |
| Azure (Německo) |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |

### <a name="azure-sql"></a>Azure SQL 

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Globální Azure | `https://database.windows.net/` | Dostupné |
| Azure Government | `https://database.usgovcloudapi.net/` | Dostupné |
| Azure (Německo) | `https://database.cloudapi.de/` | Dostupné |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Dostupné |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Globální Azure | `https://eventhubs.azure.net` | Dostupné |
| Azure Government |  | Není k dispozici |
| Azure (Německo) |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Globální Azure | `https://servicebus.azure.net`  | Dostupné |
| Azure Government |  | Dostupné |
| Azure (Německo) |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage objekty BLOB a fronty

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Globální Azure | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Dostupné |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Dostupné |
| Azure (Německo) | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Dostupné |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Dostupné |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID prostředku | Stav |
|--------|------------|--------|
| Globální Azure | `https://*.asazure.windows.net` | Dostupné |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Dostupné |
| Azure (Německo) | `https://*.asazure.cloudapi.de` | Dostupné |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Dostupné |
