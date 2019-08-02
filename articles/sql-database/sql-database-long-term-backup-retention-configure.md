---
title: Správa Azure SQL Database dlouhodobého uchovávání záloh | Microsoft Docs
description: Naučte se ukládat automatizované zálohy do úložiště SQL Azure a pak je obnovit.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/17/2019
ms.openlocfilehash: 38ecd7797452c9a16b859da921287b8026f0660d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567785"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Správa Azure SQL Database dlouhodobého uchovávání záloh

V Azure SQL Database můžete nakonfigurovat jednu nebo sdruženou databázi s dlouhodobou zásadou [uchovávání záloh](sql-database-long-term-retention.md) (LTR) pro automatické uchovávání záloh v úložišti objektů BLOB v Azure po dobu až 10 let. Pak můžete databázi pomocí těchto záloh obnovit pomocí Azure Portal nebo PowerShellu.

> [!IMPORTANT]
> [Azure SQL Database Managed instance](sql-database-managed-instance.md) v současné době nepodporuje dlouhodobé uchovávání záloh.

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Použití Azure Portal ke konfiguraci dlouhodobých zásad uchovávání informací a obnovení záloh

V následujících částech se dozvíte, jak použít Azure Portal ke konfiguraci dlouhodobého uchovávání, zobrazení záloh v dlouhodobém uchovávání a obnovení zálohy z dlouhodobého uchovávání.

### <a name="configure-long-term-retention-policies"></a>Konfigurace dlouhodobých zásad uchovávání informací

Můžete nakonfigurovat SQL Database pro [uchovávání automatizovaných záloh](sql-database-long-term-retention.md) po dobu delší, než je doba uchování pro vaši úroveň služeb. 

1. V Azure Portal vyberte svůj SQL Server a pak klikněte na **Spravovat zálohy**. Na kartě **Konfigurovat zásady** *zaškrtněte políčko pro databázi, ve které chcete nastavit nebo upravit dlouhodobé zásady uchovávání záloh*. Pokud není zaškrtnuté políčko vedle databáze, změny zásad se na tuto databázi nevztahují.  

   ![odkaz Správa zálohování](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. V podokně **Konfigurovat zásady** vyberte, pokud chcete uchovávat týdenní, měsíční nebo roční zálohy a určete dobu uchování pro každou z nich. 

   ![Konfigurace zásad](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Po dokončení klikněte na **použít**.

> [!IMPORTANT]
> Pokud povolíte dlouhodobé zásady uchovávání záloh, může trvat až 7 dní, než se první záloha zobrazí a bude dostupná pro obnovení. Podrobnosti o Cadance zálohování LTR najdete v tématu [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Zobrazení záloh a obnovení ze zálohy pomocí Azure Portal

Prohlédněte si zálohy, které jsou uchovány pro určitou databázi se zásadou LTR, a obnovte je z těchto záloh. 

1. V Azure Portal vyberte svůj SQL Server a pak klikněte na **Spravovat zálohy**. Na kartě **dostupné zálohy** vyberte databázi, pro kterou chcete zobrazit dostupné zálohy.

   ![vybrat databázi](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. V podokně **dostupné zálohy** zkontrolujte dostupné zálohy. 

   ![Zobrazit zálohy](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Vyberte zálohu, ze které chcete obnovit, a pak zadejte název nové databáze.

   ![Obnovení](./media/sql-database-long-term-retention/ltr-restore.png)

5. Kliknutím na **OK** obnovte databázi ze zálohy ve službě Azure SQL Storage do nové databáze.

6. Pokud chcete zobrazit stav úlohy obnovení, na panelu nástrojů klikněte na ikonu oznámení.

   ![průběh úlohy obnovení](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Po dokončení úlohy obnovení otevřete stránku **databáze SQL** a zobrazte nově obnovenou databázi.

> [!NOTE]
> Odtud se můžete pomocí aplikace SQL Server Management Studio připojit k obnovené databázi a provádět požadované úlohy, jako je například [extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze na název existující databáze](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Konfigurace dlouhodobých zásad uchovávání a obnovení záloh pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

V následujících částech se dozvíte, jak pomocí PowerShellu nakonfigurovat dlouhodobé uchovávání záloh, zobrazit zálohy ve službě Azure SQL Storage a obnovit je ze zálohy ve službě Azure SQL Storage.


### <a name="rbac-roles-to-manage-long-term-retention"></a>Role RBAC pro správu dlouhodobého uchovávání

Aby bylo možné spravovat zálohy LTR, budete muset 
- Vlastník předplatného nebo
- Role přispěvatele SQL Server v oboru předplatného nebo
- Role přispěvatele SQL Database v oboru předplatného

Pokud potřebujete podrobnější kontrolu, můžete vytvořit vlastní role RBAC a přiřadit je v oboru předplatného. 

Pro **Get-AzSqlDatabaseLongTermRetentionBackup** a **Restore-AzSqlDatabase** role musí mít následující oprávnění:

Microsoft. SQL/Locations/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/ longTermRetentionBackups/číst
 
Pro **Remove-AzSqlDatabaseLongTermRetentionBackup** role musí mít následující oprávnění:

Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete


### <a name="create-an-ltr-policy"></a>Vytvoření zásady LTR

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

# get the server
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Zobrazit zásady LTR
Tento příklad ukazuje, jak zobrazit seznam zásad LTR v rámci serveru.

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Vymazat zásadu LTR
Tento příklad ukazuje, jak vymazat zásadu LTR z databáze.

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Zobrazit zálohy LTR

Tento příklad ukazuje, jak zobrazit seznam záloh LTR v rámci serveru. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Odstranit zálohy LTR

Tento příklad ukazuje, jak odstranit zálohu LTR ze seznamu záloh.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> Odstranění zálohy LTR je nevratné. Můžete nastavit oznámení o každém odstranění v Azure Monitor filtrováním pro operaci odstraní zálohu pro dlouhodobé uchovávání. Protokol aktivit obsahuje informace o tom, kdo a kdy žádost odeslal. Podrobné pokyny najdete v tématu [vytvoření výstrah protokolu aktivit](../azure-monitor/platform/alerts-activity-log.md) .
>

### <a name="restore-from-ltr-backups"></a>Obnovení ze zálohy LTR
Tento příklad ukazuje, jak obnovit ze zálohy LTR. Všimněte si, že toto rozhraní se nezměnilo, ale parametr ID prostředku teď vyžaduje ID záložního prostředku LTR. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Odtud se můžete připojit k obnovené databázi pomocí SQL Server Management Studio a provést potřebné úkoly, jako je například extrakce dat z obnovené databáze pro zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze k názvu existující databáze. Viz [Obnovení bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Další postup

- Další informace o automatických zálohách generovaných službou najdete u popisu [automatických záloh](sql-database-automated-backups.md).
- Další informace o dlouhodobém uchovávání záloh najdete v části [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md)
