---
title: Příklad PowerShellu – Obnovení databáze SQL Azure ze zálohy | Microsoft Docs
description: Ukázkový skript Azure Powershellu k obnovení jedné databáze Azure SQL z geograficky redundantní zálohy
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/04/2019
ms.openlocfilehash: 8974aeb327218e6108efc6e8b37008501fb42565
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336459"
---
# <a name="use-powershell-to-restore-an-azure-sql-single-database-from-backups"></a>Použití Powershellu k obnovení jedné databáze Azure SQL ze zálohy

Tento ukázkový skript PowerShellu obnoví databázi SQL Azure z geograficky redundantní zálohy, odstraněnou databázi SQL Azure z poslední zálohy a databázi SQL Azure ke konkrétnímu bodu v čase.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. | 
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server služby SQL Database, který je hostitelem elastický fond nebo izolovanou databázi. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří databázi na serveru SQL Database jako samostatný nebo databázi ve fondu. |
[Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Získá geograficky redundantní zálohy samostatný nebo databázi ve fondu. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Obnoví samostatné SQL nebo databázi ve fondu. |
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Odebere samostatné Azure SQL nebo databázi ve fondu. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Načte odstraněné samostatné nebo databázi ve fondu, že můžete provést obnovení. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
