---
title: Správa dlouhodobého uchovávání záloh Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak ukládáním automatizovaných záloh v SQL Azure storage a potom je obnovit
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 850467dff0a16cb2ac7cda44537406f0267711b4
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366518"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Správa dlouhodobého uchovávání záloh Azure SQL Database

Azure SQL database s můžete nakonfigurovat [dlouhodobého uchovávání záloh](sql-database-long-term-retention.md) zásad (LTR) automaticky uchovávání záloh ve službě Azure blob storage po dobu až 10 let. Pak můžete obnovit databázi pomocí tyto zálohy pomocí webu Azure portal nebo Powershellu.

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Pomocí webu Azure portal nakonfigurovat zásady dlouhodobou uchovávání a obnovení záloh

Následující části ukazují, jak pomocí webu Azure portal nakonfigurovat dlouhodobé uchovávání, zobrazení v rámci dlouhodobého uchovávání záloh a obnovení z dlouhodobého uchování zálohy.

### <a name="configure-long-term-retention-policies"></a>Konfigurace dlouhodobého uchovávání informací zásady

SQL Database, které můžete nakonfigurovat [uchovávání automatizovaných záloh](sql-database-long-term-retention.md) dobu delší, než je doba uchovávání vaší úrovně služby. 

1. Na webu Azure Portal, vyberte váš server SQL a pak klikněte na tlačítko **spravovat zálohy**. Na **nakonfigurovat zásady** kartu, zaškrtněte políčko pro databázi, na kterém chcete nastavit nebo změnit zásady dlouhodobého uchovávání záloh.

   ![Správa záloh odkaz](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. V **nakonfigurovat zásady** podokně, vyberte, zda chcete uchovávat týdenní, měsíční nebo roční zálohy a určit dobu uchovávání pro každý. 

   ![Konfigurace zásad](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Jakmile budete hotovi, klikněte na tlačítko **použít**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Zobrazení záloh a obnovit ze zálohy pomocí webu Azure portal

Zobrazení záloh, které jsou zachovány určité databáze s zásady LTR a obnovit tyto zálohy. 

1. Na webu Azure Portal, vyberte váš server SQL a pak klikněte na tlačítko **spravovat zálohy**. Na **dostupnými zálohami** kartu, vyberte databázi, pro kterou chcete zobrazit dostupné zálohy.

   ![Vyberte databázi](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. V **dostupnými zálohami** podokno, projděte si dostupné zálohy. 

   ![zobrazení záloh](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Vybrat zálohu, ze kterého chcete obnovit a pak zadejte název nové databáze.

   ![Obnovení](./media/sql-database-long-term-retention/ltr-restore.png)

5. Klikněte na tlačítko **OK** k obnovení databáze ze zálohy v úložišti Azure SQL do nové databáze.

6. Pokud chcete zobrazit stav úlohy obnovení, na panelu nástrojů klikněte na ikonu oznámení.

   ![průběh úlohy obnovení](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Po dokončení úlohy obnovení otevřete **databází SQL** stránku a zobrazte nově obnovenou databázi.

> [!NOTE]
> Odtud se můžete pomocí aplikace SQL Server Management Studio připojit k obnovené databázi a provádět požadované úlohy, jako je například [extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze na název existující databáze](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Konfigurace zásady dlouhodobou uchovávání a obnovení záloh pomocí Powershellu

Následující části ukazují, jak pomocí prostředí PowerShell můžete nakonfigurovat dlouhodobé uchovávání záloh, zobrazení záloh v Azure SQL storage a obnovení ze zálohy v úložišti Azure SQL.

> [!IMPORTANT]
> Rozhraní API V2 zleva doprava je podporováno v následujících verzích Powershellu:
- [AzureRM.Sql 4.5.0](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0) nebo novější
- [AzureRM 6.1.0](https://www.powershellgallery.com/packages/AzureRM/6.1.0) nebo novější
> 

### <a name="create-an-ltr-policy"></a>Vytvoření zásad LTR

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

### <a name="view-ltr-policies"></a>Zobrazit zásady LTR
Tento příklad ukazuje, jak zobrazit seznam zásad LRT v rámci serveru

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Vymazat zásadu zleva doprava.
Tento příklad ukazuje, jak vymazat zásady LTR z databáze

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Zobrazení záloh zleva doprava.

Tento příklad ukazuje, jak zobrazit seznam zálohování LTR v rámci serveru. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Odstranit zálohy zleva doprava.

Tento příklad ukazuje odstranění LTR zálohování z seznam záloh.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Obnovení ze zálohy zleva doprava.
Tento příklad ukazuje, jak obnovit ze zálohy zleva doprava. Poznámka: Toto rozhraní nezměnil, ale parametr id prostředku vyžaduje id prostředku zálohování LTR. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Odtud se můžete připojit k obnovené databázi pomocí SQL Server Management Studio a provádět požadované úlohy, například za účelem extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovenou databáze na název existující databáze. Zobrazit [bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Další postup

- Další informace o automatických zálohách generovaných službou najdete u popisu [automatických záloh](sql-database-automated-backups.md).
- Další informace o dlouhodobém uchovávání záloh najdete v části [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md)
