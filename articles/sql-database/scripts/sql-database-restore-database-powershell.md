---
title: Příklad PowerShellu – Obnovení databáze SQL Azure ze zálohy | Microsoft Docs
description: Ukázkový skript Azure PowerShellu pro obnovení databáze SQL Azure z geograficky redundantní zálohy
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: craigg
editor: carlrab
tags: azure-service-management
ms.assetid: ''
ms.service: sql-database
ms.custom: business continuity, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 789f5df60024651731b7ba34e1c6fdc53a4b9f2b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366660"
---
# <a name="use-powershell-to-restore-an-azure-sql-database-from-backups"></a>Obnovení databáze SQL Azure ze zálohy pomocí PowerShellu

Tento ukázkový skript PowerShellu obnoví databázi SQL Azure z geograficky redundantní zálohy, odstraněnou databázi SQL Azure z poslední zálohy a databázi SQL Azure ke konkrétnímu bodu v čase.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. | [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Vytvoří logický server, který je hostitelem databáze nebo elastického fondu. | 
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Vytvoří databázi na logickém serveru jako izolovanou databázi nebo databázi ve fondu. |
[Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) | Získá geograficky redundantní zálohu databáze. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) | Obnoví databázi SQL. |
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase) | Odebere databázi SQL Azure. |
| [Get-AzureRmSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Získá odstraněnou databázi, kterou můžete obnovit. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
