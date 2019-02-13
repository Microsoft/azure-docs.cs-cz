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
ms.openlocfilehash: d0c38b69d8cb2fda85a85fd27f0a5b26da51b699
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106201"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>Připojit aplikaci služby App Service do služby SQL database

V tomto scénáři se dozvíte, jak vytvořit databázi Azure SQL a aplikaci služby App Service. Pak do aplikace pomocí nastavení aplikace propojíte databázi SQL.

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu slouží následující příkaz k odebrání skupiny prostředků, aplikace služby App Service a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Vytvoří plán služby App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří aplikaci služby App Service. |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server služby SQL Database. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro server služby SQL Database. |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří databázi nebo elastickou databázi. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Upraví konfiguraci aplikace služby App Service. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure Powershellu pro službu Azure App Service najdete v [ukázky Azure Powershellu](../samples-powershell.md).
