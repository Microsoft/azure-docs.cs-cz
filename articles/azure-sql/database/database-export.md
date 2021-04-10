---
title: Export Azure SQL Database do souboru BACPAC (Azure Portal)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Exportujte databázi do souboru BACPAC pomocí Azure Portal.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: 14854f839d6dfe3c8a08a4a1453fd78e389fe8d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568728"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>Export do souboru BACPAC-Azure SQL Database a Azure SQL Managed instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Pokud potřebujete exportovat databázi k archivaci nebo přesunout na jinou platformu, můžete exportovat schéma databáze a data do souboru [BacPac](/sql/relational-databases/data-tier-applications/data-tier-applications#Anchor_4) . Soubor BACPAC je soubor ZIP s příponou BACPAC obsahující metadata a data z databáze. Soubor BACPAC může být uložený v úložišti objektů BLOB v Azure nebo v místním úložišti v místním umístění a později se importuje zpátky do Azure SQL Database, spravované instance Azure SQL nebo instance SQL Server.

## <a name="considerations"></a>Požadavky

- Aby bylo možné export provést bez zásahu, je nutné zajistit, aby během exportu nedošlo k žádné aktivitě zápisu nebo zda exportujete z převedené [kopie](database-copy.md) vaší databáze.
- Pokud exportujete do úložiště objektů blob, maximální velikost souboru BACPAC je 200 GB. Pokud chcete archivovat větší soubor BACPAC, exportujte ho do místního úložiště.
- Export souboru BACPAC do služby Azure Premium Storage pomocí metod popsaných v tomto článku není podporován.
- Úložiště za bránou firewall se momentálně nepodporuje.
- Název souboru úložiště nebo vstupní hodnota pro StorageURI by měla být kratší než 128 znaků a nesmí končit znakem '. ' a nesmí obsahovat speciální znaky, jako je znak mezery nebo ' <, >, *,%, &,:, \, /,?). 
- Pokud operace exportu přesáhne 20 hodin, může se zrušit. Pokud chcete během exportu zvýšit výkon, můžete:

  - Dočasně zvyšte velikost výpočetní kapacity.
  - Ukončí veškerou aktivitu čtení a zápisu během exportu.
  - Použijte [clusterovaný index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) s hodnotami, které nejsou null na všech velkých tabulkách. Bez clusterovaných indexů může export selhat, pokud trvá déle než 6-12 hodin. Je to proto, že služba pro export potřebuje dokončit prohledávání tabulky, aby se pokusila exportovat celou tabulku. Dobrým způsobem, jak určit, jestli jsou vaše tabulky optimalizované pro export, je spuštění **příkazu DBCC SHOW_STATISTICS** a ujistěte se, že *RANGE_HI_KEY* není null a jeho hodnota má dobrou distribuci. Podrobnosti najdete v tématu [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql).

> [!NOTE]
> BACPACs nejsou určeny k použití pro operace zálohování a obnovení. Azure automaticky vytvoří zálohy pro každou uživatelskou databázi. Podrobnosti najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md) a [zálohování SQL Database](automated-backups-overview.md).

## <a name="the-azure-portal"></a>Azure Portal

Export BACPAC databáze ze [spravované instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) pomocí Azure Portal není aktuálně podporován. Místo toho použijte SQL Server Management Studio nebo SQLPackage.

