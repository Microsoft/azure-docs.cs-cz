---
title: Služby Azure, které podporují spravované identity pro prostředky Azure
description: Seznam služeb, které podporují spravované identity pro prostředky Azure a ověřování Azure AD
services: active-directory
author: MarkusVi
ms.author: priyamo
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c67f1dad55d5d8710a997394e247793943cd21
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293506"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Služby, které podporují spravované identity pro prostředky Azure

Spravované identity pro prostředky Azure poskytují služby Azure se automaticky spravované identity v Azure Active Directory. Pomocí spravované identity, můžete ověřit u jakékoli služby, který podporuje ověřování Azure AD bez nutnosti přihlašovací údaje ve vašem kódu. Připravujeme k integrační spravovaných identit pro prostředky Azure a ověřování Azure AD v Azure. Zkontrolujte back často aktualizací.

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Služby Azure, které podporují spravované identity pro prostředky Azure

Následující služby Azure podporují spravované identity pro prostředky Azure:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Typ identity spravované | Obecně dostupné<br>Globálními oblastmi Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazenou systémem | K dispozici | Náhled | Náhled | Náhled | 
| Přiřazené uživateli | Náhled | Náhled | Náhled | Náhled |

Najdete v následujícím seznamu ke konfiguraci spravované identity pro virtuální počítače Azure (v oblastech dostupné):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Typ identity spravované | Obecně dostupné<br>Globálními oblastmi Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazenou systémem | K dispozici | Náhled | Náhled | Náhled |
| Přiřazené uživateli | Náhled | Náhled | Náhled | Náhled |

Najdete v následujícím seznamu ke konfiguraci spravované identity pro Škálovací sady virtuálních počítačů Azure (v oblastech dostupné):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Typ identity spravované | Obecně dostupné<br>Globálními oblastmi Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazenou systémem | K dispozici | K dispozici | K dispozici | K dispozici |
| Přiřazené uživateli | Náhled | Není k dispozici. | Není k dispozici. | Není k dispozici. |

Najdete v následujícím seznamu ke konfiguraci spravovaných identit pro službu Azure App Service (v oblastech dostupné):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Typ identity spravované | Obecně dostupné<br>Globálními oblastmi Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazenou systémem | Náhled | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| Přiřazené uživateli | Náhled | Není k dispozici. | Není k dispozici. | Není k dispozici. |

Přečtěte si následující seznam, aby používal spravovanou identitu s [plány Azure](../../governance/blueprints/overview.md):

- [Azure portal – přiřazení podrobného plánu](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [Rozhraní REST API – přiřazení podrobného plánu](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Typ identity spravované |Obecně dostupné<br>Globálními oblastmi Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazenou systémem | K dispozici | K dispozici | K dispozici | K dispozici |
| Přiřazené uživateli | Náhled | Není k dispozici. | Není k dispozici. | Není k dispozici. |

Najdete v následujícím seznamu ke konfiguraci spravovaných identit pro službu Azure Functions (v oblastech dostupné):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ identity spravované | Obecně dostupné<br>Globálními oblastmi Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazenou systémem | Náhled | Náhled | Není k dispozici. | Náhled |
| Přiřazené uživateli | Není k dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |

Najdete v následujícím seznamu ke konfiguraci spravované identity Azure Logic Apps (v oblastech dostupné):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Šablona Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ identity spravované | Obecně dostupné<br>Globálními oblastmi Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazenou systémem | K dispozici | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| Přiřazené uživateli | Není k dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |

Najdete v následujícím seznamu ke konfiguraci spravovaných identit pro Azure Data Factory verze 2 (v oblastech dostupné):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Sada SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Typ identity spravované | Obecně dostupné<br>Globálními oblastmi Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazenou systémem | K dispozici | K dispozici | Není k dispozici. | Není k dispozici. |
| Přiřazené uživateli | Není k dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |

Najdete v následujícím seznamu ke konfiguraci spravovaných identit pro službu Azure API Management (v oblastech dostupné):

- [Šablona Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Typ identity spravované | Obecně dostupné<br>Globálními oblastmi Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazenou systémem | Linux: Náhled<br>Windows: Není k dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| Přiřazené uživateli | Linux: Náhled<br>Windows: Není k dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |

Najdete v následujícím seznamu ke konfiguraci spravovaných identit pro Azure Container Instances (v oblastech dostupné):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Šablona Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Úlohy Azure Container Registry

Typ identity spravované | Obecně dostupné<br>Globálními oblastmi Azure | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Přiřazenou systémem | K dispozici | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| Přiřazené uživateli | Náhled | Není k dispozici. | Není k dispozici. | Není k dispozici. |

Najdete v následujícím seznamu ke konfiguraci spravovaných identit pro Azure Container Registry úlohy (v oblastech dostupné):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Podpora služby Azure AD ověření služby Azure

Tyto služby podporují ověřování Azure AD a prošel testováním s využitím klientské služby, které používají spravované identity pro prostředky Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Najdete v následujícím seznamu můžete nakonfigurovat přístup k Azure Resource Manageru:

- [Přiřazení přístupu prostřednictvím webu Azure portal](howto-assign-access-portal.md)
- [Přiřadit přístup přes Powershell](howto-assign-access-powershell.md)
- [Přiřadit přístup přes rozhraní příkazového řádku Azure](howto-assign-access-CLI.md)
- [Přiřazení přístupu pomocí šablony Azure Resource Manageru](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID prostředku | Status |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| K dispozici |
| Azure Government | `https://management.usgovcloudapi.net/` | K dispozici |
| Azure Germany | `https://management.microsoftazure.de/` | K dispozici |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | K dispozici |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | ID prostředku | Status |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| K dispozici |
| Azure Government | `https://vault.usgovcloudapi.net` | K dispozici |
| Azure Germany |  `https://vault.microsoftazure.de` | K dispozici |
| Azure China 21Vianet | `https://vault.azure.cn` | K dispozici |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | ID prostředku | Status |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | K dispozici |
| Azure Government |  | Není k dispozici |
| Azure Germany |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |

### <a name="azure-sql"></a>Azure SQL 

| Cloud | ID prostředku | Status |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | K dispozici |
| Azure Government | `https://database.usgovcloudapi.net/` | K dispozici |
| Azure Germany | `https://database.cloudapi.de/` | K dispozici |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | K dispozici |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | ID prostředku | Status |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Náhled |
| Azure Government |  | Není k dispozici |
| Azure Germany |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | ID prostředku | Status |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Náhled |
| Azure Government |  | Není k dispozici |
| Azure Germany |   | Není k dispozici |
| Azure China 21Vianet |  | Není k dispozici |

### <a name="azure-storage-blobs-and-queues"></a>Azure Storage BLOB a fronty

| Cloud | ID prostředku | Status |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` | K dispozici |
| Azure Government | `https://storage.azure.com/` | K dispozici |
| Azure Germany | `https://storage.azure.com/` | K dispozici |
| Azure China 21Vianet | `https://storage.azure.com/` | K dispozici |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID prostředku | Status |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | K dispozici |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | K dispozici |
| Azure Germany | `https://*.asazure.cloudapi.de` | K dispozici |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | K dispozici |
