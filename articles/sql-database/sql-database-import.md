---
title: Import souboru BACPAC k vytvoření databáze Azure SQL | Dokumentace Microsoftu
description: Vytvoření databáze SQL newAzure importem souboru BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: c5f90776cb0e8617f0e524bd6b1701f4bf20d0a1
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415697"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Rychlý start: Import souboru BACPAC do databáze ve službě Azure SQL Database

Můžete importovat do databáze SQL serveru do databáze Azure SQL Database pomocí [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) souboru. Můžete importovat data z `BACPAC` souborů uložených ve službě Azure Blob storage (jenom standardní úložiště) nebo z místního úložiště do místního umístění. Pokud chcete maximalizovat rychlost import tím, že poskytuje rychlejší a další prostředky, škálování databáze na vyšší úroveň služby a výpočetní velikost během procesu importu. Můžete pak vertikálně snížit kapacitu po úspěšném importu.

> [!NOTE]
> Úroveň kompatibility databáze importované je založená na úrovni kompatibility databáze zdroje.
> [!IMPORTANT]
> Po importu vaší databáze, můžete k provozu databáze na její aktuální úroveň kompatibility (úroveň 100 pro databázi AdventureWorks2008R2) nebo vyšší úrovni. Další informace o důsledcích a možnostech provozu databáze na konkrétní úrovni kompatibility najdete v tématu [Úroveň kompatibility ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). V tématu věnovaném příkazu [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) najdete také informace o dalších nastaveních na úrovni databáze souvisejících s úrovněmi kompatibility.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importovat ze souboru BACPAC na webu Azure Portal

[Webu Azure portal](https://portal.azure.com) *pouze* podporují vytvoření izolované databáze ve službě Azure SQL Database a *pouze* ze souboru BACPAC ukládají ve službě Azure Blob storage.

> [!NOTE]
> [Spravovaná instance](sql-database-managed-instance.md) aktuálně nepodporuje migrace databáze do instance databáze ze souboru BACPAC s použitím webu Azure portal. Pro import do spravované instance pomocí SQL Server Management Studio nebo nástroje SQLPackage.

1. Importovat ze souboru BACPAC do nové izolované databáze pomocí webu Azure portal, otevřete stránku pro příslušnou databázi serveru a pak na panelu nástrojů vyberte **importovat databázi**.  

   ![Import1 databáze](./media/sql-database-import/import1.png)

2. Vyberte účet úložiště a kontejner pro soubor BACPAC a potom vyberte soubor BACPAC ze kterého se má importovat.
3. Zadejte novou velikost databáze (obvykle stejné jako původní) a cíl zadejte přihlašovací údaje SQL serveru. Seznam možných hodnot pro novou databázi Azure SQL najdete v tématu [Create Database](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Import2 databáze](./media/sql-database-import/import2.png)

4. Klikněte na **OK**.

5. Chcete-li sledovat postup importu, otevřete stránku serveru vaší databáze a v části **nastavení**vyberte **historie importu a exportu**. V případě úspěchu se import **dokončeno** stav.

   ![Stav importu databáze](./media/sql-database-import/import-status.png)

6. Pokud chcete ověřit, databázi je v provozu na databázovém serveru, vyberte **databází SQL** a ověřte nové databáze **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importovat ze souboru BACPAC s použitím nástroje SqlPackage

Import pomocí databáze systému SQL Server [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) nástroj příkazového řádku, naleznete v tématu [importovat parametry a vlastnosti](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage má nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools pro Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Můžete si také stáhnout nejnovější [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) z webu Microsoft download center.

Škálovatelnost a výkon doporučujeme použitím nástroje SqlPackage ve většině produkčních prostředí než pomocí webu Azure portal. Pro blog zákaznického poradního týmu SQL serveru o migraci pomocí `BACPAC` soubory, naleznete v tématu [migraci z SQL serveru do Azure SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Následující příkaz SqlPackage importuje **AdventureWorks2008R2** databáze z místního úložiště na serveru Azure SQL Database s názvem **mynewserver20170403**. Vytvoří novou databázi s názvem **myMigratedDatabase** s **Premium** úroveň služby a **P6** cíle služby. Změňte tyto hodnoty v závislosti na vašem prostředí.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Pro připojení k serveru služby SQL Database Správa izolované databáze za podniková brána firewall, musí mít bránu firewall port 1433 otevřít. Chcete-li připojit k managed instance, musíte mít [připojení point-to-site](sql-database-managed-instance-configure-p2s.md) nebo připojení k expressroute.
>

Tento příklad ukazuje, jak importovat databázi s použitím nástroje SqlPackage pomocí univerzálního ověřování Active Directory.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Importovat do jedné databáze ze souboru BACPAC s použitím prostředí PowerShell

> [!NOTE]
> [Spravovaná instance](sql-database-managed-instance.md) aktuálně nepodporuje migrace databáze do instance databáze ze souboru BACPAC s použitím prostředí Azure PowerShell]. Pro import do spravované instance pomocí SQL Server Management Studio nebo nástroje SQLPackage.


Použití [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) rutinu k odeslání žádosti o importu databáze do služby Azure SQL Database. V závislosti na velikosti databáze může import trvat nějakou dobu.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Můžete použít [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) rutiny importu průběh zkontrolovat. Spuštěním rutiny ihned po žádosti obvykle vrátí **stavu: Probíhá zpracování**. Když se zobrazí dokončení importu **stavu: Úspěšné**.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Další příklad skriptu, naleznete v tématu [Import databáze ze souboru BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Omezení

Import do databáze v elastickém fondu se nepodporuje. Můžete importovat data do izolované databáze a poté přesuňte databázi do elastického fondu.

## <a name="import-using-wizards"></a>Import pomocí průvodců

Můžete také použít průvodců.

- [Průvodce aplikace datové vrstvy v aplikaci SQL Server Management Studio importem](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server Import a Export průvodce](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Další postup

- Informace o připojení a dotazování importovanou databázi SQL, najdete v článku [rychlý start: Azure SQL Database: Použít SQL Server Management Studio k připojení a dotazování dat](sql-database-connect-query-ssms.md).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Diskuzi o celý proces SQL serveru databáze migrace, včetně doporučení k výkonu, naleznete v tématu [migrace databáze SQL serveru do Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o správě a sdílení klíčů k úložišti a sdílené přístupové podpisy bezpečně, najdete v [Průvodci zabezpečením Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
