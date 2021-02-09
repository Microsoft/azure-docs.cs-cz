---
title: 'Spravovaná instance Azure SQL: dlouhodobé uchovávání záloh (PowerShell)'
description: Naučte se ukládat a obnovovat automatizované zálohy na samostatných kontejnerech úložiště objektů BLOB v Azure pro spravovanou instanci Azure SQL pomocí PowerShellu.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 04/29/2020
ms.openlocfilehash: a5a2ff85395a55bcd4e8405e2eb60c6a4645818c
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833435"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Správa dlouhodobého uchovávání záloh spravované instance Azure SQL (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ve spravované instanci Azure SQL můžete nakonfigurovat [dlouhodobé zásady uchovávání záloh](../database/long-term-retention-overview.md#sql-managed-instance-support) (LTR) jako omezené funkce Public Preview. Díky tomu můžete automaticky uchovávat zálohy databáze v samostatných kontejnerech úložiště objektů BLOB v Azure po dobu až 10 let. Pak můžete obnovit databázi pomocí těchto záloh pomocí PowerShellu.

   > [!IMPORTANT]
   > LTR pro spravované instance je aktuálně ve verzi omezené verze Preview a je k dispozici pro předplatné EA a CSP v případě jednotlivých případů. Pokud chcete požádat o registraci, vytvořte prosím [lístek podpory Azure](https://azure.microsoft.com/support/create-ticket/). Pro typ problému vyberte technický problém, u služby zvolte SQL Database spravovaná instance a pro typ problému vyberte **zálohování, obnovení a kontinuitu podnikových/dlouhodobého uchovávání záloh**. Ve vaší žádosti prosím uveďte stav, který chcete zaregistrovat pro spravovanou instanci v části omezené verze Public Preview LTR.

V následujících částech se dozvíte, jak pomocí PowerShellu nakonfigurovat dlouhodobé uchovávání záloh, zobrazit zálohy ve službě Azure SQL Storage a obnovit je ze zálohy ve službě Azure SQL Storage.

## <a name="azure-roles-to-manage-long-term-retention"></a>Role Azure pro správu dlouhodobého uchovávání

Pro **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** a **Restore-AzSqlInstanceDatabase** budete muset mít jednu z následujících rolí:

- Role vlastníka předplatného nebo
- Role přispěvatele spravované instance nebo
- Vlastní role s následujícími oprávněními:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

V případě funkce **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup** budete muset mít jednu z následujících rolí:

- Role vlastníka předplatného nebo
- Vlastní role s následujícím oprávněním:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Role Přispěvatel spravované instance nemá oprávnění odstraňovat zálohy LTR.

Oprávnění Azure RBAC se mohla udělit buď v rámci *předplatného* , nebo v oboru *skupiny prostředků* . Pro přístup k zálohám LTR, které patří do vyřazené instance, ale musí být oprávnění uděleno v oboru *předplatného* této instance.

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
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

## <a name="view-ltr-policies"></a>Zobrazit zásady LTR

Tento příklad ukazuje, jak zobrazit seznam zásad LTR v rámci instance pro izolovanou databázi.

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

Tento příklad ukazuje, jak zobrazit seznam zásad LTR pro všechny databáze v instanci.

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

## <a name="clear-an-ltr-policy"></a>Vymazat zásadu LTR

Tento příklad ukazuje, jak vymazat zásadu LTR z databáze.

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

## <a name="view-ltr-backups"></a>Zobrazit zálohy LTR

Tento příklad ukazuje, jak zobrazit seznam záloh LTR v rámci instance.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

## <a name="delete-ltr-backups"></a>Odstranit zálohy LTR

Tento příklad ukazuje, jak odstranit zálohu LTR ze seznamu záloh.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Odstranění zálohy LTR je nevratné. Pokud chcete odstranit zálohu LTR po odstranění instance, musíte mít oprávnění k oboru předplatného. Můžete nastavit oznámení o každém odstranění v Azure Monitor filtrováním pro operaci odstraní zálohu pro dlouhodobé uchovávání. Protokol aktivit obsahuje informace o tom, kdo a kdy žádost odeslal. Podrobné pokyny najdete v tématu [vytvoření výstrah protokolu aktivit](../../azure-monitor/platform/alerts-activity-log.md) .

## <a name="restore-from-ltr-backups"></a>Obnovení ze zálohy LTR

Tento příklad ukazuje, jak obnovit ze zálohy LTR. Všimněte si, že toto rozhraní se nezměnilo, ale parametr ID prostředku teď vyžaduje ID záložního prostředku LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> Pro obnovení ze zálohy LTR po odstranění instance musíte mít oprávnění oboru pro předplatné instance a toto předplatné musí být aktivní. Je také nutné vynechat volitelný parametr-ResourceGroupName.

> [!NOTE]
> Odtud se můžete pomocí aplikace SQL Server Management Studio připojit k obnovené databázi a provádět požadované úlohy, jako je například extrakce části dat z obnovené databáze a zkopírování do existující databáze nebo odstranění existující databáze a přejmenování obnovené databáze na název existující databáze. Viz [Obnovení bodu v čase](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Další kroky

- Další informace o automatických zálohách generovaných službou najdete u popisu [automatických záloh](../database/automated-backups-overview.md).
- Další informace o dlouhodobém uchovávání záloh najdete v části [dlouhodobé uchovávání záloh](../database/long-term-retention-overview.md)
