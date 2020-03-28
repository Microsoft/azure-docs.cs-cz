---
title: PowerShell příklad aktivní geograficky replikační databáze Azure SQL Database
description: Ukázkový skript Azure PowerShellu pro nastavení aktivní geografické replikace pro jednu databázi v Azure SQL Database a převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: ddaf9de7fdb09f8c93815c918edac010555bb4d8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691527"
---
# <a name="use-powershell-to-configure-active-geo-replication-for-a-single-database-in-azure-sql-database"></a>Konfigurace aktivní geografické replikace pro jednu databázi v Azure SQL Database pomocí PowerShellu

Tento příklad skriptu prostředí PowerShell konfiguruje aktivní geografickou replikaci pro jednu databázi a převezme ji do sekundární repliky databáze.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat prostředí PowerShell místně, tento kurz vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-scripts"></a>Ukázkové skripty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-single-database.ps1?highlight=18-21 "Set up active geo-replication for single database")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní spojené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nový-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří databázový server SQL, který hostuje jednotlivé databáze a elastické fondy. |
| [Nový elastický fond AzSql](/powershell/module/az.sql/new-azsqlelasticpool) | Vytvoří elastický fond. |
| [Set-AzSqlDatabáze](/powershell/module/az.sql/set-azsqldatabase) | Aktualizuje vlastnosti databáze nebo přesune databázi do nebo z elastického fondu nebo mezi elastickými fondy. |
| [New-AzSqlDatabaseSekundární](/powershell/module/az.sql/new-azsqldatabasesecondary)| Vytvoří sekundární databázi pro existující databázi a spustí replikaci dat. |
| [Databáze Get-AzSql](/powershell/module/az.sql/get-azsqldatabase)| Získá jednu nebo více databází. |
| [Set-AzSqlDatabaseSekundární](/powershell/module/az.sql/set-azsqldatabasesecondary)| Přepne sekundární databázi na primární a zahájí tak převzetí služeb při selhání.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Získá vazby geografické replikace mezi službou Azure SQL Database a skupinou prostředků nebo SQL Serverem. |
| [Odebrat-AzSqlDatabaseSekundární](/powershell/module/az.sql/remove-azsqldatabasesecondary) | Ukončí replikaci dat mezi službou SQL Database a zadanou sekundární databází. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
