---
title: Exportujte jednu nebo sdruženou databázi SQL Azure do souboru BACPAC | Microsoft Docs
description: Exportujte databázi SQL Azure do souboru BACPAC pomocí Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 07/16/2019
ms.openlocfilehash: 8d795fe88721dfed65134b550eb0036c4e7310eb
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305803"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Export databáze SQL Azure do souboru BACPAC

Pokud potřebujete exportovat databázi k archivaci nebo přesunout na jinou platformu, můžete exportovat schéma databáze a data do souboru [BacPac](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) . Soubor BACPAC je soubor ZIP s příponou BACPAC obsahující metadata a data z databáze SQL Server. Soubor BACPAC může být uložený v úložišti objektů BLOB v Azure nebo v místním úložišti v místním umístění a později se importuje zpátky do Azure SQL Database nebo do SQL Server místní instalace.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Předpoklady při exportu databáze SQL Azure

- Aby se export mohl provádět bez zásahu, musíte zajistit, aby během exportu neprobíhala žádná aktivita zápisu nebo aby se exportovali z [reakční kopie](sql-database-copy.md) vaší databáze SQL Azure.
- Pokud exportujete do úložiště objektů blob, maximální velikost souboru BACPAC je 200 GB. Pokud chcete archivovat větší soubor BACPAC, exportujte ho do místního úložiště.
- Export souboru BACPAC do služby Azure Premium Storage pomocí metod popsaných v tomto článku není podporován.
- Úložiště za bránou firewall se momentálně nepodporuje.
- Pokud operace exportu z Azure SQL Database přesáhne 20 hodin, může se zrušit. Pokud chcete během exportu zvýšit výkon, můžete:

  - Dočasně zvyšte velikost výpočetní kapacity.
  - Ukončí veškerou aktivitu čtení a zápisu během exportu.
  - Použijte [clusterovaný index](https://msdn.microsoft.com/library/ms190457.aspx) s hodnotami, které nejsou null na všech velkých tabulkách. Bez clusterovaných indexů může export selhat, pokud trvá déle než 6-12 hodin. Je to proto, že služba pro export potřebuje dokončit prohledávání tabulky, aby se pokusila exportovat celou tabulku. Dobrým způsobem, jak určit, jestli jsou vaše tabulky optimalizované pro export, je spuštění **příkazu DBCC SHOW_STATISTICS** a ujistěte se, že *RANGE_HI_KEY* není null a jeho hodnota má dobrou distribuci. Podrobnosti najdete v tématu [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs nejsou určeny k použití pro operace zálohování a obnovení. Azure SQL Database automaticky vytvoří zálohy pro každou uživatelskou databázi. Podrobnosti najdete v tématu [Přehled provozní kontinuity](sql-database-business-continuity.md) a [zálohování SQL Database](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Export do souboru BACPAC pomocí Azure Portal

Export BACPAC databáze ze [spravované instance](sql-database-managed-instance.md) pomocí Azure PowerShell není aktuálně podporován. Místo toho použijte SQL Server Management Studio nebo SQLPackage.

> [!NOTE]
> Počítače zpracovávající požadavky na Import a export odeslané prostřednictvím Azure Portal nebo PowerShellu musí ukládat soubor BACPAC a také dočasné soubory generované rozhraním Application Framework (DacFX) na datové vrstvě. Požadované místo na disku se výrazně liší mezi databázemi se stejnou velikostí a může vyžadovat místo na disku až třikrát velikosti databáze. Počítače, na kterých běží požadavek import/export, mají 450GB místo na místním disku. V důsledku toho se může stát, že některé požadavky selžou `There is not enough space on the disk`s chybou. V takovém případě je alternativním řešením spustit SqlPackage. exe na počítači s dostatečným místem na místním disku. K tomu, abyste se vyhnuli tomuto problému, doporučujeme používat [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) k importu a exportu databází větších než 150 GB.

1. Pokud chcete exportovat databázi pomocí [Azure Portal](https://portal.azure.com), otevřete stránku pro vaši databázi a na panelu nástrojů klikněte na **exportovat** .

   ![Export databáze](./media/sql-database-export/database-export1.png)

2. Zadejte název souboru BACPAC, vyberte existující účet úložiště Azure a kontejner pro export a potom zadejte příslušné přihlašovací údaje pro přístup ke zdrojové databázi. **Přihlašovací jméno správce SQL serveru** je potřeba, i když jste správce Azure, protože správce Azure nerovná se SQL Server oprávnění správce.

    ![Export databáze](./media/sql-database-export/database-export2.png)

3. Klikněte na **OK**.

4. Chcete-li monitorovat průběh operace exportu, otevřete stránku serveru SQL Database obsahujícího exportovanou databázi. V části **Nastavení** klikněte na možnost **Import/export historie**.

   ![Exportovat historii](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Export do souboru BACPAC pomocí nástroje SQLPackage

Chcete-li exportovat databázi SQL pomocí nástroje příkazového řádku [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) , přečtěte si téma [Export parametrů a vlastností](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Nástroj SQLPackage je dodáván s nejnovějšími verzemi [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) a [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)nebo si můžete stáhnout nejnovější verzi [SQLPackage](https://www.microsoft.com/download/details.aspx?id=53876) přímo z webu Microsoft Download Center.

Pro většinu produkčních prostředí doporučujeme používat nástroj SQLPackage pro škálování a výkon. Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Tento příklad ukazuje, jak exportovat databázi pomocí SqlPackage. exe s univerzálním ověřováním služby Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Export do souboru BACPAC pomocí SQL Server Management Studio (SSMS)

Nejnovější verze SQL Server Management Studio poskytují průvodce pro export databáze SQL Azure do souboru BACPAC. Podívejte se na téma [Export aplikace na datové vrstvě](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Export do souboru BACPAC pomocí prostředí PowerShell

> [!NOTE]
> [Spravovaná instance v](sql-database-managed-instance.md) současné době nepodporuje export databáze do souboru BACPAC pomocí Azure PowerShell. K exportu spravované instance do souboru BACPAC použijte SQL Server Management Studio nebo SQLPackage.

K odeslání žádosti o export databáze službě Azure SQL Database použijte rutinu [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) . V závislosti na velikosti databáze může dokončení operace exportu nějakou dobu trvat.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Pokud chcete zjistit stav žádosti o export, použijte rutinu [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Okamžité spuštění, jakmile požadavek obvykle vrátí **stav: Probíhá zpracování**. Když se zobrazí **stav: Export** byl úspěšně dokončen.

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

## <a name="next-steps"></a>Další postup

- Další informace o dlouhodobém uchovávání záloh izolovaných databází a databází ve fondu jako alternativu k exportu databáze pro účely archivace najdete v tématu [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md). Úlohy agenta SQL můžete použít k plánování [záloh databáze pouze kopírování](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternativu dlouhodobého uchovávání záloh.
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Další informace o importování BACPAC do databáze SQL Server najdete v tématu [Import BacPac do databáze SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Další informace o exportu BACPAC z databáze SQL Server najdete v tématu [Export aplikace na datové vrstvě](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) .
- Další informace o použití služby migrace dat k migraci databáze najdete v tématu [migrace SQL Server pro Azure SQL Database offline pomocí DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Pokud exportujete z SQL Server jako předehru pro migraci do Azure SQL Database, přečtěte si téma [migrace databáze SQL Server do Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o správě a sdílení klíčů k úložišti a sdílené přístupové podpisy bezpečně, najdete v [Průvodci zabezpečením Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
