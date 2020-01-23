---
title: Spravovaná instance – obnovení k bodu v čase
description: Obnovte databázi SQL ve spravované instanci k předchozímu bodu v čase.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 9ed694ec524c4e3e033c3139735e8e079141ec4a
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515118"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Obnovení databáze SQL ve spravované instanci k předchozímu bodu v čase

Použijte obnovení k určitému bodu v čase (PITR) k vytvoření databáze jako kopie jiné databáze z nějaké doby v minulosti. Tento článek popisuje, jak provést obnovení databáze k určitému bodu v čase v Azure SQL Database spravované instanci.

Obnovení k bodu v čase je užitečné ve scénářích obnovení, jako jsou incidenty způsobené chybami, nesprávně načtená data nebo mazání důležitých dat. Můžete ho také použít pro testování nebo auditování. Záložní soubory se uchovávají 7 až 35 dní v závislosti na nastaveních databáze.

Obnovení k bodu v čase může:

- Obnovte databázi z existující databáze.
- Obnovte databázi z odstraněné databáze.

Pro spravovanou instanci může obnovení k určitému bodu v čase také provádět tyto akce:

- Obnovte databázi do stejné spravované instance.
- Obnovte databázi do jiné spravované instance.

> [!NOTE]
> Obnovení celé spravované instance z určitého bodu v čase není možné. Tento článek vysvětluje jenom to, co je možné: obnovení databáze, která je hostovaná na spravované instanci, v určitém časovém bodě.

## <a name="limitations"></a>Omezení

Když obnovujete z jedné spravované instance na jinou, obě instance musí být ve stejném předplatném a oblasti. Obnovení mezi různými oblastmi a mezi předplatnými není aktuálně podporováno.

> [!WARNING]
> Pamatujte na velikost úložiště vaší spravované instance. V závislosti na velikosti dat, která mají být obnovena, je možné, že dojde k vyzkoušení instance úložiště. Pokud není dostatek místa pro obnovená data, použijte jiný přístup.

Následující tabulka uvádí scénáře obnovení k bodu v čase pro spravované instance:

|           |Obnovit existující databázi do stejné spravované instance| Obnovit existující databázi do jiné spravované instance|Obnovit vyřazenou databázi do stejné spravované instance|Obnovit vyřazenou databázi do jiné spravované instance|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portal**| Ano|Ne |Ne|Ne|
|**Azure CLI**|Ano |Ano |Ne|Ne|
|**PowerShell**| Ano|Ano |Ano|Ano|

## <a name="restore-an-existing-database"></a>Obnovení existující databáze

Obnovte stávající databázi do stejné instance pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI. K obnovení databáze do jiné instance použijte PowerShell nebo Azure CLI, abyste mohli zadat vlastnosti cílové spravované instance a skupiny prostředků. Pokud tyto parametry nezadáte, databáze bude ve výchozím nastavení obnovena do existující instance. Azure Portal v současné době nepodporuje obnovení do jiné instance.

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k [Portálu Azure](https://portal.azure.com). 
2. Přejít do spravované instance a vybrat databázi, kterou chcete obnovit.
3. Na stránce databáze vyberte **obnovit** :

    ![Obnovení databáze pomocí Azure Portal](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Na stránce **obnovit** vyberte bod pro datum a čas, do kterého chcete databázi obnovit.
5. Vyberte **Potvrdit** pro obnovení databáze. Tato akce spustí proces obnovení, který vytvoří novou databázi a naplní ji daty z původní databáze v určeném časovém okamžiku. Další informace o procesu obnovení najdete v tématu [čas obnovení](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud ještě nemáte nainstalované Azure PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Chcete-li obnovit databázi pomocí prostředí PowerShell, zadejte hodnoty pro parametry v následujícím příkazu. Pak spusťte příkaz:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Chcete-li obnovit databázi do jiné spravované instance, zadejte také názvy cílové skupiny prostředků a spravované instance:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Podrobnosti najdete v tématu [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud ještě nemáte nainstalované rozhraní příkazového řádku Azure, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Pokud chcete obnovit databázi pomocí rozhraní příkazového řádku Azure, zadejte hodnoty pro parametry v následujícím příkazu. Pak spusťte příkaz:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Chcete-li obnovit databázi do jiné spravované instance, zadejte také názvy cílové skupiny prostředků a spravované instance:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Podrobné vysvětlení dostupných parametrů naleznete v dokumentaci k rozhraní příkazového [řádku pro obnovení databáze ve spravované instanci](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze

Obnovení odstraněné databáze se dá provést pomocí PowerShellu nebo webu Azure Portal. k tomu prosím použijte tento dokument na webu [Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#managed-instance-database-1). Databázi lze obnovit do stejné instance nebo jiné instance.

Chcete-li obnovit odstraněnou databázi pomocí prostředí PowerShell, zadejte hodnoty parametrů do následujícího příkazu. Pak spusťte příkaz:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

Chcete-li obnovit odstraněnou databázi do jiné instance, změňte názvy skupiny prostředků a spravované instance. Také se ujistěte, že parametr Location odpovídá umístění skupiny prostředků a spravované instance.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-an-existing-database"></a>Přepsat existující databázi

Chcete-li přepsat existující databázi, je nutné:

1. Vyřaďte existující databázi, kterou chcete přepsat.
2. Přejmenujte obnovenou databázi bodu v čase na název databáze, kterou jste přetáhli.

### <a name="drop-the-original-database"></a>Vyřaďte původní databázi.

Databázi můžete vyřadit pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI.

Databázi můžete také vyřadit pomocí připojení ke spravované instanci přímo, spuštěním SQL Server Management Studio (SSMS) a následným spuštěním následujícího příkazu Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Pro připojení k databázi ve spravované instanci použijte jednu z následujících metod:

- [SSMS/Azure Data Studio přes virtuální počítač Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Point-to-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Veřejný koncový bod](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

V Azure Portal vyberte databázi ze spravované instance a pak vyberte **Odstranit**.

   ![Odstranění databáze pomocí Azure Portal](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

K vyřazení existující databáze ze spravované instance použijte následující příkaz prostředí PowerShell:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

K vyřazení existující databáze ze spravované instance použijte následující příkaz rozhraní příkazového řádku Azure:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Změňte název nové databáze tak, aby odpovídal původnímu názvu databáze.

Připojte se přímo ke spravované instanci a spusťte SQL Server Management Studio. Pak spusťte následující dotaz Transact-SQL (T-SQL). Dotaz změní název obnovené databáze na databázi, kterou máte v úmyslu přepsat.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Pro připojení k databázi ve spravované instanci použijte jednu z následujících metod:

- [Virtuální počítač Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Point-to-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Veřejný koncový bod](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Další kroky

Přečtěte si o [automatizovaných zálohách](sql-database-automated-backups.md).
