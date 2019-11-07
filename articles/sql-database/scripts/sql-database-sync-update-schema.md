---
title: Příklad PowerShellu – aktualizace schématu Synchronizace dat SQL synchronizace
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
ms.openlocfilehash: d8c4f21630afa4a57d3c3886819ec2842e3cb681
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691470"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Aktualizace schématu synchronizace ve stávající skupině synchronizace pomocí PowerShellu

Tento příklad PowerShellu aktualizuje schéma synchronizace ve stávající skupině synchronizace v Synchronizaci dat SQL. Pokud synchronizujete několik tabulek, tento skript vám pomůže efektivně aktualizovat schéma synchronizace. Tento příklad ukazuje použití skriptu **UpdateSyncSchema**, který je k dispozici na GitHubu jako [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](../sql-database-sync-data.md).

> [!IMPORTANT]
> Azure Synchronizace dat SQL v tuto **chvíli nepodporuje spravovanou** instanci Azure SQL Database.

## <a name="sample-script"></a>Ukázkový skript

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Příklad 1 – Přidání všech tabulek do schématu synchronizace

Následující příklad aktualizuje schéma databáze a přidá do schématu synchronizace všechny platné tabulky v databázi centra.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Příklad 2 – Přidání a odebrání tabulek a sloupců

Následující příklad do schématu synchronizace přidá `[dbo].[Table1]` a `[dbo].[Table2].[Column1]` a odebere z něj `[dbo].[Table3]`.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parametry skriptu

Skript **UpdateSyncSchema** obsahuje následující parametry:

| Parametr | Poznámky |
|---|---|
| $SubscriptionId | Předplatné, ve kterém se skupina synchronizace vytvoří. |
| $ResourceGroupName | Skupina prostředků, ve které se skupina synchronizace vytvoří.|
| $ServerName | Název serveru s databází centra.|
| $DatabaseName | Název databáze centra. |
| $SyncGroupName | Název skupiny synchronizace. |
| $MemberName | Název člena zadejte, pokud chcete schéma databáze načíst z člena synchronizace, a ne z databáze centra. Pokud chcete schéma databáze načíst z centra, ponechte tento parametr prázdný. |
| $TimeoutInSeconds | Časový limit aktualizace schématu databáze skriptem. Výchozí hodnota je 900 sekund. |
| $RefreshDatabaseSchema | Určuje, jestli má skript aktualizovat schéma databáze. Pokud se schéma vaší databáze od předchozí konfigurace změnilo (například pokud jste přidali novou tabulku nebo nový sloupec), musíte schéma aktualizovat, abyste ho mohli znovu nakonfigurovat. Výchozí hodnota je false. |
| $AddAllTables | Pokud je tato hodnota true, přidají se do schématu synchronizace všechny platné tabulky a sloupce. Hodnoty parametrů $TablesAndColumnsToAdd a $TablesAndColumnsToRemove se ignorují. |
| $TablesAndColumnsToAdd | Určuje tabulky nebo sloupce, které se mají přidat do schématu synchronizace. Názvy jednotlivých tabulek a sloupců musí být úplně oddělené názvem schématu. Příklad: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Je možné zadat více názvů tabulek nebo sloupců a oddělit je čárkami (,). |
| $TablesAndColumnsToRemove | Určuje tabulky nebo sloupce, které se mají odebrat ze schématu synchronizace. Názvy jednotlivých tabulek a sloupců musí být úplně oddělené názvem schématu. Příklad: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Je možné zadat více názvů tabulek nebo sloupců a oddělit je čárkami (,). |
|||

## <a name="script-explanation"></a>Vysvětlení skriptu

Skript **UpdateSyncSchema** používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Vrátí informace o skupině synchronizace. |
| [Update – AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Aktualizuje skupinu synchronizace. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Vrátí informace o členovi synchronizace. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Vrátí informace o schématu synchronizace. |
| [Update – AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Aktualizuje schéma synchronizace. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).

Další informace o Synchronizaci dat SQL:

-   Přehled – [synchronizace dat napříč několika cloudy a místními databázemi pomocí Azure synchronizace dat SQL](../sql-database-sync-data.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurz: nastavení synchronizace dat SQL pro synchronizaci dat mezi Azure SQL Database a SQL Server místním](../sql-database-get-started-sql-data-sync.md) prostředím
    - S využitím PowerShellu
        -  [Synchronizace mezi několika databázemi Azure SQL pomocí PowerShellu](sql-database-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizace dat – [Agent synchronizace dat pro Azure synchronizace dat SQL](../sql-database-data-sync-agent.md)
-   Osvědčené postupy – [osvědčené postupy pro Azure synchronizace dat SQL](../sql-database-best-practices-data-sync.md)
-   Monitorování – [monitorování synchronizace dat SQL pomocí protokolů Azure monitor](../sql-database-sync-monitor-oms.md)
-   Řešení potíží – [řešení potíží s Azure synchronizace dat SQL](../sql-database-troubleshoot-data-sync.md)
-   Aktualizace schématu synchronizace
    -   Pomocí jazyka Transact-SQL – [Automatizace replikace změn schématu v Azure synchronizace dat SQL](../sql-database-update-sync-schema.md)

Další informace o službě SQL Database:

-   [Přehled služby SQL Database](../sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
