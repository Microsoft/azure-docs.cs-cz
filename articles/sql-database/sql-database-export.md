---
title: Exportovat do souboru BACPAC souboru Azure SQL database | Microsoft Docs
description: "Azure SQL database exportovat do souboru BACPAC souboru pomocí portálu Azure"
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 10/11/2017
ms.author: carlrab
ms.topic: article
ms.openlocfilehash: 8f722f6d7563d22008105b503eb2433c7bd310a5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportovat do souboru BACPAC souboru Azure SQL database

Pokud budete potřebovat na export databáze pro archivaci nebo pro přesun na jiné platformě, můžete exportovat schéma databáze a dat [souboru BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) souboru. Soubor souboru BACPAC je soubor ZIP s příponou souboru BACPAC obsahující metadata a data z databáze systému SQL Server. Soubor souboru BACPAC lze uložená v úložišti objektů blob Azure nebo v místním úložišti v místní umístění a později importovat zpět do Azure SQL Database nebo do místní instalace SQL serveru. 

> [!IMPORTANT] 
> Azure SQL Database automatizované exportovat vyřazenou na 1. března 2017. Můžete použít [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md
) nebo [Azure Automation](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) pravidelně archivovat SQL databáze pomocí prostředí PowerShell podle plánu, podle vašeho výběru. Ukázku, stáhněte si [ukázkový skript prostředí PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) z Githubu.
>

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Aspekty při exportu Azure SQL database

* Pro export být stavu transakční konzistence, je nutné zajistit buď žádné zápisu aktivity dochází při exportu nebo které exportujete z [stavu transakční konzistence kopie](sql-database-copy.md) vaší databáze Azure SQL.
* Pokud chcete exportovat do úložiště objektů blob, je maximální velikost souboru BACPAC souboru 200 GB. K archivaci větší soubor souboru BACPAC, exportovat do místního úložiště.
* Export souboru BACPAC souboru na Azure premium storage pomocí metody popsané v tomto článku není podporován.
* Pokud operace exportu z databáze SQL Azure je větší než 20 hodin, může být zrušena. Chcete-li zvýšit výkon při exportu, můžete:
  * Dočasně zvýšit úroveň vaší služby.
  * Ukončí všechny čtení a zápisu aktivit během exportu.
  * Použití [clusterovaný index](https://msdn.microsoft.com/library/ms190457.aspx) s nenulové hodnoty pro všechny velké tabulky. Bez Clusterované indexy exportu může selhat, pokud trvá déle než 6 – 12 hodin. Je to proto, že služba export potřebuje k dokončení tabulky vyhledávání s cílem při exportu celou tabulku. Dobrým způsobem, jak určit, pokud vaše tabulky jsou optimalizované pro export je spuštění **DBCC SHOW_STATISTICS** a ujistěte se, že *RANGE_HI_KEY* není null a jeho hodnota má správné distribuční. Podrobnosti najdete v tématu [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs nejsou určeny k použít pro zálohování a obnovení operací. Databáze SQL Azure automaticky vytvoří zálohy pro každé uživatelské databáze. Podrobnosti najdete v tématu [obchodní kontinuity přehled](sql-database-business-continuity.md) a [zálohy databáze SQL](sql-database-automated-backups.md).  
> 

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportovat do souboru BACPAC souboru pomocí portálu Azure

Export databáze pomocí [portál Azure](https://portal.azure.com), otevřete stránku pro vaši databázi a klikněte na **exportovat** na panelu nástrojů. Zadejte název souboru BACPAC souboru, zadejte účet úložiště Azure a kontejner pro export a zadejte přihlašovací údaje pro připojení k databázi zdrojové.  

![Export databáze.](./media/sql-database-export/database-export.png)

Pokud chcete sledovat průběh operace exportu, otevřete stránku pro logický server obsahující databáze, která je exportována. Přejděte dolů k položce **operace** a pak klikněte na **importu a exportu** historie.

![Export historie](./media/sql-database-export/export-history.png)
![exportovat historie stavu](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportovat do souboru BACPAC souboru pomocí nástroje SQLPackage

Export databáze SQL pomocí [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) nástroj příkazového řádku najdete v části [exportovat parametry a vlastnosti](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). Nástroj SQLPackage se dodává s nejnovější verze [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) a [SQL Server Data Tools pro sadu Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), nebo si můžete stáhnout nejnovější verzi [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) přímo z webu Microsoft download center.

Doporučujeme použít nástroj SQLPackage pro škálování a výkon ve většině produkční prostředí. Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Tento příklad ukazuje, jak exportovat databázi pomocí SqlPackage.exe Universal ověřování služby Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportovat do souboru BACPAC souboru pomocí SQL Server Management Studio (SSMS)

Nejnovější verze SQL Server Management Studio také poskytují Průvodce exportovat do souboru BACPAC soubor databáze SQL Azure. Najdete v článku [exportovat aplikace na datové vrstvě](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportovat do souboru BACPAC soubor pomocí prostředí PowerShell

Použití [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) rutiny odeslání žádosti o export databáze ke službě Azure SQL Database. V závislosti na velikosti vaší databáze operace exportu může trvat delší dobu.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Chcete-li zkontrolovat stav žádosti exportu, použijte [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) rutiny. Spuštění to hned po dokončení žádosti o obvykle vrátí **stav: InProgress**. Až se zobrazí **stav: úspěšné** dokončení exportu.

```powershell
$exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")   
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Další postup

* Další informace o dlouhodobé uchovávání záloh zálohy databáze Azure SQL, jako alternativu k exportovány databázi pro účely archivace, najdete v části [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Další informace o import souboru BACPAC k databázi systému SQL Server najdete v tématu [importovat do databáze serveru SQL BACPCAC](https://msdn.microsoft.com/library/hh710052.aspx).
* Další informace o export souboru BACPAC z databáze SQL serveru najdete v tématu [exportovat aplikace na datové vrstvě](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) a [migrovat svoji první databázi](sql-database-migrate-your-sql-server-database.md).
* Chcete-li exportovat ze serveru SQL Server jako prelude migrace do Azure SQL Database, najdete v části [migrovat do databáze SQL serveru do Azure SQL Database](sql-database-cloud-migrate.md).
