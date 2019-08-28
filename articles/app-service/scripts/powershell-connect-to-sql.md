---
title: Ukázka skriptu Azure PowerShell – připojení aplikace k databázi SQL | Microsoft Docs
description: Ukázka skriptu Azure PowerShell – připojení aplikace App Service k databázi SQL
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 799fb67b20c45653ec741a92ec7a75aff2f8c509
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087836"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>Připojení aplikace App Service k databázi SQL

V tomto scénáři se naučíte, jak vytvořit databázi SQL Azure a aplikaci App Service. Pak propojíte databázi SQL s aplikací pomocí nastavení aplikace.

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, App Service aplikaci a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Vytvoří plán služby App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří aplikaci App Service. |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server služby SQL Database. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro server služby SQL Database. |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří databázi nebo elastickou databázi. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Upraví konfiguraci aplikace App Service. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service najdete v ukázkách [Azure PowerShell](../samples-powershell.md).
