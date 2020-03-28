---
title: 'PowerShell: Připojení k databázi SQL'
description: Zjistěte, jak pomocí Azure PowerShellu automatizovat nasazení a správu služby App Service. Tato ukázka ukazuje, jak připojit aplikaci k databázi SQL.
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: bb5fc8bcc99a4439276f53325c029635143c86c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74685388"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>Připojení aplikace Služby App Service k databázi SQL

V tomto scénáři se dozvíte, jak vytvořit databázi Azure SQL a aplikaci app service. Pak propojíte databázi SQL s aplikací pomocí nastavení aplikace.

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázky skriptu lze následující příkaz použít k odebrání skupiny prostředků, aplikace App Service a všech souvisejících prostředků.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nový-AzAppServicePlán](/powershell/module/az.websites/new-azappserviceplan) | Vytvoří plán služby App Service. |
| [Nový-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří aplikaci služby App Service. |
| [Nový-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server služby SQL Database. |
| [Nové pravidlo azsqlserverfirewallfirewall](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro server služby SQL Database. |
| [Nová databáze AzSQL](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří databázi nebo elastickou databázi. |
| [Sada-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Upravuje konfiguraci aplikace služby App Service. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure Powershellu pro Azure App Service najdete ve [ukázkách Azure PowerShellu](../samples-powershell.md).