> [!NOTE]
> Počítače zpracovávající požadavky na Import a export odeslané prostřednictvím Azure Portal nebo PowerShellu musí ukládat soubor BACPAC a také dočasné soubory generované Data-Tier aplikačním rozhraním (DacFX). Požadované místo na disku se výrazně liší mezi databázemi se stejnou velikostí a může vyžadovat místo na disku až třikrát velikosti databáze. Počítače, na kterých běží požadavek import/export, mají 450GB místo na místním disku. V důsledku toho se může stát, že některé požadavky selžou s chybou `There is not enough space on the disk` . V takovém případě je možné alternativní řešení spustit sqlpackage.exe v počítači s dostatečným místem na disku. K tomu, abyste se vyhnuli tomuto problému, doporučujeme používat [SqlPackage](#sqlpackage-utility) k importu a exportu databází větších než 150 GB.

1. Pokud chcete exportovat databázi pomocí [Azure Portal](https://portal.azure.com), otevřete stránku pro vaši databázi a na panelu nástrojů klikněte na **exportovat** .

   ![Snímek obrazovky, který zvýrazní tlačítko pro export.](./media/database-export/database-export1.png)

2. Zadejte název souboru BACPAC, vyberte existující účet úložiště Azure a kontejner pro export a potom zadejte příslušné přihlašovací údaje pro přístup ke zdrojové databázi. **Přihlašovací jméno správce SQL serveru** je potřeba, i když jste správce Azure, protože se správcem Azure nerovná oprávnění správce v Azure SQL Database nebo spravované instanci SQL Azure.

    ![Export databáze](./media/database-export/database-export2.png)

3. Klikněte na **OK**.

4. Chcete-li monitorovat průběh operace exportu, otevřete stránku serveru obsahujícího exportovanou databázi. V části **Nastavení** klikněte na možnost **Import/export historie**.

   ![Exportovat historii](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>Nástroj SQLPackage

Chcete-li exportovat databázi v SQL Database pomocí nástroje příkazového řádku [SqlPackage](/sql/tools/sqlpackage) , přečtěte si téma [Export parametrů a vlastností](/sql/tools/sqlpackage#export-parameters-and-properties). Nástroj SQLPackage je dodáván s nejnovějšími verzemi [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools for Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt)nebo si můžete stáhnout nejnovější verzi [SQLPackage](https://www.microsoft.com/download/details.aspx?id=53876) přímo z webu Microsoft Download Center.

Pro většinu produkčních prostředí doporučujeme používat nástroj SQLPackage pro škálování a výkon. Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Tento příklad ukazuje, jak exportovat databázi pomocí SqlPackage.exe s univerzálním ověřováním služby Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Nejnovější verze SQL Server Management Studio poskytují průvodce pro export databáze do Azure SQL Database nebo databáze spravované instance SQL do souboru BACPAC. Podívejte se na téma [Export aplikace na datové vrstvě](/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [Spravovaná instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) v současné době nepodporuje export databáze do souboru BACPAC pomocí Azure PowerShell. K exportu spravované instance do souboru BACPAC použijte SQL Server Management Studio nebo SQLPackage.

K odeslání žádosti o export databáze službě Azure SQL Database použijte rutinu [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) . V závislosti na velikosti databáze může dokončení operace exportu nějakou dobu trvat.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Pokud chcete zjistit stav žádosti o export, použijte rutinu [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Spuštění hned poté, co požadavek obvykle vrátí **stav: probíhá zpracování**. Po zobrazení **stavu:** export byl dokončen.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```
## <a name="cancel-the-export-request"></a>Zrušení žádosti o export

V následujícím příkladu příkazu PowerShellu použijte [operace Database Operations – Cancel API](/rest/api/sql/databaseoperations/cancel) nebo PowerShell [stop-AzSqlDatabaseActivity](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity).

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```

## <a name="next-steps"></a>Další kroky

- Další informace o dlouhodobém uchovávání záloh jedné databáze a databází ve fondu jako alternativu k exportu databáze pro účely archivace najdete v tématu [dlouhodobé uchovávání záloh](long-term-retention-overview.md). Úlohy agenta SQL můžete použít k plánování [záloh databáze pouze kopírování](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternativu dlouhodobého uchovávání záloh.
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).
- Další informace o importování BACPAC do databáze SQL Server najdete v tématu [Import BacPac do databáze SQL Server](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database).
- Další informace o exportu BACPAC z databáze SQL Server najdete v tématu [Export aplikace na datové vrstvě](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) .
- Další informace o použití služby migrace dat k migraci databáze najdete v tématu [migrace z SQL Server pro Azure SQL Database offline pomocí DMS](../../dms/tutorial-sql-server-to-azure-sql.md).
- Pokud exportujete z SQL Server jako předehru pro migraci do Azure SQL Database, přečtěte si téma [migrace databáze SQL Server do Azure SQL Database](migrate-to-database-from-sql-server.md).
- Informace o tom, jak bezpečně spravovat a sdílet klíče úložiště a signatury sdíleného přístupu, najdete v tématu [Azure Storage Průvodce zabezpečením](../../storage/blobs/security-recommendations.md).