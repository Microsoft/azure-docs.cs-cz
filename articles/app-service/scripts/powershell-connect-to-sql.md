---
title: Skript Azure Powershellu ukázkový – připojení aplikace k SQL database | Dokumentace Microsoftu
description: Skript Azure Powershellu ukázkový – připojení k SQL database aplikaci služby App Service
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fc0046f16222fe20a7b11901690acccaae382a6c
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650116"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>Připojit aplikaci služby App Service do služby SQL database

V tomto scénáři se dozvíte, jak vytvořit databázi Azure SQL a aplikaci služby App Service. Pak do aplikace pomocí nastavení aplikace propojíte databázi SQL.

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzureRmAccount` vytvořte připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu slouží následující příkaz k odebrání skupiny prostředků, aplikace služby App Service a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Vytvoří plán služby App Service. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Vytvoří aplikaci služby App Service. |
| [New-AzureRMSQLServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Vytvoří server služby SQL Database. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro server služby SQL Database. |
| [New-AzureRMSQLDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Vytvoří databázi nebo elastickou databázi. |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Upraví konfiguraci aplikace služby App Service. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure Powershellu pro službu Azure App Service najdete v [ukázky Azure Powershellu](../samples-powershell.md).
