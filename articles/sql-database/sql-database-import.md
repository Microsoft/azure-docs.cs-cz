---
title: Import souboru BACPAC k vytvoření databáze
description: Vytvořte novou databázi Azure SQL importem souboru BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 4fe8eb0b363fc95ac5913e66884a2ed8f3d225a6
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086483"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Úvodní příručka: Import souboru BACPAC do databáze v Azure SQL Database

Databázi serveru SQL Server můžete importovat do databáze v Azure SQL Database pomocí souboru [BACPAC.](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) Data můžete importovat `BACPAC` ze souboru uloženého v úložišti objektů Blob Azure (pouze standardní úložiště) nebo z místního úložiště v místním umístění. Chcete-li maximalizovat rychlost importu tím, že poskytuje více a rychlejší prostředky, škálování databáze na vyšší úroveň služby a výpočetní velikost během procesu importu. Po úspěšném importu pak můžete vertikálně snížit kapacitu.

> [!NOTE]
> Úroveň kompatibility importované databáze je založena na úrovni kompatibility zdrojové databáze.

> [!IMPORTANT]
> Po importu databáze můžete databázi provozovat na aktuální úrovni kompatibility (úroveň 100 pro databázi AdventureWorks2008R2) nebo na vyšší úrovni. Další informace o důsledcích a možnostech provozu databáze na konkrétní úrovni kompatibility najdete v tématu [Úroveň kompatibility ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). V tématu věnovaném příkazu [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) najdete také informace o dalších nastaveních na úrovni databáze souvisejících s úrovněmi kompatibility.

## <a name="using-azure-portal"></a>Pomocí webu Azure Portal

