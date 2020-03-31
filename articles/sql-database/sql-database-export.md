---
title: Export jedné nebo sdružené databáze do souboru BACPAC
description: Export databáze Azure SQL do souboru BACPAC pomocí portálu Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 0bc72f0ad58829a3ff6545b5c4741ddc20916c31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061632"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Export databáze Azure SQL do souboru BACPAC

Pokud potřebujete exportovat databázi pro archivaci nebo pro přesun na jinou platformu, můžete exportovat schéma databáze a data do souboru [BACPAC.](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) Soubor BACPAC je soubor ZIP s příponou BACPAC obsahující metadata a data z databáze SQL Serveru. Soubor BACPAC lze uložit v úložišti objektů Blob Azure nebo v místním úložišti v místním umístění a později importovat zpět do Azure SQL Database nebo do místní instalace SQL Server.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Důležité informace při exportu databáze Azure SQL

- Aby byl export interakreditálně konzistentní, musíte zajistit, aby během exportu nedocházelo k žádné aktivitě zápisu nebo aby exportovalz [transakční konzistentní kopie](sql-database-copy.md) databáze Azure SQL.
- Pokud exportujete do úložiště objektů blob, maximální velikost souboru BACPAC je 200 GB. Chcete-li archivovat větší soubor BACPAC, exportujte do místního úložiště.
- Export souboru BACPAC do úložiště Azure premium pomocí metod popsaných v tomto článku není podporován.
- Úložiště za bránou firewall není aktuálně podporováno.
- Pokud operace exportu z Azure SQL Database překročí 20 hodin, může být zrušena. Chcete-li zvýšit výkon během exportu, můžete:

  - Dočasně zvětšit velikost výpočetních prostředků.
  - Ukončit veškerou aktivitu čtení a zápisu během exportu.
  - Ve všech velkých tabulkách použijte [seskupený index](https://msdn.microsoft.com/library/ms190457.aspx) s hodnotami, které nejsou nulové. Bez seskupených indexů může dojít k selhání exportu, pokud trvá déle než 6-12 hodin. Důvodem je, že služba exportu musí dokončit kontrolu tabulky, aby se pokusila exportovat celou tabulku. Dobrým způsobem, jak zjistit, zda jsou tabulky optimalizovány pro export, je spuštění **SHOW_STATISTICS DBCC** a ujistěte se, že *RANGE_HI_KEY* není null a jeho hodnota má dobré rozdělení. Podrobnosti naleznete v [tématu DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPAC nejsou určeny k použití pro operace zálohování a obnovení. Azure SQL Database automaticky vytvoří zálohy pro každou uživatelskou databázi. Podrobnosti naleznete v [tématu přehled kontinuity provozu](sql-database-business-continuity.md) a [zálohování databáze SQL](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Export do souboru BACPAC pomocí portálu Azure

Export BACPAC databáze ze [spravované instance](sql-database-managed-instance.md) pomocí Azure PowerShell u prostřednictvím Azure PowerShell není aktuálně podporován. Místo toho použijte SQL Server Management Studio nebo SQLPackage.

> [!NOTE]
> Počítače zpracovávající požadavky na import a export odeslané prostřednictvím portálu Azure nebo prostředí PowerShell musí ukládat soubor BACPAC a také dočasné soubory generované architekturou datových vrstev (DacFX). Požadované místo na disku se v databázích se stejnou velikostí značně liší a může vyžadovat až trojnásobek velikosti databáze. Počítače s požadavkem na import a export mají pouze 450 GB místního místa na disku. V důsledku toho může dojít k `There is not enough space on the disk`selhání některých požadavků s chybou . V tomto případě je řešení spustit sqlpackage.exe v počítači s dostatkem místa na disku. Doporučujeme používat [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) importovat a exportovat databáze větší než 150 GB, aby se zabránilo tomuto problému.

1. Pokud chcete exportovat databázi pomocí [portálu Azure](https://portal.azure.com), otevřete stránku databáze a na panelu nástrojů klikněte na **Exportovat.**

   ![Export databáze](./media/sql-database-export/database-export1.png)

2. Zadejte název souboru BACPAC, vyberte existující účet úložiště Azure a kontejner pro export a zadejte příslušná pověření pro přístup ke zdrojové databázi. Přihlášení **správce** SQL Serveru je potřeba zde i v případě, že jste správce Azure, jako je správce Azure se nerovná s sql server oprávnění správce.

    ![Export databáze](./media/sql-database-export/database-export2.png)

3. Klikněte na tlačítko **OK**.

4. Chcete-li sledovat průběh operace exportu, otevřete stránku serveru SQL Database obsahující exportovnou databázi. V části **Nastavení** klikněte na **Položku Importovat nebo exportovat historii**.

   ![historie exportu](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Export do souboru BACPAC pomocí nástroje SQLPackage

Informace o exportu databáze SQL pomocí nástroje příkazového řádku [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) naleznete v [tématu Export parametrů a vlastností](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Nástroj SQLPackage je dodáván s nejnovějšími verzemi [sql server management studio](https://msdn.microsoft.com/library/mt238290.aspx) a SQL Server Data Tools pro Visual [Studio](https://msdn.microsoft.com/library/mt204009.aspx), nebo si můžete stáhnout nejnovější verzi [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) přímo ze služby Stažení softwaru společnosti Microsoft.

Doporučujeme použití nástroje SQLPackage pro škálování a výkon ve většině produkčních prostředí. Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Tento příklad ukazuje, jak exportovat databázi pomocí souboru SqlPackage.exe pomocí univerzálního ověřování služby Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Export do souboru BACPAC pomocí sql server management studio (SSMS)

Nejnovější verze SQL Server Management Studio poskytuje průvodce pro export databáze Azure SQL do souboru BACPAC. Viz [Export aplikace datové vrstvy](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Export do souboru BACPAC pomocí prostředí PowerShell

> [!NOTE]
> [Spravovaná instance](sql-database-managed-instance.md) aktuálně nepodporuje export databáze do souboru BACPAC pomocí Azure PowerShellu. Chcete-li exportovat spravovanou instanci do souboru BACPAC, použijte SQL Server Management Studio nebo SQLPackage.

Pomocí rutiny [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) odešlete požadavek na export databáze do služby Azure SQL Database. V závislosti na velikosti databáze může dokončení operace exportu nějakou dobu trvat.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Chcete-li zkontrolovat stav požadavku na export, použijte rutinu [Get-AzSqlDatabaseExportExportStatus.](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) Spuštění tohoto bezprostředně po požadavku obvykle vrátí **Stav: InProgress**. Když se zobrazí **stav: Proběhlo úspěšné** export je dokončen.

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

## <a name="next-steps"></a>Další kroky

- Informace o dlouhodobé uchování zálohování jedné databáze a sdružené databáze jako alternativu k exportované databáze pro účely archivace naleznete v [tématu Dlouhodobé uchovávání záloh](sql-database-long-term-retention.md). Úlohy sql agenta můžete naplánovat [zálohování pouze zkopírování databáze](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternativu k dlouhodobé uchovávání záloh.
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Informace o importu bacpacu do databáze serveru SQL Server naleznete [v tématu Import bacpacu do databáze serveru SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Další informace o exportu BACPAC z databáze serveru SQL Server naleznete v [tématu Export aplikace datové vrstvy](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Informace o migraci databáze pomocí služby migrace dat najdete v [tématu Migrace SQL Serveru do databáze Azure SQL offline pomocí Služby DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Pokud exportujete z SQL Serveru jako předehru k migraci do azure sql database, přečtěte si další [informace o migraci databáze SERVERU SQL Server do databáze Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o tom, jak bezpečně spravovat a sdílet klíče úložiště a sdílené přístupové podpisy, najdete v [tématu Průvodce zabezpečením úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
