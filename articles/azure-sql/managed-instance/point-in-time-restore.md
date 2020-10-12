---
title: Obnovení k bodu v čase (PITR)
titleSuffix: Azure SQL Managed Instance
description: Obnovte databázi na spravované instanci Azure SQL pro předchozí bod v čase.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 9b4d0fadf157ce1eef6821ccbc32f5725aea611f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616512"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Obnovení databáze ve spravované instanci Azure SQL k předchozímu bodu v čase
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Použijte obnovení k určitému bodu v čase (PITR) k vytvoření databáze jako kopie jiné databáze z nějaké doby v minulosti. Tento článek popisuje, jak provést obnovení databáze v rámci spravované instance Azure SQL pomocí určitého bodu v čase.

Obnovení k bodu v čase je užitečné ve scénářích obnovení, jako jsou incidenty způsobené chybami, nesprávně načtená data nebo mazání důležitých dat. Můžete ho také použít pro testování nebo auditování. Záložní soubory se uchovávají 7 až 35 dní v závislosti na nastaveních databáze.

Obnovení k určitému bodu v čase může obnovit databázi:

- z existující databáze.
- z odstraněné databáze.
- do stejné spravované instance SQL nebo do jiné spravované instance SQL. 

## <a name="limitations"></a>Omezení

Obnovení bodu v čase do spravované instance SQL má následující omezení:

- Při obnovování z jedné instance spravované instance SQL do jiné musí být obě instance ve stejném předplatném a oblasti. Obnovení mezi různými oblastmi a mezi předplatnými není aktuálně podporováno.
- Obnovení celé spravované instance SQL na určitém bodu v čase není možné. Tento článek vysvětluje jenom to, co je možné: obnovení databáze, která je hostovaná na spravované instanci SQL, k určitému bodu v čase.

> [!WARNING]
> Pamatujte na velikost úložiště spravované instance SQL. V závislosti na velikosti dat, která mají být obnovena, je možné, že dojde k vyzkoušení instance úložiště. Pokud není dostatek místa pro obnovená data, použijte jiný přístup.

Následující tabulka uvádí scénáře obnovení k bodu v čase pro spravovanou instanci SQL:

|           |Obnovení existující databáze na stejnou instanci spravované instance SQL| Obnovit existující databázi do jiné spravované instance SQL|Obnovit vyřazenou databázi do stejné spravované instance SQL|Obnovit vyřazenou databázi do jiné spravované instance SQL|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portal**| Yes|No |Yes|No|
|**Azure CLI**|Yes |Yes |No|No|
|**PowerShell**| Yes|Yes |Yes|Yes|

## <a name="restore-an-existing-database"></a>Obnovení existující databáze

Obnovte stávající databázi na stejnou spravovanou instanci SQL pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure. K obnovení databáze do jiné spravované instance SQL použijte PowerShell nebo rozhraní příkazového řádku Azure CLI, abyste mohli zadat vlastnosti cílové instance SQL spravované instance a skupiny prostředků. Pokud tyto parametry nezadáte, bude ve výchozím nastavení databáze obnovena do existující spravované instance SQL. Azure Portal v současné době nepodporuje obnovení do jiné spravované instance SQL.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). 
2. Přejít do spravované instance SQL a vybrat databázi, kterou chcete obnovit.
3. Na stránce databáze vyberte **obnovit** :

    ![Obnovení databáze pomocí Azure Portal](./media/point-in-time-restore/restore-database-to-mi.png)

4. Na stránce **obnovit** vyberte bod pro datum a čas, do kterého chcete databázi obnovit.
5. Vyberte **Potvrdit** pro obnovení databáze. Tato akce spustí proces obnovení, který vytvoří novou databázi a naplní ji daty z původní databáze v určeném časovém okamžiku. Další informace o procesu obnovení najdete v tématu [čas obnovení](../database/recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud ještě nemáte nainstalované Azure PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Chcete-li obnovit databázi pomocí prostředí PowerShell, zadejte hodnoty pro parametry v následujícím příkazu. Pak spusťte příkaz:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
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

Chcete-li obnovit databázi do jiné spravované instance SQL, zadejte také názvy cílových skupin prostředků a cílové spravované instance SQL:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud ještě nemáte nainstalované rozhraní příkazového řádku Azure, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Pokud chcete obnovit databázi pomocí rozhraní příkazového řádku Azure, zadejte hodnoty pro parametry v následujícím příkazu. Pak spusťte příkaz:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Chcete-li obnovit databázi do jiné spravované instance SQL, zadejte také názvy cílové skupiny prostředků a spravované instance SQL:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Podrobné vysvětlení dostupných parametrů naleznete v dokumentaci k rozhraní [příkazového řádku pro obnovení databáze ve spravované instanci SQL](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze

Obnovení odstraněné databáze se dá provést pomocí PowerShellu nebo Azure Portal. Chcete-li obnovit odstraněnou databázi do stejné instance, použijte buď Azure Portal, nebo PowerShell. K obnovení odstraněné databáze na jinou instanci použijte PowerShell. 

### <a name="portal"></a>Portál 


Chcete-li obnovit spravovanou databázi pomocí Azure Portal, otevřete stránku Přehled spravované instance SQL a vyberte **odstraněné databáze**. Vyberte odstraněnou databázi, kterou chcete obnovit, a zadejte název nové databáze, která bude vytvořena s daty obnovenými ze zálohy.

  ![Snímek obrazovky obnovení odstraněné databáze Azure SQL instance](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

.. /.. /sql-database

### <a name="powershell"></a>PowerShell

Chcete-li obnovit databázi do stejné instance, aktualizujte hodnoty parametrů a spusťte následující příkaz prostředí PowerShell: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Chcete-li obnovit databázi do jiné spravované instance SQL, zadejte také názvy cílových skupin prostředků a cílové spravované instance SQL:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Přepsat existující databázi

Chcete-li přepsat existující databázi, je nutné:

1. Vyřaďte existující databázi, kterou chcete přepsat.
2. Přejmenujte obnovenou databázi bodu v čase na název databáze, kterou jste přetáhli.

### <a name="drop-the-original-database"></a>Vyřaďte původní databázi.

Databázi můžete vyřadit pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI.

Databázi můžete také vyřadit přímým připojením k spravované instanci SQL, spuštěním SQL Server Management Studio (SSMS) a následným spuštěním následujícího příkazu Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Pomocí jedné z následujících metod se připojte k databázi ve spravované instanci SQL:

- [SSMS/Azure Data Studio přes virtuální počítač Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Připojení typu point-to-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Veřejný koncový bod](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V Azure Portal vyberte databázi ze spravované instance SQL a pak vyberte **Odstranit**.

   ![Odstranění databáze pomocí Azure Portal](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

K vyřazení existující databáze z spravované instance SQL použijte následující příkaz prostředí PowerShell:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

K vyřazení existující databáze ze spravované instance SQL použijte následující příkaz Azure CLI:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Změňte název nové databáze tak, aby odpovídal původnímu názvu databáze.

Připojte se přímo k spravované instanci SQL a spusťte SQL Server Management Studio. Pak spusťte následující dotaz Transact-SQL (T-SQL). Dotaz změní název obnovené databáze na databázi, kterou máte v úmyslu přepsat.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Pro připojení k databázi ve spravované instanci SQL použijte jednu z následujících metod:

- [Virtuální počítač Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Připojení typu point-to-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Veřejný koncový bod](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Další kroky

Přečtěte si o [automatizovaných zálohách](../database/automated-backups-overview.md).
