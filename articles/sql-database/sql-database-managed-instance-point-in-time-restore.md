---
title: Spravovaná instance – obnovení v čase (PITR)
description: Obnovte databázi SQL ve spravované instanci do předchozího bodu v čase.
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
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268805"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Obnovení databáze SQL ve spravované instanci do předchozího bodu v čase

Pomocí obnovení v čase (PITR) vytvořit databázi jako kopii jiné databáze z nějakého času v minulosti. Tento článek popisuje, jak provést obnovení databáze v čase ve spravované instanci Azure SQL Database.

Obnovení bodu v čase je užitečné ve scénářích obnovení, jako jsou incidenty způsobené chybami, nesprávně načtená data nebo odstranění důležitých dat. Můžete jej také použít jednoduše pro testování nebo auditování. Záložní soubory jsou uchovávány po dobu 7 až 35 dnů, v závislosti na nastavení databáze.

Obnovení v čase může obnovit databázi:

- z existující databáze.
- z odstraněné databáze.
- stejné spravované instanci nebo na jinou spravovanou instanci. 

## <a name="limitations"></a>Omezení

Obnovení spravované instance v čase má následující omezení:

- Při obnovení z jedné spravované instance do druhé, obě instance musí být ve stejném předplatném a oblasti. Obnovení mezi oblastmi a mezi předplatnými nejsou momentálně podporovány.
- Obnovení celé spravované instance v čase není možné. Tento článek vysvětluje pouze to, co je možné: bod v čase obnovení databáze, která je hostovaná na spravované instanci.

> [!WARNING]
> Mějte na paměti velikost úložiště spravované instance. V závislosti na velikosti dat, která mají být obnovena, může dojít k vyčerpání úložiště instancí. Pokud není dostatek místa pro obnovená data, použijte jiný přístup.

V následující tabulce jsou uvedeny scénáře obnovení v čase pro spravované instance:

|           |Obnovení existující databáze na stejnou spravovanou instanci| Obnovení existující databáze do jiné spravované instance|Obnovení vynechání DB na stejnou spravovanou instanci|Obnovení vynechání DB na jinou spravovanou instanci|
|:----------|:----------|:----------|:----------|:----------|
|**Portál Azure**| Ano|Ne |Ano|Ne|
|**Azure CLI**|Ano |Ano |Ne|Ne|
|**PowerShell**| Ano|Ano |Ano|Ano|

## <a name="restore-an-existing-database"></a>Obnovení existující databáze

Obnovte existující databázi do stejné instance pomocí portálu Azure, PowerShellu nebo příkazového příkazového příkazu k webu Azure. Chcete-li obnovit databázi do jiné instance, použijte PowerShell nebo Azure CLI, takže můžete zadat vlastnosti pro cílovou spravovanou instanci a skupinu prostředků. Pokud tyto parametry nezadáte, bude databáze ve výchozím nastavení obnovena na existující instanci. Portál Azure momentálně nepodporuje obnovení do jiné instance.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. Přejděte na spravovanou instanci a vyberte databázi, kterou chcete obnovit.
3. Na stránce databáze vyberte **Obnovit:**

    ![Obnovení databáze pomocí portálu Azure](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Na stránce **Obnovení** vyberte bod pro datum a čas, do kterého chcete databázi obnovit.
5. Chcete-li databázi obnovit, vyberte **potvrdit.** Tato akce spustí proces obnovení, který vytvoří novou databázi a naplní ji daty z původní databáze v zadaném okamžiku. Další informace o procesu obnovení naleznete v tématu [Doba obnovení](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud ještě nemáte nainstalovaný Azure PowerShell, [přečtěte si tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Chcete-li obnovit databázi pomocí prostředí PowerShell, zadejte hodnoty parametrů v následujícím příkazu. Potom spusťte příkaz:

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

Chcete-li obnovit databázi do jiné spravované instance, zadejte také názvy cílové skupiny prostředků a cílové spravované instance:  

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

Podrobnosti naleznete v [tématu Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud ještě nemáte nainstalované vázané příkazové k vodítku azure, přečtěte si informace [o instalaci příkazového příkazu k azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Chcete-li obnovit databázi pomocí rozhraní příkazového řádku Azure, zadejte hodnoty pro parametry v následujícím příkazu. Potom spusťte příkaz:

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

Podrobné vysvětlení dostupných parametrů naleznete v [dokumentaci příkazového řádku pro obnovení databáze ve spravované instanci](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze

Obnovení odstraněné databáze lze provést pomocí PowerShellu nebo portálu Azure. Chcete-li obnovit odstraněnou databázi do stejné instance, použijte buď portál Azure nebo PowerShell. Chcete-li obnovit odstraněnou databázi do jiné instance, použijte PowerShell. 

### <a name="portal"></a>Portál 


Chcete-li obnovit spravovanou databázi pomocí portálu Azure, otevřete stránku přehledu spravovaných **instancí**a vyberte Odstraněná databáze . Zvolte odstraněnou databázi, kterou chcete obnovit, a zadejte název nové databáze, která bude vytvořena s daty obnovenými ze zálohy.

  ![Snímek obrazovky s databází odstraněných instancí Azure SQL](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Chcete-li obnovit databázi do stejné instance, aktualizujte hodnoty parametrů a spusťte následující příkaz prostředí PowerShell: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
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

Chcete-li obnovit databázi do jiné spravované instance, zadejte také názvy cílové skupiny prostředků a cílové spravované instance:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Přepsání existující databáze

Chcete-li přepsat existující databázi, je nutné:

1. Přetáhněte existující databázi, kterou chcete přepsat.
2. Přejmenujte databázi s obnovením bodu v čase na název databáze, kterou jste vypustili.

### <a name="drop-the-original-database"></a>Přetažení původní databáze

Databázi můžete přetáhnout pomocí portálu Azure, PowerShellu nebo azure cli.

Databázi můžete také přetáhnout přímým připojením ke spravované instanci, spuštěním sql server management studia (SSMS) a spuštěním následujícího příkazu Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Pro připojení k databázi ve spravované instanci použijte jednu z následujících metod:

- [SSMS/Azure Data Studio přes virtuální počítač Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Bod na pracoviště](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Veřejný koncový bod](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Portál](#tab/azure-portal)

Na webu Azure Portal vyberte databázi ze spravované instance a pak vyberte **Odstranit**.

   ![Odstranění databáze pomocí portálu Azure](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pomocí následujícího příkazu Prostředí PowerShell přetáhněte existující databázi ze spravované instance:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí následujícího příkazu Azure CLI přetáhněte existující databázi ze spravované instance:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Změna názvu nové databáze tak, aby odpovídal původnímu názvu databáze

Připojte se přímo ke spravované instanci a spusťte SQL Server Management Studio. Potom spusťte následující dotaz Transact-SQL (T-SQL). Dotaz změní název obnovené databáze na název vyřazené databáze, kterou chcete přepsat.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Pro připojení k databázi ve spravované instanci použijte jednu z následujících metod:

- [Virtuální počítač Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Bod na pracoviště](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Veřejný koncový bod](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Další kroky

Informace o [automatických zálohách](sql-database-automated-backups.md).
