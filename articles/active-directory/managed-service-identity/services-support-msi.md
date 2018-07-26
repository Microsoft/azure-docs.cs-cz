---
title: Služby Azure, které podporují spravované Identity služby
description: Seznam služeb, které podporují identita spravované služby a ověřování Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: f63832723a2c33b88d0e5fc9c6a38a0cad63fa38
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259173"
---
# <a name="services-that-support-managed-service-identity"></a>Služby, které podporují identita spravované služby 

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Pomocí spravované identity, můžete ověřit u jakékoli služby, který podporuje ověřování Azure AD bez nutnosti přihlašovací údaje ve vašem kódu. Připravujeme k integraci ověřování Identity spravované služby a služby Azure AD v Azure. Zkontrolujte back často aktualizací.

## <a name="azure-services-that-support-managed-service-identity"></a>Služby Azure, které podporují spravované Identity služby

Následující služby Azure podporují identita spravované služby.

| Služba | Stav přiřazenou systémem | Uživatel přiřazený stav| Konfigurace | Získání tokenu |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Preview | Preview | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash nebo nástroj Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure Virtual Machine Scale Sets | Preview | Preview | [Azure Portal](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Azure CLI](qs-configure-cli-windows-vmss.md)<br>[Šablony Azure Resource Manageru](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash nebo nástroj Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure App Service | K dispozici. | Není k dispozici. | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Šablona Azure Resource Manageru](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure Functions | K dispozici. | Není k dispozici. | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Šablona Azure Resource Manageru](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | Preview | Není k dispozici. | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[Sada SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |


## <a name="azure-services-that-support-azure-ad-authentication"></a>Podpora služby Azure AD ověření služby Azure

Tyto služby podporují ověřování Azure AD a prošel testováním s využitím klientské služby, které používají identitu spravované služby.

| Služba | ID prostředku | Status | Datum | Přiřazení přístupu |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | K dispozici. | Září 2017 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | K dispozici. | Září 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | K dispozici. | Září 2017 | |
| Azure SQL | https://database.windows.net/ | K dispozici. | Říjen 2017 | |
| Azure Event Hubs | https://eventhubs.azure.net | K dispozici. | . Prosince 2017 | |
| Azure Service Bus | https://servicebus.azure.net | K dispozici. | . Prosince 2017 | |
| Azure Storage | https://storage.azure.com/ | Preview | Květen 2018 | |
