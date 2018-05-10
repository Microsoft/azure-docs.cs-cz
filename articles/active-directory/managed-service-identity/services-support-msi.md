---
title: Služby Azure, které podporují identita spravované služby
description: Seznam služeb, které podporují identita spravované služby a ověřování služby Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 03/28/2018
ms.topic: reference
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: ff56928748f536ffe1cfc30891585565e962d9fe
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="services-that-support-managed-service-identity"></a>Služby, které podporují identita spravované služby 

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Pomocí spravovaného identity, můžete provést ověření na jakoukoli službu, která podporuje ověřování Azure AD bez nutnosti přihlašovací údaje ve vašem kódu. Probíhá integrace MSI a Azure AD ověřování přes Azure. Zkontrolujte zpět často aktualizací.

## <a name="azure-services-that-support-managed-service-identity"></a>Služby Azure, které podporují identita spravované služby

Následující služby Azure podporují identita spravované služby.

| Služba | Status | Datum | Konfigurace | Získání tokenu |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Preview | 2017 září | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Šablony Azure Resource Manageru](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Preview | 2017 září | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Šablona Azure Resource Manageru](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | Preview | 2017 září | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Šablona Azure Resource Manageru](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Preview | 2017 listopadu | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[Sada SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure services, ověření podpory Azure AD

Následující služby podporují ověřování Azure AD a byly testovány s klienta služby, které používají identita spravované služby.

| Služba | ID prostředku | Status | Datum | Přiřadit přístup pro |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com | Dostupné | 2017 září | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Dostupné | 2017 září | |
| Azure Data Lake | https://datalake.azure.net | Dostupné | 2017 září | |
| Azure SQL | https://database.windows.net | Dostupné | Říjen 2017 | |
| Azure Event Hubs | https://eventhubs.azure.net | Dostupné | 2017 prosinec | |
| Azure Service Bus | https://servicebus.azure.net | Dostupné | 2017 prosinec | |