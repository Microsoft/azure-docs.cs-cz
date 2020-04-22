---
title: 'Spravovaná instance: Dlouhodobé uchovávání záloh (PowerShell)'
description: Zjistěte, jak ukládat a obnovovat automatické zálohy v samostatných kontejnerech úložiště objektů Blob Azure pro spravovanou instanci Azure SQL Database pomocí PowerShellu.
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
ms.date: 04/19/2020
ms.openlocfilehash: 24eacb555704593fe44bc2d949de44de163345bc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677107"
---
# <a name="manage-azure-sql-database-managed-instance-long-term-backup-retention-powershell"></a>Správa dlouhodobého uchovávání záloh služby Azure SQL Database (PowerShell)

Ve spravované instanci Azure SQL Database můžete nakonfigurovat dlouhodobé zásady [uchovávání informací o zálohování](sql-database-long-term-retention.md#managed-instance-support) (LTR) jako omezenou funkci public preview. To vám umožní automaticky uchovávat zálohy databáze v samostatných kontejnerech úložiště objektů Blob Azure po dobu až 10 let. Potom můžete obnovit databázi pomocí těchto záloh pomocí prostředí PowerShell.

   > [!IMPORTANT]
   > LTR pro spravované instance je aktuálně v omezené verzi preview a je k dispozici pro odběry EA a CSP případ od případu. Chcete-li požádat o registraci, vytvořte [lístek podpory Azure](https://azure.microsoft.com/support/create-ticket/) pod tématem podpory **Zálohování, Obnovení a Kontinuita podnikání / Dlouhodobé uchovávání záloh**. 


V následujících částech se ukazuje, jak pomocí PowerShellu nakonfigurovat dlouhodobé uchovávání záloh, zobrazit zálohy v úložišti Azure SQL a obnovit ze zálohy v úložišti Azure SQL.

## <a name="rbac-roles-to-manage-long-term-retention"></a>Role RBAC pro správu dlouhodobého uchovávání

Pro **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** and **Restore-AzSqlInstanceDatabase**budete muset mít jednu z následujících rolí:

- Role vlastníka předplatného nebo
- Role přispěvatele spravované instance nebo
- Vlastní role s následujícími oprávněními:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Pro **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup**, budete muset mít jednu z následujících rolí:

- Role vlastníka předplatného nebo
- Vlastní role s následujícím oprávněním:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Role přispěvatele spravované instance nemá oprávnění k odstranění záloh LTR.

Oprávnění RBAC mohou být udělena v *oboru předplatného* nebo *skupiny prostředků.* Pro přístup k zálohám LTR, které patří do vyřazené instance, musí být oprávnění uděleno v oboru *předplatného* této instance.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>Vytvoření zásady LTR

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

## <a name="view-ltr-policies"></a>Zobrazit zásady LTR

Tento příklad ukazuje, jak uvést zásady LTR v rámci instance

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>Vymazání zásady LTR

Tento příklad ukazuje, jak vymazat zásady LTR z databáze

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>Zobrazit zálohy LTR

Tento příklad ukazuje, jak vypsat zálohy LTR v rámci instance.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase
```

## <a name="delete-ltr-backups"></a>Odstranění záloh LTR

Tento příklad ukazuje, jak odstranit zálohu LTR ze seznamu záloh.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Odstranění zálohy LTR je nevratné. Chcete-li odstranit zálohu LTR po odstranění instance, musíte mít oprávnění oboru odběr. Oznámení o každém odstranění v Azure Monitoru můžete nastavit filtrováním operace "Odstraní dlouhodobou zálohu uchovávání informací". Protokol aktivit obsahuje informace o tom, kdo a kdy byl požadavek podán. Podrobné pokyny naleznete [v tématu Vytvoření výstrah protokolu aktivit.](../azure-monitor/platform/alerts-activity-log.md)

## <a name="restore-from-ltr-backups"></a>Obnovení ze záloh LTR

Tento příklad ukazuje, jak obnovit ze zálohy LTR. Všimněte si, že toto rozhraní se nezměnilo, ale parametr Id prostředku nyní vyžaduje ID záložního prostředku LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> Chcete-li obnovit ze zálohy LTR po odstranění instance, musíte mít oprávnění vymezená pro odběr instance a toto předplatné musí být aktivní. Musíte také vynechat volitelný parametr -ResourceGroupName.

> [!NOTE]
> Odtud se můžete pomocí aplikace SQL Server Management Studio připojit k obnovené databázi a provádět požadované úlohy, jako je například extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze na název existující databáze. Viz [bod v čase obnovení](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Další kroky

- Další informace o automatických zálohách generovaných službou najdete u popisu [automatických záloh](sql-database-automated-backups.md).
- Další informace o dlouhodobém uchovávání záloh najdete v části [dlouhodobé uchovávání záloh](sql-database-long-term-retention.md)
