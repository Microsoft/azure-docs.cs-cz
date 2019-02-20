---
title: Exportovat jeden nebo ve fondu Azure SQL database do souboru BACPAC | Dokumentace Microsoftu
description: Exportovat databázi Azure SQL do souboru BACPAC s použitím webu Azure portal
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: 757d7e039b24beb170545d8055bad16410cf7883
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415880"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportovat databázi Azure SQL do souboru BACPAC

Když je potřeba vyexportovat databázi pro archivaci nebo pro přesun do jiné platformy, můžete exportovat schéma databáze a dat [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) souboru. Soubor BACPAC je soubor ZIP s příponou souboru BACPAC, který obsahuje metadata i data z databáze SQL serveru. Soubor BACPAC mohou být uloženy v úložišti objektů Blob v Azure nebo v místním úložišti v místním umístěním a později importované zpět do Azure SQL Database nebo do systému SQL Server na místní instalace.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Důležité informace při exportu služby Azure SQL database

- Pro export transakčně konzistentní, je nutné zajistit buď bez zápisu aktivity dochází při exportu nebo, který jste exportovali z [transakčně konzistentní kopie](sql-database-copy.md) pro Azure SQL database.
- Pokud exportujete do úložiště objektů blob, maximální velikost souboru BACPAC je 200 GB. Pro archivaci souboru BACPAC s větší, exportovat do místního úložiště.
- Export souboru BACPAC do Azure premium storage pomocí metody popsané v tomto článku se nepodporuje.
- Pokud operace exportu ze služby Azure SQL Database je větší než 20 hodin, může být zrušena. Chcete-li zvýšit výkon při exportu, můžete:

  - Velikost výpočetních dočasně zvýšit.
  - Ukončení všech čtení a zápis aktivit během exportu.
  - Použití [clusterovaný index](https://msdn.microsoft.com/library/ms190457.aspx) s nenulovou hodnoty na všech velké tabulky. Bez Clusterované indexy export může selhat, pokud to trvá déle, než 6 až 12 hodin. Je to proto, že služba export musí dokončit prohledávání tabulky k pokusu o export celou tabulku. Dobrým způsobem, jak určit, pokud vaše tabulky jsou optimalizované pro export, je spustit **DBCC SHOW_STATISTICS** a ujistěte se, že *RANGE_HI_KEY* nemá hodnotu null a jeho hodnota má dobré distribuci. Podrobnosti najdete v tématu [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> U souborů Bacpac nejsou určeny k použít pro zálohování a obnovení operací. Azure SQL Database automaticky vytvoří zálohy pro každé uživatelské databázi. Podrobnosti najdete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md) a [záloh SQL Database](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportovat do souboru BACPAC s použitím webu Azure portal

> [!NOTE]
> [Spravovaná instance](sql-database-managed-instance.md) aktuálně nepodporuje export databáze do souboru BACPAC s použitím webu Azure portal. Export do souboru BACPAC managed instance pomocí SQL Server Management Studio nebo nástroje SQLPackage.

1. Chcete-li exportovat databázi pomocí [webu Azure portal](https://portal.azure.com), otevřete stránku pro vaši databázi a klikněte na tlačítko **exportovat** na panelu nástrojů.

   ![export databáze](./media/sql-database-export/database-export1.png)

2. Zadejte název souboru BACPAC, vybrat existující účet úložiště Azure a kontejnerů pro export a potom zadejte příslušné přihlašovací údaje pro přístup ke zdrojové databázi.

    ![export databáze](./media/sql-database-export/database-export2.png)

3. Klikněte na **OK**.

4. Můžete sledovat průběh operace exportu otevřete stránku pro server SQL Database, který obsahuje exportovanou databázi. V části pro **nastavení** a potom klikněte na tlačítko **historie importu a exportu**.

   ![Export historie](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportovat do souboru BACPAC s použitím nástroje SQLPackage

Export databáze SQL pomocí [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) nástroj příkazového řádku, naleznete v tématu [exportovat parametry a vlastnosti](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Nástroj SQLPackage se dodává s nejnovější verzí [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) a [SQL Server Data Tools pro Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), nebo si můžete stáhnout nejnovější verzi [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) přímo z webu Microsoft download center.

Doporučujeme používat nástroj SQLPackage pro škálování a výkonu ve většině produkčních prostředí. Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Tento příklad ukazuje, jak exportovat databázi SqlPackage.exe pomocí univerzálního ověřování Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportovat do souboru BACPAC s použitím SQL Server Management Studio (SSMS)

Nejnovější verze aplikace SQL Server Management Studio poskytuje průvodce export do souboru BACPAC s Azure SQL database. Zobrazit [exportem aplikace datové vrstvy](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportovat do souboru BACPAC s použitím prostředí PowerShell

> [!NOTE]
> [Spravovaná instance](sql-database-managed-instance.md) aktuálně nepodporuje export databáze do souboru BACPAC s použitím prostředí Azure PowerShell. Export do souboru BACPAC managed instance pomocí SQL Server Management Studio nebo nástroje SQLPackage.

Použití [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) rutinu k odeslání žádosti o export databáze do služby Azure SQL Database. V závislosti na velikosti databáze operace exportu může trvat nějakou dobu.

```powershell
$exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Chcete-li zkontrolovat stav žádosti o export, použijte [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) rutiny. Spuštěna ihned po žádosti obvykle vrátí **stavu: Probíhá zpracování**. Když se zobrazí **stavu: Úspěšné** export byl dokončen.

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

- Další informace o dlouhodobém uchovávání záloh z jedné databáze a databáze ve fondu jako alternativu k exportovat databázi pro účely archivace, najdete v části [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md). Úlohy agenta SQL serveru můžete použít k naplánování [pouze kopie zálohy](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternativu k dlouhodobé uchovávání záloh.
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Další informace o import souboru BACPAC do databáze SQL serveru najdete v tématu [Import souboru BACPAC do databáze systému SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Další informace o export souboru BACPAC z databáze SQL serveru najdete v tématu [exportem aplikace datové vrstvy](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Další informace o použití služby migrace dat k migraci databáze, najdete v článku [migrace systému SQL Server do služby Azure SQL Database pomocí DMS offline](../dms/tutorial-sql-server-to-azure-sql.md).
- Pokud exportujete z SQL serveru jako prelude migrace do Azure SQL Database, přečtěte si téma [migrace databáze SQL serveru do služby Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o správě a sdílení klíčů k úložišti a sdílené přístupové podpisy bezpečně, najdete v [Průvodci zabezpečením Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
