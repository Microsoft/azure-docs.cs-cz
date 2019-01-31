---
title: Import souboru BACPAC k vytvoření databáze Azure SQL | Dokumentace Microsoftu
description: Vytvoření databáze SQL newAzure importem souboru BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: douglaslMS
ms.author: douglasl
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: c1b6c55475c1600c89c1ac1cae9dee0068b92070
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478215"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>Rychlý start: Import souboru BACPAC do nové databáze SQL Azure

Můžete migrovat databáze SQL serveru do Azure SQL database pomocí [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) file (soubor zip s `.bacpac` rozšíření, která obsahuje metadata a data databáze). Můžete import souboru BACPAC z úložiště objektů blob v Azure (jenom standardní úložiště) nebo z místního úložiště do místního umístění. Maximalizovat rychlost importu, můžete zadat na vyšší úroveň služby a vypočítat velikost (například P6). Můžete pak vertikálně snížit kapacitu po úspěšném importu. Úroveň kompatibility databáze importované je založená na úrovni kompatibility databáze zdroje.

> [!IMPORTANT]
> Po importu vaší databáze, můžete k provozu databáze na její aktuální úroveň kompatibility (úroveň 100 pro databázi AdventureWorks2008R2) nebo vyšší úrovni. Další informace o důsledcích a možnostech provozu databáze na konkrétní úrovni kompatibility najdete v tématu [Úroveň kompatibility ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). V tématu věnovaném příkazu [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) najdete také informace o dalších nastaveních na úrovni databáze souvisejících s úrovněmi kompatibility.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importovat ze souboru BACPAC na webu Azure Portal

Tato část ukazuje, jak v [webu Azure portal](https://portal.azure.com), chcete-li vytvořit Azure SQL database ze souboru BACPAC uložené ve službě Azure blob storage. Na portálu *pouze* podporuje import souboru BACPAC z Azure blob storage.

> [!NOTE]
> [Azure SQL Database Managed Instance](sql-database-managed-instance.md) podporuje import ze souboru BACPAC s použitím jiné metody v tomto článku, ale aktuálně nepodporuje migraci na webu Azure Portal.

K importu databáze na webu Azure Portal, otevřete stránku pro server SQL Database, která bude hostovat import a na panelu nástrojů vyberte **importovat databázi**.  

   ![import databáze](./media/sql-database-import/import.png)

Vyberte účet úložiště, kontejner a souboru BACPAC, který chcete importovat. Zadejte novou velikost databáze (obvykle stejné jako původní) a cíl zadejte přihlašovací údaje SQL serveru. 

### <a name="monitor-imports-progress"></a>Sledujte průběh importu

Chcete-li sledovat postup importu, otevřete stránku serveru importovanou databázi a v části **nastavení**vyberte **historie importu a exportu**. V případě úspěchu se import **dokončeno** stav.

Pokud chcete ověřit, je databáze na serveru, vyberte **databází SQL** a ověřte nové databáze **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importovat ze souboru BACPAC s použitím nástroje SqlPackage

Import databáze SQL pomocí [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) nástroj příkazového řádku, naleznete v tématu [importovat parametry a vlastnosti](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage má nejnovější [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) a [SQL Server Data Tools pro Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Můžete si také stáhnout nejnovější [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) z webu Microsoft download center.

Škálovatelnost a výkon doporučujeme použitím nástroje SqlPackage ve většině produkčních prostředí. Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Následující příkaz SqlPackage importuje **AdventureWorks2008R2** databáze z místního úložiště na serveru Azure SQL Database s názvem **mynewserver20170403**. Vytvoří novou databázi s názvem **myMigratedDatabase** s **Premium** úroveň služby a **P6** cíle služby. Změňte tyto hodnoty v závislosti na vašem prostředí.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Server služby SQL Database naslouchá na portu 1433. Pro připojení k serveru služby SQL Database za podniková brána firewall, brána firewall musí mít tento port otevřít.
>

Tento příklad ukazuje, jak importovat databázi s použitím nástroje SqlPackage pomocí univerzálního ověřování Active Directory.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importovat ze souboru BACPAC s použitím prostředí PowerShell

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
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Diskuzi o celý proces SQL serveru databáze migrace, včetně doporučení k výkonu, naleznete v tématu [migrace databáze SQL serveru do Azure SQL Database](sql-database-cloud-migrate.md).
- Informace o správě a sdílení klíčů k úložišti a sdílené přístupové podpisy bezpečně, najdete v [Průvodci zabezpečením Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
