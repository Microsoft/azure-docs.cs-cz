---
title: Spravovat dlouhodobé uchovávání záloh Azure SQL Database | Microsoft Docs
description: Zjistěte, jak uložit automatizované zálohování SQL Azure storage a potom je obnovit
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 0ce22cae50e70ca7232e025d4009b23d62f6a198
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649223"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Spravovat dlouhodobé uchovávání záloh Azure SQL Database

Můžete nakonfigurovat databázi Azure SQL s [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md) zásad (zleva doprava) automaticky uchování záloh v Azure blob storage až 10 let. Pak můžete obnovit databázi pomocí tyto zálohy pomocí portálu Azure nebo Powershellu.

> [!NOTE]
> Jako součást počáteční verze Preview tuto funkci v říjnu 2016 byly zálohy uložené ve službě Azure Recovery Services trezoru. Tato aktualizace odebere tuto závislost, ale kvůli zpětné kompatibilitě se původní rozhraní API podporuje do 31 může 2018. Pokud potřebujete pro interakci s záloh v trezoru Azure Services Recovery, přečtěte si [dlouhodobé uchovávání záloh pomocí trezoru službu Azure Recovery Services](sql-database-long-term-backup-retention-configure-vault.md). 

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Pomocí portálu Azure ke konfiguraci zásady dlouhodobé uchovávání informací a obnovit zálohy

Následující části ukazují, jak pomocí portálu Azure ke konfiguraci dlouhodobé uchovávání, zobrazit v dlouhodobé uchovávání zálohování a obnovení zálohy z dlouhodobé uchovávání.

### <a name="configure-long-term-retention-policies"></a>Nakonfigurujte zásady dlouhodobé uchovávání

Můžete nakonfigurovat databázi SQL pro [zachovat automatizované zálohování](sql-database-long-term-retention.md) po dobu delší než doba uchování pro vaše vrstvu služeb. 

1. Na portálu Azure vyberte svůj server SQL a pak klikněte na tlačítko **dlouhodobé uchovávání záloh**.

   ![odkaz na dlouhodobé uchovávání záloh](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Na **nakonfigurovat zásady** kartě, vyberte databázi, na kterém chcete nastavit nebo změnit zásady dlouhodobé uchovávání záloh.

   ![Vyberte databázi](./media/sql-database-long-term-retention/ltr-configure-select-database.png)

3. V **nakonfigurovat zásady** podokně, vyberte, zda chcete zachovat týdně, měsíčně nebo ročně zálohy a zadejte dobu uchování pro každou. 

   ![Konfigurace zásad](./media/sql-database-long-term-retention/ltr-configure-policies.png)

4. Po dokončení klikněte na tlačítko **použít**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Zobrazit zálohování a obnovení ze zálohy pomocí portálu Azure

Zobrazte zálohování, které se zachovají pro konkrétní databázi zásad zleva doprava a obnovení ze tyto zálohy. 

1. Na portálu Azure vyberte svůj server SQL a pak klikněte na tlačítko **dlouhodobé uchovávání záloh**.

   ![odkaz na dlouhodobé uchovávání záloh](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Na **dostupné zálohy** kartě, vyberte databázi, pro který chcete zobrazit dostupné zálohy.

   ![Vyberte databázi](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. V **dostupné zálohy** podokně zkontrolujte dostupné zálohy. 

   ![Zobrazit zálohy](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Vyberte zálohu, ze kterého chcete obnovit a pak zadejte název nové databáze.

   ![Obnovení](./media/sql-database-long-term-retention/ltr-restore.png)

5. Klikněte na tlačítko **OK** obnovit databázi ze zálohy v úložišti Azure SQL k nové databázi.

6. Pokud chcete zobrazit stav úlohy obnovení, na panelu nástrojů klikněte na ikonu oznámení.

   ![průběh úlohy obnovení z trezoru](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Po dokončení úlohy obnovení, otevřete **databází SQL** zobrazit nově obnovenou databázi.

> [!NOTE]
> Odtud se můžete pomocí aplikace SQL Server Management Studio připojit k obnovené databázi a provádět požadované úlohy, jako je například [extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze na název existující databáze](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Nakonfigurovat zásady dlouhodobé uchovávání informací a obnovení zálohy pomocí prostředí PowerShell

Následující části ukazují, jak pomocí prostředí PowerShell nakonfigurovat dlouhodobé uchovávání záloh, prohlížet zálohy v úložiště Azure SQL a obnovení ze zálohy v úložišti Azure SQL.

> [!IMPORTANT]
> Budete muset použít nejnovější powershell AzureRM nastavit zásady V2 zleva doprava. Aktuální verze je [AzureRM 4.5.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0-preview), to je ve verzi preview, takže použít tento příkaz k její instalaci: `Install-Module -Name AzureRM.Sql -AllowPrerelease -Force`.
> Pokyny k instalaci předprodejní verze najdete v tématu [modulu získat PowerShellGet](https://docs.microsoft.com/en-us/powershell/gallery/installing-psget). Prostředí powershell AzureRM verzi může 2018 pochází za pár dní (očekáván 5/18 nebo 2018), můžete ignorovat přepínačem - AllowPrelease při instalací prodejní verze, až bude k dispozici a použijte následující příkaz " `Install-Module -Name AzureRM.Sql -Force`.

### <a name="create-an-ltr-policy"></a>Vytvoření zásady zleva doprava.

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Zobrazit zásady zleva doprava.
Tento příklad ukazuje, jak k zobrazení seznamu zásad zleva doprava v rámci serveru

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Vymazat zásady zleva doprava.
Tento příklad ukazuje, jak vymazat zásadu LTR z databáze

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Zobrazit zálohy zleva doprava.

Tento příklad ukazuje postup seznam LTR záloh v rámci serveru. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Odstranit zálohy zleva doprava.

Tento příklad ukazuje postup odstranění LTR zálohování ze seznamu záloh.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Obnovení ze zálohy zleva doprava.
Tento příklad ukazuje postup obnovení ze zálohy zleva doprava. Všimněte si, toto rozhraní se nezměnila, ale parametr id prostředků teď vyžaduje id prostředku zálohování zleva doprava. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Tady můžete připojit k obnovené databázi pomocí SQL Server Management Studio potřebné úkoly, například za účelem extrahování bit dat z obnovené databáze chcete zkopírovat do existující databáze nebo odstranit existující databázi a přejmenujte obnovenou databáze na název existující databáze. V tématu [bodu v době obnovení](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Další postup

- Další informace o automatických zálohách generovaných službou najdete u popisu [automatických záloh](sql-database-automated-backups.md).
- Další informace o dlouhodobém uchovávání záloh najdete v části [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md)
