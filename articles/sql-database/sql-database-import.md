---
title: Import souboru BACPAC k vytvoření databáze Azure SQL | Dokumentace Microsoftu
description: Vytvoření databáze SQL newAzure importem souboru BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: f48e9656aa2f562a1475bc5e0f6e81fdcbfda66a
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361682"
---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>Import souboru BACPAC do nové databáze SQL Azure

Když je potřeba importovat databázi z archivu nebo při migraci z jiné platformy, můžete importovat schéma databáze a dat z [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) souboru. Soubor BACPAC je soubor ZIP s příponou souboru BACPAC, který obsahuje metadata i data z databáze SQL serveru. Soubor BACPAC je možné importovat z Azure blob storage (jenom standardní úložiště) nebo z místního úložiště do místního umístění. Maximalizovat rychlost import, doporučujeme zadat vyšší úroveň služby a vypočítat velikost, například P6 a pak škálovat dolů podle potřeby a po úspěšném importu. Navíc úroveň kompatibility databáze po dokončení importu podle zdrojové databáze úroveň kompatibility.

> [!IMPORTANT]
> Po dokončení migrace databáze do služby Azure SQL Database můžete provozovat databázi na její aktuální úroveň kompatibility (úroveň 100 pro databázi AdventureWorks2008R2) nebo vyšší úrovni. Další informace o důsledcích a možnostech provozu databáze na konkrétní úrovni kompatibility najdete v tématu [Úroveň kompatibility ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). V tématu věnovaném příkazu [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) najdete také informace o dalších nastaveních na úrovni databáze souvisejících s úrovněmi kompatibility.

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Importovat ze souboru BACPAC s použitím webu Azure portal

Tento článek obsahuje pokyny pro vytvoření Azure SQL database ze souboru BACPAC uložená v Azure blob storage pomocí [webu Azure portal](https://portal.azure.com). Import pomocí webu Azure portal podporuje import souboru BACPAC z úložiště objektů blob v Azure.

K importu databáze pomocí webu Azure portal, otevřete stránku pro server (ne stránku pro databázi) přidružit databázi a potom klikněte na **importovat** na panelu nástrojů. Zadejte účet úložiště a kontejner a vyberte soubor BACPAC, který chcete importovat. Vyberte velikost novou databázi (obvykle stejné jako původní) a zadejte cíl přihlašovací údaje SQL serveru.  

   ![import databáze](./media/sql-database-import/import.png)

Pokud chcete monitorovat průběh operace importu, otevřete stránku pro logický server, který obsahuje importovanou databází. Přejděte dolů k položce **operace** a potom klikněte na tlačítko **Import/Export** historie.

> [!NOTE]
> [Azure SQL Database Managed Instance](sql-database-managed-instance.md) nepodporuje import ze souboru BACPAC soubor pomocí jiné metody v tento článek ale fakturuje se aktuálně podporují migraci pomocí webu Azure portal.

### <a name="monitor-the-progress-of-an-import-operation"></a>Sledujte průběh operace importu

Pokud chcete monitorovat průběh operace importu, otevřete stránku pro logický server, do kterého se importuje databázi. Přejděte dolů k položce **operace** a potom klikněte na tlačítko **Import/Export** historie.

   ![Importovat](./media/sql-database-import/import-history.png) ![stav importu](./media/sql-database-import/import-status.png)

K ověření na serveru databáze, klikněte na tlačítko **databází SQL** a ověřte nové databáze **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importovat ze souboru BACPAC s použitím nástroje SQLPackage

Import databáze SQL pomocí [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) nástroj příkazového řádku, naleznete v tématu [importovat parametry a vlastnosti](https://docs.microsoft.com/sql/tools/sqlpackage#-import-parameters-and-properties). Nástroj SQLPackage se dodává s nejnovější verzí [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) a [SQL Server Data Tools pro Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), nebo si můžete stáhnout nejnovější verzi [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) přímo z webu Microsoft download center.

Doporučujeme používat nástroj SQLPackage pro škálování a výkonu ve většině produkčních prostředí. Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Viz následující příkaz SQLPackage příklad skriptu pro import **AdventureWorks2008R2** databáze z místního úložiště k logickému serveru Azure SQL Database, volá **mynewserver20170403** v tomto příkladu. Tento skript ukazuje vytvoření objektu novou databázi s názvem **myMigratedDatabase**, se na úrovni služby **Premium**a jako cíl služby **P6**. Změňte tyto hodnoty v závislosti na vašem prostředí.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Logický server Azure SQL Database naslouchá na portu 1433. Pokud se pokoušíte připojovat k logickému serveru Azure SQL Database z oblasti za podnikovou bránou firewall, je třeba v podnikové brány firewall otevřít tento port, abyste se mohli úspěšně připojit.
>

Tento příklad ukazuje, jak importovat databázi SqlPackage.exe pomocí univerzálního ověřování Active Directory:

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importovat ze souboru BACPAC s použitím prostředí PowerShell

Použití [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) rutinu k odeslání žádosti o importu databáze do služby Azure SQL Database. V závislosti na velikosti databáze se operace importu může trvat nějakou dobu.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

Chcete-li zkontrolovat stav požadavek na importování, použijte [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) rutiny. Spuštěna ihned po žádosti obvykle vrátí **stav: Probíhá zpracování**. Když se zobrazí **stav: úspěšné** dokončení importu.

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

- Importovat do databáze v elastickém fondu se nepodporuje. Můžete importovat data do izolované databáze a potom přesunutí databáze do fondu.

## <a name="import-using-other-methods"></a>Import pomocí jiné metody

Můžete také použít průvodců:

- [Průvodce aplikace datové vrstvy v aplikaci SQL Server Management Studio importem](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server Import a Export průvodce](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Další postup

- Informace o připojení a dotazování importovanou databázi SQL, najdete v článku [připojit k SQL Database přes SQL Server Management Studio a provedení ukázkového dotazu T-SQL](sql-database-connect-query-ssms.md).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Diskuzi o celý proces SQL serveru databáze migrace, včetně doporučení k výkonu, naleznete v tématu [migrace databáze SQL serveru do služby Azure SQL Database](sql-database-cloud-migrate.md).
- Informace o správě a sdílení klíčů k úložišti a sdílené přístupové podpisy bezpečně, najdete v [Průvodci zabezpečením Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