V tomto videu se podívejte, jak importovat ze souboru BACPAC na webu Azure Portal nebo pokračovat ve čtení níže:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Portál Azure](https://portal.azure.com) podporuje *jenom* vytváření jedné databáze v Azure SQL Database a *jenom* ze souboru BACPAC uloženého v úložišti objektů blob Azure.

Migrace databáze do [spravované instance](sql-database-managed-instance.md) ze souboru BACPAC pomocí Azure PowerShellu není aktuálně podporována. Místo toho použijte SQL Server Management Studio nebo SQLPackage.

> [!NOTE]
> Počítače zpracovávající požadavky na import a export odeslané prostřednictvím portálu Azure nebo prostředí PowerShell musí ukládat soubor BACPAC a také dočasné soubory generované architekturou datových vrstev (DacFX). Požadované místo na disku se v databázích se stejnou velikostí značně liší a může vyžadovat až trojnásobek velikosti databáze. Počítače s požadavkem na import a export mají pouze 450 GB místního místa na disku. V důsledku toho může dojít k `There is not enough space on the disk`selhání některých požadavků s chybou . V tomto případě je řešení spustit sqlpackage.exe v počítači s dostatkem místa na disku. Doporučujeme používat SqlPackage importovat a exportovat databáze větší než 150 GB, aby se zabránilo tomuto problému.

1. Chcete-li importovat ze souboru BACPAC do nové jediné databáze pomocí portálu Azure, otevřete příslušnou stránku databázového serveru a potom na panelu nástrojů vyberte **Importovat databázi**.  

   ![Import databáze1](./media/sql-database-import/sql-server-import-database.png)

1. Vyberte účet úložiště a kontejner pro soubor BACPAC a pak vyberte soubor BACPAC, ze kterého chcete importovat.

1. Zadejte novou velikost databáze (obvykle stejnou jako počátek) a zadejte cílové přihlašovací údaje serveru SQL Server. Seznam možných hodnot pro novou databázi Azure SQL najdete v tématu [Vytvoření databáze](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Import databáze2](./media/sql-database-import/sql-server-import-database-settings.png)

1. Klikněte na tlačítko **OK**.

1. Chcete-li sledovat průběh importu, otevřete stránku serveru databáze a v části **Nastavení**vyberte **položku Import/Export historie**. V případě úspěchu má import stav **Dokončeno.**

   ![Stav importu databáze](./media/sql-database-import/sql-server-import-database-history.png)

1. Chcete-li ověřit, zda je databáze na databázovém serveru aktivní, vyberte **databáze SQL** a ověřte, zda je nová databáze **online**.

## <a name="using-sqlpackage"></a>Použití balíčku SqlPackage

Informace o importu databáze serveru SQL Server pomocí nástroje příkazového řádku [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) naleznete [v tématu import parametrů a vlastností](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). [Sql Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Datové nástroje pro Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) patří SqlPackage. Můžete si také stáhnout nejnovější [sqlpackage](https://www.microsoft.com/download/details.aspx?id=53876) ze služby Stažení softwaru společnosti Microsoft.

Pro škálování a výkon doporučujeme použít SqlPackage ve většině produkčních prostředí, nikoli pomocí portálu Azure. Blog týmu zákaznického zpravodaje serveru SQL `BACPAC` Server o migraci pomocí souborů najdete v [tématu migrace z SQL Serveru do databáze Azure SQL pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Následující příkaz SqlPackage importuje databázi **AdventureWorks2008R2** z místního úložiště na server Azure SQL Database s názvem **mynewserver20170403**. Vytvoří novou databázi s názvem **myMigratedDatabase** s úrovní služby **Premium** a cílem služby **P6.** Změňte tyto hodnoty podle potřeby pro vaše prostředí.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Chcete-li se připojit k serveru SQL Database, který spravuje jednu databázi zpoza podnikové brány firewall, musí být brána firewall otevřena jako port 1433. Chcete-li se připojit ke spravované instanci, musíte mít [připojení bodu k síti](sql-database-managed-instance-configure-p2s.md) nebo připojení k expresní trase.

Tento příklad ukazuje, jak importovat databázi pomocí balíčku SqlPackage pomocí univerzálního ověřování služby Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Pomocí prostředí PowerShell

> [!NOTE]
> [Spravovaná instance](sql-database-managed-instance.md) aktuálně nepodporuje migraci databáze do databáze instance ze souboru BACPAC pomocí Azure PowerShellu. Chcete-li importovat do spravované instance, použijte SQL Server Management Studio nebo SQLPackage.

> [!NOTE]
> Stroje zpracovávající požadavky na import a export odeslané prostřednictvím portálu nebo powershellu musí ukládat soubor bacpac i dočasné soubory generované architekturou DacFX (Data-Tier Application Framework). Požadované místo na disku se výrazně liší mezi disky DB se stejnou velikostí a může trvat až 3 krát velikosti databáze. Počítače s požadavkem na import a export mají pouze 450 GB místního místa na disku. V důsledku toho může dojít k selhání některých požadavků s chybou "Na disku není dostatek místa". V tomto případě je řešení spustit sqlpackage.exe v počítači s dostatkem místa na disku. Při importu a exportu databází větších než 150 GB se tomuto problému vyhnete pomocí balíčku SqlPackage.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Modul Správce prostředků Azure (RM) prostředí PowerShell je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Modul AzureRM bude nadále dostávat opravy chyb nejméně do prosince 2020.  Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Další informace o jejich kompatibilitě [najdete v tématu Představení nového modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Pomocí rutiny [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) odešlete požadavek na databázi importu do služby Azure SQL Database. V závislosti na velikosti databáze může dokončení importu nějakou dobu trvat.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Rutina [Get-AzSqlDatabaseExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) můžete použít ke kontrole průběhu importu. Spuštění rutiny ihned po požadavku `Status: InProgress`se obvykle vrátí . Import je dokončen, `Status: Succeeded`když se zobrazí .

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [az-sql-db-import](/cli/azure/sql/db#az-sql-db-import) odešlete požadavek na databázi importu do služby Azure SQL Database. V závislosti na velikosti databáze může dokončení importu nějakou dobu trvat.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Další příklad skriptu viz [Import databáze ze souboru BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Omezení

- Import do databáze v elastickém fondu není podporován. Můžete importovat data do jedné databáze a potom přesunout databázi do elastického fondu.
- Import export ní služba nefunguje, když povolit přístup ke službám Azure je nastavena na OFF. Problém však můžete vyřešit ručním spuštěním sqlpackage.exe z virtuálního počítače Azure nebo provedením exportu přímo ve vašem kódu pomocí rozhraní DACFx API.

## <a name="import-using-wizards"></a>Import pomocí průvodců

Můžete také použít tyto průvodce.

- [Průvodce importem aplikací na datové vrstvě ve studiu SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Průvodce importem a exportem serveru SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak se připojit k importované databázi SQL a jak se jí dotazovat, najdete v [tématu Úvodní příručka: Azure SQL Database: Použití sql server management ového studia pro připojení a dotazování na data](sql-database-connect-query-ssms.md).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Diskuse o celém procesu migrace databáze SQL Server, včetně doporučení pro výkon, naleznete [v tématu migrace databáze SQL Server do Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o tom, jak bezpečně spravovat a sdílet klíče úložiště a sdílené přístupové podpisy, najdete v [tématu Průvodce zabezpečením úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
