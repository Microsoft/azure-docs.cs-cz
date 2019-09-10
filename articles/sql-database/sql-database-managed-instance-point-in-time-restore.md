---
title: SQL Database obnovení spravované instance – bod v čase | Microsoft Docs
description: Postup obnovení databáze ve spravované instanci SQL k předchozímu bodu v čase.
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
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862132"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>Obnovení databáze spravované instance SQL k předchozímu bodu v čase

Obnovení k určitému bodu v čase (PITR) umožňuje v minulosti vytvořit databázi jako kopii jiné databáze v určitém okamžiku. Tento článek popisuje, jak provést obnovení databáze v určitém bodě v čase ve spravované instanci.

Obnovení k bodu v čase lze použít ve scénářích obnovení, jako jsou incidenty způsobené chybami, nesprávně načtená data, odstranění důležitých dat a další problémy, a to jednoduše pro účely testování nebo auditování. V závislosti na nastavení databáze se záložní soubory uchovávají po dobu mezi 7 a 35 dny.

Obnovení k bodu v čase lze použít k těmto akcím:

- Obnovte databázi z existující databáze.
- Obnovte databázi z odstraněné databáze.

Pomocí spravované instance se navíc dá obnovení k určitému bodu v čase použít k těmto akcím: 

- Obnovte databázi do stejné spravované instance.
- Obnovte databázi do jiné spravované instance.


> [!NOTE]
> Obnovení celé spravované instance z určitého bodu v čase není možné. Co je možné a vysvětluje v tomto článku, je obnovení databáze hostované na spravované instanci v určitém bodě v čase.


## <a name="limitations"></a>Omezení

Při obnovení do jiné spravované instance musí být obě instance ve stejném předplatném a oblasti. Obnovení mezi oblastmi a mezi předplatnými se v současnosti nepodporují.

> [!WARNING]
> Buďte opatrní s velikostí úložiště vaší spravované instance – v závislosti na velikosti obnovy dat můžete vycházet z úložiště instance. Pokud není dostatek místa pro obnovená data, použijte alternativní přístup.

Následující tabulka uvádí scénáře obnovení k bodu v čase pro spravovanou instanci:

|           |Obnovit existující databázi| Obnovit existující databázi|Obnovit odloženou databázi| Obnovit odloženou databázi|
|:----------|:----------|:----------|:----------|:----------|
|Cíl| Stejný MI|Další MI |Stejný MI|Další MI |
|portál Azure| Ano|Ne |Ne|Ne|
|Azure CLI|Ano |Ano |Ne|Ne|
|PowerShell| Ano|Ano |Ano|Ano|


## <a name="restore-existing-database"></a>Obnovit existující databázi

Obnovte existující databázi do stejné instance pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI. Obnovte databázi do jiné instance pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI zadáním vlastnosti cílová spravovaná instance a skupiny prostředků. Nejsou-li tyto parametry zadány, bude ve výchozím nastavení databáze obnovena do existující instance. Obnovení do jiné instance se v současnosti nepodporují prostřednictvím Azure Portal. 

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Přejděte do spravované instance a vyberte databázi, kterou chcete obnovit. 
1. Na stránce databáze vyberte **obnovit** . 

    ![Obnovit existující databázi](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. Na stránce **obnovení** vyberte bod pro datum a čas v historii, do které chcete databázi obnovit.
1. Vyberte **Potvrdit** pro obnovení databáze. Tím se spustí proces obnovení, který vytvoří novou databázi a v požadovaném časovém okamžiku se naplní daty z původní databáze. Další informace o procesu obnovení najdete v tématu [čas obnovení](sql-database-recovery-using-backups.md#recovery-time). 

1. Najít spravovanou instanci
1. Vyberte databázi, kterou chcete obnovit.
1. Na obrazovce databáze klikněte na obnovit akci.
1. Na obrazovce obnovit vyberte datum a čas bodu v historii, na kterou obnovujete databázi.
1. Po ověření se spustí proces obnovení a v závislosti na velikosti databáze se vytvoří nová databáze a naplní se daty z původní databáze v požadovaném časovém okamžiku. Pro dobu trvání procesu obnovení proveďte obnovení pomocí článku zálohování.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud ještě nemáte nainstalované Azure PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Chcete-li obnovit databázi pomocí prostředí PowerShell, aktualizujte parametry pomocí vašich hodnot a spusťte následující příkaz:

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

Chcete-li obnovit databázi do jiné spravované instance, nastavte název cílové skupiny prostředků a název cílové spravované instance.  

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

Chcete-li obnovit databázi pomocí rozhraní příkazového řádku Azure, aktualizujte parametry pomocí vašich hodnot a spusťte následující příkaz:


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


Chcete-li obnovit databázi do jiné spravované instance, nastavte název cílové skupiny prostředků a název cílové spravované instance.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Podrobné vysvětlení dostupných parametrů najdete v tématu [Managed instance CLI](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore). 

---

## <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze 
 
Obnovení odstraněné databáze se dá udělat jenom s PowerShellem. Databázi lze obnovit do stejné instance nebo jiné instance. 

Chcete-li obnovit odstraněnou databázi pomocí prostředí PowerShell, aktualizujte parametry pomocí vašich hodnot a spusťte následující příkaz:

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

Chcete-li obnovit odstraněnou databázi do jiné instance, změňte název skupiny prostředků a název spravované instance.

Parametr Location by se měl shodovat s umístěním skupiny prostředků a spravované instance.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>Přepsat existující databázi 
 
Chcete-li přepsat existující databázi, je nutné také:

1. Vyřaďte existující databázi, kterou chcete přepsat.
1. Přejmenujte obnovenou databázi bodu v čase na název databáze, která byla vyřazena. 


### <a name="drop-original-database"></a>ZRUŠIT původní databázi 
 
Vyřazení databáze se dá udělat pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI. 

Databázi můžete také vyřadit pomocí připojení ke spravované instanci přímo, spuštěním SQL Server Management Studio (SSMS) a spuštěním níže uvedeného příkazu jazyka Transact-SQL (T-SQL).

```sql
DROP DATABASE WorldWideImporters;
```

K připojení k databázi spravované instance použijte jednu z následujících metod: 

- [Virtuální počítač SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Point-to-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Veřejný koncový bod](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

V Azure Portal vyberte databázi ze spravované instance a vyberte **Odstranit**.

   ![Obnovit existující databázi](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

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


### <a name="alter-new-database-name-to-original"></a>ZMĚNIT název nové databáze na původní

Připojte se přímo ke spravované instanci, spusťte SQL Server Management Studio a pak spusťte následující dotaz Transact-SQL (T-SQL), abyste změnili název obnovené databáze na databázi, kterou jste chtěli přepsat. 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


K připojení k databázi spravované instance použijte jednu z následujících metod: 

- [Virtuální počítač SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Point-to-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Veřejný koncový bod](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Další postup

Přečtěte si o [dlouhodobém uchovávání](sql-database-long-term-retention.md) a [automatizovaném zálohování](sql-database-automated-backups.md). 
