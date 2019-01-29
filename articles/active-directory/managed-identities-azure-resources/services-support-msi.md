---
title: Služby Azure, které podporují spravované identity pro prostředky Azure
description: Seznam služeb, které podporují spravované identity pro prostředky Azure a ověřování Azure AD
services: active-directory
author: priyamohanram
ms.author: priyamo
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.openlocfilehash: 2ca70805a0806078613bb37ac1d2836dd8d626c8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180734"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Služby, které podporují spravované identity pro prostředky Azure

Spravované identity pro prostředky Azure poskytují služby Azure se automaticky spravované identity v Azure Active Directory. Pomocí spravované identity, můžete ověřit u jakékoli služby, který podporuje ověřování Azure AD bez nutnosti přihlašovací údaje ve vašem kódu. Připravujeme k integrační spravovaných identit pro prostředky Azure a ověřování Azure AD v Azure. Zkontrolujte back často aktualizací.

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Služby Azure, které podporují spravované identity pro prostředky Azure

Následující služby Azure podporují spravované identity pro prostředky Azure:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

|Typ identity spravované |  Obecně dostupné<br>Globálními oblastmi Azure | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | K dispozici. | Preview | Preview | Preview | Preview |
| Přiřazeno uživatelem | Preview | Preview | Preview | Preview | Preview

Najdete v následujícím seznamu ke konfiguraci spravované identity pro virtuální počítače Azure (v oblastech dostupné):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Typ identity spravované |  Obecně dostupné<br>Globálními oblastmi Azure | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | K dispozici. | Preview | Preview | Preview |
| Přiřazeno uživatelem | Preview | Preview | Preview | Preview

Najdete v následujícím seznamu ke konfiguraci spravované identity pro Škálovací sady virtuálních počítačů Azure (v oblastech dostupné):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

|Typ identity spravované |  Obecně dostupné<br>Globálními oblastmi Azure | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | K dispozici. | K dispozici. | K dispozici. | K dispozici. |
| Přiřazeno uživatelem | Preview | Není k dispozici. | Není k dispozici. | Není k dispozici.

Najdete v následujícím seznamu ke konfiguraci spravovaných identit pro službu Azure App Service (v oblastech dostupné):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manageru](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-functions"></a>Azure Functions

Typ identity spravované |  Obecně dostupné<br>Globálními oblastmi Azure | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | K dispozici. | K dispozici. | K dispozici. | K dispozici. |
| Přiřazeno uživatelem | Preview | Není k dispozici. | Není k dispozici. | Není k dispozici.

Najdete v následujícím seznamu ke konfiguraci spravovaných identit pro službu Azure Functions (v oblastech dostupné):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Šablona Azure Resource Manageru](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Typ identity spravované |  Obecně dostupné<br>Globálními oblastmi Azure | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | K dispozici. | K dispozici. | K dispozici. | K dispozici. |
| Přiřazeno uživatelem | Není k dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici.

Najdete v následujícím seznamu ke konfiguraci spravované identity Azure Logic Apps (v oblastech dostupné):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Šablona Azure Resource Manageru](/azure/app-service/overview-managed-identity#deployment-template)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Typ identity spravované |  Obecně dostupné<br>Globálními oblastmi Azure | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | K dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| Přiřazeno uživatelem | Není k dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici.

Najdete v následujícím seznamu ke konfiguraci spravovaných identit pro Azure Data Factory verze 2 (v oblastech dostupné):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)
- [Sada SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Typ identity spravované |  Obecně dostupné<br>Globálními oblastmi Azure | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | K dispozici. | K dispozici. | Není k dispozici. | Není k dispozici. |
| Přiřazeno uživatelem | Není k dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici.

Najdete v následujícím seznamu ke konfiguraci spravovaných identit pro službu Azure API Management (v oblastech dostupné):

- [Šablona Azure Resource Manageru](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Typ identity spravované |  Obecně dostupné<br>Globálními oblastmi Azure | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Přiřazeno systémem | Linux: Preview<br>Windows: Není k dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici. |
| Přiřazeno uživatelem | Linux: Preview<br>Windows: Není k dispozici. | Není k dispozici. | Není k dispozici. | Není k dispozici.

Najdete v následujícím seznamu ke konfiguraci spravovaných identit pro Azure Container Instances (v oblastech dostupné):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Šablona Azure Resource Manageru](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


## <a name="azure-services-that-support-azure-ad-authentication"></a>Podpora služby Azure AD ověření služby Azure

Tyto služby podporují ověřování Azure AD a prošel testováním s využitím klientské služby, které používají spravované identity pro prostředky Azure.

| Služba | ID prostředku | Status | Přiřazení přístupu |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | K dispozici. | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) <br>[Šablona Azure Resource Manageru](../../role-based-access-control/role-assignments-template.md) |
| Azure Key Vault | `https://vault.azure.net` | K dispozici. |  
| Azure Data Lake | `https://datalake.azure.net/` | K dispozici. |
| Azure SQL | `https://database.windows.net/` | K dispozici. |
| Azure Event Hubs | `https://eventhubs.azure.net` | Preview |
| Azure Service Bus | `https://servicebus.azure.net` | Preview |
| Azure Storage | `https://storage.azure.com/` | Preview |
