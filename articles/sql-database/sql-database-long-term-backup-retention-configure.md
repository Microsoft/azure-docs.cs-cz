---
title: Správa dlouhodobého uchovávání záloh
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
manager: craigg
ms.date: 08/21/2019
ms.openlocfilehash: ea9a1da775a64f8ee405ced52df01d0824836c42
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820022"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Správa Azure SQL Database dlouhodobého uchovávání záloh

V Azure SQL Database můžete nakonfigurovat jednu nebo sdruženou databázi s [dlouhodobou zásadou uchovávání záloh](sql-database-long-term-retention.md) (LTR) a automaticky tak uchovávat zálohy databáze v samostatných kontejnerech úložiště Azure Blob po dobu až 10 let. Pak můžete databázi pomocí těchto záloh obnovit pomocí Azure Portal nebo PowerShellu.

> [!IMPORTANT]
> [Azure SQL Database Managed instance](sql-database-managed-instance.md) v současné době nepodporuje dlouhodobé uchovávání záloh.

## <a name="use-the-azure-portal-to-manage-long-term-backups"></a>Použití Azure Portal ke správě dlouhodobých záloh

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

## <a name="use-powershell-to-manage-long-term-backups"></a>Použití PowerShellu ke správě dlouhodobých záloh

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

V následujících částech se dozvíte, jak pomocí PowerShellu nakonfigurovat dlouhodobé uchovávání záloh, zobrazit zálohy ve službě Azure SQL Storage a obnovit je ze zálohy ve službě Azure SQL Storage.


### <a name="rbac-roles-to-manage-long-term-retention"></a>Role RBAC pro správu dlouhodobého uchovávání

Pro **Get-AzSqlDatabaseLongTermRetentionBackup** a **Restore-AzSqlDatabase**budete muset mít jednu z následujících rolí:

- Role vlastníka předplatného nebo
- SQL Server role přispěvatele nebo
- Vlastní role s následujícími oprávněními:

   Microsoft. SQL/Locations/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/ longTermRetentionBackups/číst
 
V případě funkce **Remove-AzSqlDatabaseLongTermRetentionBackup**budete muset mít jednu z následujících rolí:

- Role vlastníka předplatného nebo
- Vlastní role s následujícím oprávněním:

   Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/DELETE


> [!NOTE]
> Role Přispěvatel SQL Server nemá oprávnění odstraňovat zálohy LTR.

Oprávnění RBAC se mohla udělit buď v rámci *předplatného* , nebo v oboru *skupiny prostředků* . Pro přístup k zálohám LTR, které patří k vyřazenému serveru, ale musí být oprávnění uděleno v oboru *předplatného* daného serveru.


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
# List all LTR backups under the current subscription in a specific Azure region 
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# List the LTR backups under a specific resource group in a specific Azure region 
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup

# List the LTR backups under an existing server
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup -ServerName $serverName

# List the LTR backups for a specific database 
# The backups are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.  
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database under a server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Odstranit zálohy LTR

Tento příklad ukazuje, jak odstranit zálohu LTR ze seznamu záloh.

```powershell
# Remove the earliest backup from the list of backups
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> Odstranění zálohy LTR je nevratné. Pokud chcete odstranit zálohu LTR po odstranění serveru, musíte mít oprávnění k oboru předplatného. Můžete nastavit oznámení o každém odstranění v Azure Monitor filtrováním pro operaci odstraní zálohu pro dlouhodobé uchovávání. Protokol aktivit obsahuje informace o tom, kdo a kdy žádost odeslal. Podrobné pokyny najdete v tématu [vytvoření výstrah protokolu aktivit](../azure-monitor/platform/alerts-activity-log.md) .
>

### <a name="restore-from-ltr-backups"></a>Obnovení ze zálohy LTR
Tento příklad ukazuje, jak obnovit ze zálohy LTR. Všimněte si, že toto rozhraní se nezměnilo, ale parametr ID prostředku teď vyžaduje ID záložního prostředku LTR. 

```powershell
# Restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup 
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Pro obnovení ze zálohy LTR po odstranění serveru musíte mít oprávnění oboru pro předplatné serveru a předplatné musí být aktivní. Je také nutné vynechat volitelný parametr-ResourceGroupName.  
>

> [!NOTE]
> Odtud se můžete připojit k obnovené databázi pomocí SQL Server Management Studio a provést potřebné úkoly, jako je například extrakce dat z obnovené databáze pro zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze k názvu existující databáze. Viz [Obnovení bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Další kroky

- Další informace o automatických zálohách generovaných službou najdete u popisu [automatických záloh](sql-database-automated-backups.md).
- Další informace o dlouhodobém uchovávání záloh najdete v části [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md)
