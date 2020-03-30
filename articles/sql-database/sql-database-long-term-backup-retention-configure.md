---
title: Správa dlouhodobého uchovávání záloh
description: Přečtěte si, jak ukládat automatické zálohy do úložiště SQL Azure a pak je obnovit.
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
ms.openlocfilehash: a560f4f1399792a7b150b37c3c048ccc0079b98d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420800"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Správa dlouhodobého uchovávání záloh v Azure SQL Database

V Azure SQL Database můžete nakonfigurovat jednu nebo sdruženou databázi s [dlouhodobou](sql-database-long-term-retention.md) zásadou uchovávání informací (LTR) tak, aby automaticky uchovávaly zálohy databáze v samostatných kontejnerech úložiště objektů Blob Azure po dobu až 10 let. Potom můžete obnovit databázi pomocí těchto záloh pomocí portálu Azure nebo PowerShellu.

> [!IMPORTANT]
> [Spravovaná instance databáze Azure SQL](sql-database-managed-instance.md) aktuálně nepodporuje dlouhodobé uchovávání záloh.

## <a name="using-azure-portal"></a>Pomocí webu Azure Portal

V následujících částech se ukazuje, jak pomocí portálu Azure nakonfigurovat dlouhodobé uchovávání informací, zobrazit zálohy v dlouhodobém uchovávání a obnovit zálohování z dlouhodobého uchovávání.

### <a name="configure-long-term-retention-policies"></a>Konfigurace zásad dlouhodobého uchovávání informací

Databázi SQL můžete nakonfigurovat tak, aby [uchovávala automatické zálohy](sql-database-long-term-retention.md) po dobu delší, než je doba uchování pro vaši úroveň služeb.

1. Na webu Azure Portal vyberte sql server a klikněte na **Spravovat zálohy**. Na **kartě Konfigurovat zásady** zaškrtněte políčko pro databázi, ve které chcete nastavit nebo upravit dlouhodobé zásady uchovávání záloh. Pokud není zaškrtnuto políčko vedle databáze, změny zásad se pro tuto databázi nepoužijí.  

   ![spravovat propojení záloh](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. V podokně **Konfigurovat zásady** vyberte, zda chcete zachovat týdenní, měsíční nebo roční zálohy a určit dobu uchování pro každou z nich.

   ![konfigurace zásad](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Po dokončení klepněte na **tlačítko Použít**.

> [!IMPORTANT]
> Pokud povolíte dlouhodobé zásady uchovávání informací zálohování, může trvat až 7 dní, než se první záloha zobrazí a bude k dispozici obnovení. Podrobnosti o záložní mačkání LTR naleznete v [tématu dlouhodobé uchovávání záloh](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Zobrazení záloh a obnovení ze zálohy

Zobrazení zálohy, které jsou zachovány pro konkrétní databázi s zásadou LTR a obnovení z těchto záloh.

1. Na webu Azure Portal vyberte sql server a klikněte na **Spravovat zálohy**. Na kartě **Dostupné zálohy** vyberte databázi, pro kterou chcete zobrazit dostupné zálohy.

   ![vybrat databázi](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

1. V podokně **Dostupné zálohy** zkontrolujte dostupné zálohy.

   ![zobrazení záloh](./media/sql-database-long-term-retention/ltr-available-backups.png)

1. Vyberte zálohu, ze které chcete obnovit, a zadejte nový název databáze.

   ![Obnovení](./media/sql-database-long-term-retention/ltr-restore.png)

1. Kliknutím na **OK** obnovíte databázi ze zálohy v úložišti Azure SQL do nové databáze.

1. Pokud chcete zobrazit stav úlohy obnovení, na panelu nástrojů klikněte na ikonu oznámení.

   ![průběh úlohy obnovení](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

1. Po dokončení **úlohy** obnovení otevřete stránku databáze SQL a zobrazte nově obnovenou databázi.

> [!NOTE]
> Odtud se můžete pomocí aplikace SQL Server Management Studio připojit k obnovené databázi a provádět požadované úlohy, jako je například [extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze na název existující databáze](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

V následujících částech se ukazuje, jak pomocí PowerShellu nakonfigurovat dlouhodobé uchovávání záloh, zobrazit zálohy v úložišti Azure SQL a obnovit ze zálohy v úložišti Azure SQL.

### <a name="rbac-roles-to-manage-long-term-retention"></a>Role RBAC pro správu dlouhodobého uchovávání

Pro **Get-AzSqlDatabaseLongTermRetentionBackup** a **Restore-AzSqlDatabase**, budete muset mít jednu z následujících rolí:

- Role vlastníka předplatného nebo
- Role přispěvatele serveru SQL Server nebo
- Vlastní role s následujícími oprávněními:

   Microsoft.Sql/locations/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionDatabases/longTermRetentionBackups/read

Pro **Remove-AzSqlDatabaseLongTermRetentionBackup**, budete muset mít jednu z následujících rolí:

- Role vlastníka předplatného nebo
- Vlastní role s následujícím oprávněním:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> Role přispěvatele serveru SQL Server nemá oprávnění k odstranění záloh LTR.

Oprávnění RBAC mohou být udělena v *oboru předplatného* nebo *skupiny prostředků.* Pro přístup k zálohám LTR, které patří do vyřazeného serveru, musí být oprávnění uděleno v oboru *předplatného* tohoto serveru.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>Vytvoření zásady LTR

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Zobrazit zásady LTR

Tento příklad ukazuje, jak uvést zásady LTR v rámci serveru

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | `
    Get-AzSqlDatabaseLongTermRetentionPolicy -Current

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -Current
```

### <a name="clear-an-ltr-policy"></a>Vymazání zásady LTR

Tento příklad ukazuje, jak vymazat zásady LTR z databáze

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Zobrazit zálohy LTR

Tento příklad ukazuje, jak seznam záloh LTR v rámci serveru.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Odstranění záloh LTR

Tento příklad ukazuje, jak odstranit zálohu LTR ze seznamu záloh.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Odstranění zálohy LTR je nevratné. Chcete-li odstranit zálohu LTR po odstranění serveru, musíte mít oprávnění oboru odběr. Oznámení o každém odstranění v Azure Monitoru můžete nastavit filtrováním operace "Odstraní dlouhodobou zálohu uchovávání informací". Protokol aktivit obsahuje informace o tom, kdo a kdy byl požadavek podán. Podrobné pokyny naleznete [v tématu Vytvoření výstrah protokolu aktivit.](../azure-monitor/platform/alerts-activity-log.md)

### <a name="restore-from-ltr-backups"></a>Obnovení ze záloh LTR

Tento příklad ukazuje, jak obnovit ze zálohy LTR. Všimněte si, že toto rozhraní se nezměnilo, ale parametr Id prostředku nyní vyžaduje ID záložního prostředku LTR.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Chcete-li obnovit ze zálohy LTR po odstranění serveru, musíte mít oprávnění vymezená na odběr serveru a toto předplatné musí být aktivní. Musíte také vynechat volitelný parametr -ResourceGroupName.

> [!NOTE]
> Odtud se můžete pomocí aplikace SQL Server Management Studio připojit k obnovené databázi a provádět požadované úlohy, jako je například extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze na název existující databáze. Viz [bod v čase obnovení](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Další kroky

- Další informace o automatických zálohách generovaných službou najdete u popisu [automatických záloh](sql-database-automated-backups.md).
- Další informace o dlouhodobém uchovávání záloh najdete v části [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md)
