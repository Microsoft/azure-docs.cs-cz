---
title: Exportovat do souboru BACPAC s Azure SQL database | Dokumentace Microsoftu
description: Exportovat databázi Azure SQL do souboru BACPAC s použitím webu Azure portal
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: douglaslMS
ms.author: douglasl
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: d00d9b4a945b139ed7b930609fdab24e4a211551
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55891093"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportovat databázi Azure SQL do souboru BACPAC

Když je potřeba vyexportovat databázi pro archivaci nebo pro přesun do jiné platformy, můžete exportovat schéma databáze a dat [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) souboru. Soubor BACPAC je soubor ZIP s příponou souboru BACPAC, který obsahuje metadata i data z databáze SQL serveru. Soubor BACPAC mohou být uloženy v úložišti objektů Blob v Azure nebo v místním úložišti v místním umístěním a později importované zpět do Azure SQL Database nebo do systému SQL Server na místní instalace.

> [!IMPORTANT]
> Azure SQL Database automatický Export skončil na 1. března 2017. Můžete použít [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md
) nebo [Azure Automation](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) pravidelné archivace SQL databáze pomocí prostředí PowerShell podle plánu podle vašeho výběru. Ukázku, stáhněte si [ukázkový skript Powershellu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) z Githubu.
>

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

Chcete-li exportovat databázi pomocí [webu Azure portal](https://portal.azure.com), otevřete stránku pro vaši databázi a klikněte na tlačítko **exportovat** na panelu nástrojů. Zadejte název souboru BACPAC, zadejte účet služby Azure storage a kontejner pro export a zadejte přihlašovací údaje pro připojení k databázi zdrojové.

![export databáze](./media/sql-database-export/database-export.png)

Můžete sledovat průběh operace exportu otevřete stránku pro server SQL Database, který obsahuje exportovanou databázi. Přejděte dolů k položce **operace** a potom klikněte na tlačítko **Import/Export** historie.

![Export historie](./media/sql-database-export/export-history.png)
![export historie stavu](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportovat do souboru BACPAC s použitím nástroje SQLPackage

Export databáze SQL pomocí [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) nástroj příkazového řádku, naleznete v tématu [exportovat parametry a vlastnosti](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Nástroj SQLPackage se dodává s nejnovější verzí [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) a [SQL Server Data Tools pro Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), nebo si můžete stáhnout nejnovější verzi [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) přímo z webu Microsoft download center.

Doporučujeme používat nástroj SQLPackage pro škálování a výkonu ve většině produkčních prostředí. Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Tento příklad ukazuje, jak exportovat databázi SqlPackage.exe pomocí univerzálního ověřování Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportovat do souboru BACPAC s použitím SQL Server Management Studio (SSMS)

Nejnovější verze aplikace SQL Server Management Studio také poskytuje průvodce export do souboru BACPAC s Azure SQL Database. Zobrazit [exportem aplikace datové vrstvy](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportovat do souboru BACPAC s použitím prostředí PowerShell

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

- Další informace o dlouhodobém uchovávání záloh zálohy Azure SQL database jako alternativu k exportovat databázi pro účely archivace, naleznete v tématu [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md).
- Příspěvek na blogu zákaznického poradního týmu SQL Serveru o migraci pomocí souborů BACPAC najdete v tématu popisujícím [migraci z SQL Serveru do služby SQL Database pomocí souborů BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Další informace o import souboru BACPAC do databáze SQL serveru najdete v tématu [Import souboru BACPAC do databáze systému SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Další informace o export souboru BACPAC z databáze SQL serveru najdete v tématu [exportem aplikace datové vrstvy](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Další informace o použití služby migrace dat k migraci databáze, najdete v článku [migrace systému SQL Server do služby Azure SQL Database pomocí DMS offline](../dms/tutorial-sql-server-to-azure-sql.md).
- Pokud exportujete z SQL serveru jako prelude migrace do Azure SQL Database, přečtěte si téma [migrace databáze SQL serveru do služby Azure SQL Database](sql-database-cloud-migrate.md).
- Informace o správě a sdílení klíčů k úložišti a sdílené přístupové podpisy bezpečně, najdete v [Průvodci zabezpečením Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
