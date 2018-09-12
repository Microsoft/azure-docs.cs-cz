---
title: Příklad PowerShellu – Aktualizace schématu synchronizace pro Synchronizaci dat SQL | Microsoft Docs
description: Ukázkový skript Azure PowerShellu pro aktualizaci schématu synchronizace pro Synchronizaci dat SQL
services: sql-database
documentationcenter: sql-database
author: allenwux
manager: craigg
editor: ''
tags: ''
ms.assetid: ''
ms.service: sql-database
ms.custom: load & move data, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 01/10/2018
ms.author: xiwu
ms.reviewer: douglasl
ms.openlocfilehash: 7a913b2e55c681d0905db76eb0f0e0e99baf3142
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054651"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Aktualizace schématu synchronizace ve stávající skupině synchronizace pomocí PowerShellu

Tento příklad PowerShellu aktualizuje schéma synchronizace ve stávající skupině synchronizace v Synchronizaci dat SQL. Pokud synchronizujete několik tabulek, tento skript vám pomůže efektivně aktualizovat schéma synchronizace. Tento příklad ukazuje použití skriptu **UpdateSyncSchema**, který je k dispozici na GitHubu jako [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.


Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](../sql-database-sync-data.md).

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
| [Get-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncgroup) | Vrátí informace o skupině synchronizace. |
| [Update-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncgroup) | Aktualizuje skupinu synchronizace. |
| [Get-AzureRmSqlSyncMember](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncmember) | Vrátí informace o členovi synchronizace. |
| [Get-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncschema) | Vrátí informace o schématu synchronizace. |
| [Update-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncschema) | Aktualizuje schéma synchronizace. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).

Další informace o Synchronizaci dat SQL:

-   [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](../sql-database-sync-data.md)
-   [Nastavení Synchronizace dat SQL Azure](../sql-database-get-started-sql-data-sync.md)
-   [Osvědčené postupy pro Synchronizaci dat SQL Azure](../sql-database-best-practices-data-sync.md)
-   [Monitorování Synchronizace dat SQL Azure s využitím Log Analytics](../sql-database-sync-monitor-oms.md)
-   [Řešení potíží se Synchronizací dat SQL Azure](../sql-database-troubleshoot-data-sync.md)

-   Úplné příklady PowerShellu ukazující konfiguraci Synchronizace dat SQL:
    -   [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](sql-database-sync-data-between-sql-databases.md)
    -   [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](sql-database-sync-data-between-azure-onprem.md)

Další informace o službě SQL Database:

-   [Přehled služby SQL Database](../sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
