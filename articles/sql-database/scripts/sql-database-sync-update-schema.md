---
title: Příklad prostředí PowerShell – aktualizace schématu synchronizace dat SQL
description: Ukázkový skript Azure PowerShellu pro aktualizaci schématu synchronizace pro Synchronizaci dat SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 0106b80259083c6e5e3e527063a18aae2e7c6cee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74421616"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Aktualizace schématu synchronizace ve stávající skupině synchronizace pomocí PowerShellu

Tento příklad PowerShellu aktualizuje schéma synchronizace ve stávající skupině synchronizace v Synchronizaci dat SQL. Pokud synchronizujete několik tabulek, tento skript vám pomůže efektivně aktualizovat schéma synchronizace. Tento příklad ukazuje použití skriptu **UpdateSyncSchema**, který je k dispozici na GitHubu jako [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat prostředí PowerShell místně, tento kurz vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](../sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync v tuto chvíli nepodporuje spravovanou instanci azure SQL databáze.

## <a name="examples"></a>Příklady

### <a name="add-all-tables-to-the-sync-schema"></a>Přidání všech tabulek do schématu synchronizace

Následující příklad aktualizuje schéma databáze a přidá do schématu synchronizace všechny platné tabulky v databázi centra.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Přidání a odebrání tabulek a sloupců

Následující příklad do schématu synchronizace přidá `[dbo].[Table1]` a `[dbo].[Table2].[Column1]` a odebere z něj `[dbo].[Table3]`.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parametry skriptu

Skript **UpdateSyncSchema** obsahuje následující parametry:

| Parametr | Poznámky |
|---|---|
| $subscriptionId | Předplatné, ve kterém se skupina synchronizace vytvoří. |
| $resourceGroupName | Skupina prostředků, ve které se skupina synchronizace vytvoří.|
| $serverName | Název serveru s databází centra.|
| $databaseName | Název databáze centra. |
| $syncGroupName | Název skupiny synchronizace. |
| $memberName | Název člena zadejte, pokud chcete schéma databáze načíst z člena synchronizace, a ne z databáze centra. Pokud chcete schéma databáze načíst z centra, ponechte tento parametr prázdný. |
| $timeoutInSeconds | Časový limit aktualizace schématu databáze skriptem. Výchozí hodnota je 900 sekund. |
| $refreshDatabaseSchema | Určuje, jestli má skript aktualizovat schéma databáze. Pokud se schéma vaší databáze od předchozí konfigurace změnilo (například pokud jste přidali novou tabulku nebo nový sloupec), musíte schéma aktualizovat, abyste ho mohli znovu nakonfigurovat. Výchozí hodnota je false. |
| $addAllTables | Pokud je tato hodnota true, přidají se do schématu synchronizace všechny platné tabulky a sloupce. Hodnoty parametrů $TablesAndColumnsToAdd a $TablesAndColumnsToRemove se ignorují. |
| $tablesAndColumnsToAdd | Určuje tabulky nebo sloupce, které se mají přidat do schématu synchronizace. Názvy jednotlivých tabulek a sloupců musí být úplně oddělené názvem schématu. Příklad: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Je možné zadat více názvů tabulek nebo sloupců a oddělit je čárkami (,). |
| $tablesAndColumnsToRemove | Určuje tabulky nebo sloupce, které se mají odebrat ze schématu synchronizace. Názvy jednotlivých tabulek a sloupců musí být úplně oddělené názvem schématu. Příklad: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Je možné zadat více názvů tabulek nebo sloupců a oddělit je čárkami (,). |

## <a name="script-explanation"></a>Vysvětlení skriptu

Skript **UpdateSyncSchema** používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Vrátí informace o skupině synchronizace. |
| [Aktualizace-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Aktualizuje skupinu synchronizace. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Vrátí informace o členovi synchronizace. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Vrátí informace o schématu synchronizace. |
| [Aktualizace-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Aktualizuje schéma synchronizace. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky skriptů PowerShellu databáze SQL najdete ve [skriptech PowerShellu azure SQL database .](../sql-database-powershell-samples.md)

Další informace o Synchronizaci dat SQL:

- Přehled – [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Azure SQL Data Sync](../sql-database-sync-data.md)
- Nastavení synchronizace dat
    - Na portálu – [kurz: Nastavení synchronizace dat SQL pro synchronizaci dat mezi databází Azure SQL a SQL Server em i v místním prostředí](../sql-database-get-started-sql-data-sync.md)
    - S využitím PowerShellu
        - [Synchronizace mezi několika databázemi Azure SQL pomocí PowerShellu](sql-database-sync-data-between-sql-databases.md)
        - [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](sql-database-sync-data-between-azure-onprem.md)
- Agent synchronizace dat – [agent synchronizace dat pro synchronizaci dat Azure SQL](../sql-database-data-sync-agent.md)
- Doporučené postupy – [doporučené postupy pro Azure SQL Data Sync](../sql-database-best-practices-data-sync.md)
- Monitor – [monitorování synchronizace dat SQL pomocí protokolů Azure Monitoru](../sql-database-sync-monitor-oms.md)
- Poradce při potížích – [řešení problémů se synchronizací dat Azure SQL](../sql-database-troubleshoot-data-sync.md)
- Aktualizace schématu synchronizace
    - S Transact-SQL – [automatizace replikace změn schématu v Synchronizaci dat Azure SQL](../sql-database-update-sync-schema.md)

Další informace o službě SQL Database:

- [Přehled služby SQL Database](../sql-database-technical-overview.md)
- [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
