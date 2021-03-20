---
title: Správa prostředků rozhraní API pro Azure Cosmos DB Core (SQL) API pomocí prostředí PowerShell
description: Spravujte prostředky rozhraní API pro Azure Cosmos DB Core (SQL) API pomocí prostředí PowerShell.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 2d99a760e3bf44febcee05bf9827479616bf7bd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93333422"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>Správa prostředků rozhraní API pro Azure Cosmos DB Core (SQL) pomocí PowerShellu
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Následující příručka popisuje použití prostředí PowerShell ke skriptování a automatizaci správy prostředků rozhraní API pro Azure Cosmos DB Core (SQL), včetně účtu Cosmos, databáze, kontejneru a propustnosti. Rutiny PowerShellu pro další rozhraní API najdete v tématu [ukázky PowerShellu pro Cassandra](powershell-samples-cassandra.md), ukázky PowerShellu pro [MongoDB API](powershell-samples-mongodb.md), ukázky PowerShellu pro Gremlin, [ukázky](powershell-samples-table.md) PowerShellu pro [](powershell-samples-gremlin.md)

> [!NOTE]
> Ukázky v tomto článku využívají rutiny [AZ. CosmosDB](/powershell/module/az.cosmosdb) Management. Nejnovější změny najdete na stránce s referenční stránkou [AZ. CosmosDB](/powershell/module/az.cosmosdb) API.

Pro správu Azure Cosmos DB pro různé platformy můžete použít `Az` `Az.CosmosDB` rutiny a s [prostředím PowerShell pro různé platformy](/powershell/scripting/install/installing-powershell)a také rozhraní příkazového [řádku Azure CLI](manage-with-cli.md), [REST API][rp-rest-api]nebo [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Začínáme

Postupujte podle pokynů v tématu [instalace a konfigurace Azure PowerShell][powershell-install-configure] pro instalaci a přihlášení ke svému účtu Azure v prostředí PowerShell.

> [!IMPORTANT]
> Prostředky Azure Cosmos DB nelze přejmenovat, protože jsou v rozporu s tím, jak Azure Resource Manager pracuje s identifikátory URI prostředků.

## <a name="azure-cosmos-accounts"></a>Účty Azure Cosmos

Následující části demonstrují, jak spravovat účet Azure Cosmos, včetně:

* [Vytvoření účtu Azure Cosmos](#create-account)
* [Aktualizace účtu Azure Cosmos](#update-account)
* [Výpis všech účtů Azure Cosmos v předplatném](#list-accounts)
* [Získání účtu Azure Cosmos](#get-account)
* [Odstranění účtu Azure Cosmos](#delete-account)
* [Aktualizace značek pro účet Azure Cosmos](#update-tags)
* [Seznam klíčů pro účet Azure Cosmos](#list-keys)
* [Znovu vygenerovat klíče pro účet Azure Cosmos](#regenerate-keys)
* [Výpis připojovacích řetězců pro účet Azure Cosmos](#list-connection-strings)
* [Úprava priority převzetí služeb při selhání pro účet Azure Cosmos](#modify-failover-priority)
* [Aktivace ručního převzetí služeb při selhání pro účet Azure Cosmos](#trigger-manual-failover)
* [Vypsat zámky prostředků na Azure Cosmos DB účtu](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> Vytvoření účtu Azure Cosmos

Tento příkaz vytvoří účet databáze Azure Cosmos DB s [více oblastmi][distribute-data-globally], [automatickým převzetím služeb při selhání](how-to-manage-database-account.md#automatic-failover) a [zásadami konzistence](consistency-levels.md)vázaných na zastaralé.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName` Skupina prostředků Azure, do které se má účet Cosmos nasadit Už musí existovat.
* `$locations` Oblasti pro databázový účet, oblast s, `FailoverPriority 0` je oblast zápisu.
* `$accountName` Název účtu Azure Cosmos. Musí být jedinečné, malá a velká písmena, obsahovat pouze alfanumerické znaky a znaky "-" a délku 3 až 31 znaků.
* `$apiKind` Typ Cosmos účtu, který se má vytvořit Další informace najdete v tématu [rozhraní API v Cosmos DB](introduction.md#simplified-application-development).
* `$consistencyPolicy`, `$maxStalenessInterval` a `$maxStalenessPrefix` výchozí úroveň konzistence a nastavení účtu Azure Cosmos. Další informace najdete v tématu [úrovně konzistence v Azure Cosmos DB](consistency-levels.md).

Účty Azure Cosmos se dají nakonfigurovat pomocí brány firewall protokolu IP, Virtual Networkch koncových bodů služby a privátních koncových bodů. Informace o tom, jak nakonfigurovat bránu firewall protokolu IP pro Azure Cosmos DB, najdete v tématu [Konfigurace brány firewall protokolu IP](how-to-configure-firewall.md). Informace o tom, jak povolit koncové body služby pro Azure Cosmos DB, najdete v tématu [Konfigurace přístupu z virtuálních sítí](how-to-configure-vnet-service-endpoint.md). Informace o povolení privátních koncových bodů pro Azure Cosmos DB najdete v tématu [Konfigurace přístupu z privátních koncových bodů](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> Vypsat všechny účty Azure Cosmos ve skupině prostředků

Tento příkaz vypíše všechny účty Azure Cosmos ve skupině prostředků.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> Získání vlastností účtu Azure Cosmos

Tento příkaz umožňuje získat vlastnosti stávajícího účtu Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Aktualizace účtu Azure Cosmos

Tento příkaz umožňuje aktualizovat vlastnosti účtu databáze Azure Cosmos DB. Mezi vlastnosti, které lze aktualizovat, patří následující:

* Přidávání nebo odebírání oblastí
* Mění se výchozí zásada konzistence.
* Změna filtru rozsahu IP adres
* Změna konfigurace Virtual Network
* Povolení zápisů ve více oblastech

> [!NOTE]
> Nemůžete současně přidat ani odebrat oblasti ( `locations` ) a změnit další vlastnosti pro účet Azure Cosmos. Úprava oblastí se musí provádět jako samostatná operace z jakékoli jiné změny účtu.
> [!NOTE]
> Tento příkaz umožňuje přidat a odebrat oblasti, ale neumožňuje měnit priority převzetí služeb při selhání ani aktivovat ruční převzetí služeb při selhání. Viz [Upravit prioritu převzetí služeb při selhání](#modify-failover-priority) a [aktivovat ruční převzetí služeb při selhání](#trigger-manual-failover)

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a> Povolení více oblastí zápisu pro účet Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Odstranění účtu Azure Cosmos

Tento příkaz odstraní existující účet Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Aktualizovat značky účtu Azure Cosmos

Tento příkaz nastaví [značky prostředků Azure][azure-resource-tags] pro účet Azure Cosmos. Značky je možné nastavit jak při vytvoření účtu `New-AzCosmosDBAccount` , tak i na použití aktualizace účtu pomocí `Update-AzCosmosDBAccount` .

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> Výpis klíčů účtu

Když vytvoříte účet Azure Cosmos, vygeneruje služba dva primární přístupové klíče, které se dají použít k ověřování při přístupu k účtu Azure Cosmos. Vygenerují se taky klíče jen pro čtení pro ověřování operací jen pro čtení.
Po poskytnutí dvou přístupových klíčů vám Azure Cosmos DB umožňuje znovu vygenerovat a otočit jeden klíč, a to bez přerušení pro váš účet Azure Cosmos.
Účty Cosmos DB mají dva klíče pro čtení i zápis (primární a sekundární) a dva klíče jen pro čtení (primární a sekundární).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> Vypsat připojovací řetězce

Následující příkaz načte připojovací řetězce pro připojení aplikací k účtu Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> Znovu vygenerovat klíče účtu

Přístup ke klíčům k účtu Azure Cosmos by se měl pravidelně znovu vygenerovat, aby se připojení zajistila v bezpečí. K účtu se přiřadí primární a sekundární přístupové klíče. To umožňuje klientům zachovat přístup v době, kdy se jeden klíč najednou vygeneruje.
Existují čtyři typy klíčů pro účet Azure Cosmos (primární, sekundární, PrimaryReadonly a SecondaryReadonly).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> Povolit automatické převzetí služeb při selhání

Následující příkaz nastaví účet Cosmos DB pro automatické převzetí služeb při selhání do sekundární oblasti v případě, že primární oblast nebude k dispozici.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> Úprava priority převzetí služeb při selhání

U účtů konfigurovaných s automatickým převzetím služeb při selhání můžete změnit pořadí, ve kterém bude Cosmos povýšit sekundární repliky na primární, pokud primární databáze není k dispozici.

V následujícím příkladu Předpokládejme, že aktuální priorita převzetí služeb při selhání je `West US 2 = 0` , `East US 2 = 1` , `South Central US = 2` . Příkaz tuto změnu provede na `West US 2 = 0` , `South Central US = 1` , `East US 2 = 2` .

> [!CAUTION]
> Změna umístění pro spustí `failoverPriority=0` ruční převzetí služeb při selhání pro účet Azure Cosmos. Jakékoli další změny priority nebudou aktivovat převzetí služeb při selhání.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> Aktivace ručního převzetí služeb při selhání

U účtů konfigurovaných pomocí ručního převzetí služeb při selhání můžete převzít služby při selhání a zvýšit úroveň sekundární repliky na primární úpravou `failoverPriority=0` . Tato operace se dá použít k inicializaci plánování zotavení po havárii při zotavení po havárii.

V následujícím příkladu Předpokládejme, že účet má aktuální prioritu převzetí služeb při selhání pro `West US 2 = 0` a `East US 2 = 1` a překlopit oblasti.

> [!CAUTION]
> Změna `locationName` pro spustí `failoverPriority=0` ruční převzetí služeb při selhání pro účet Azure Cosmos. Žádná jiná změna priority nebude aktivovat převzetí služeb při selhání.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> Vypsat zámky prostředků na Azure Cosmos DB účtu

Zámky prostředků se dají umístit na prostředky Azure Cosmos DB, včetně databází a kolekcí. Následující příklad ukazuje, jak zobrazit seznam všech zámků prostředků Azure na účtu Azure Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB databáze

Následující části demonstrují, jak spravovat databázi Azure Cosmos DB, včetně:

* [Vytvoření databáze Azure Cosmos DB](#create-db)
* [Vytvoření databáze Azure Cosmos DB se sdílenou propustností](#create-db-ru)
* [Získání propustnosti Azure Cosmos DB databáze](#get-db-ru)
* [Migrace propustnosti databáze do automatického škálování](#migrate-db-ru)
* [Výpis všech Azure Cosmos DB databází v účtu](#list-db)
* [Získat jednu Azure Cosmos DB databázi](#get-db)
* [Odstranění databáze Azure Cosmos DB](#delete-db)
* [Vytvoření zámku prostředku na Azure Cosmos DB databázi, aby se zabránilo odstranění](#create-db-lock)
* [Odebrání zámku prostředku na Azure Cosmos DB databázi](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Vytvoření databáze Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Vytvoření databáze Azure Cosmos DB se sdílenou propustností

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Získání propustnosti Azure Cosmos DB databáze

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>Migrace propustnosti databáze do automatického škálování

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Získání všech Azure Cosmos DB databází v účtu

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Získat jednu Azure Cosmos DB databázi

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Odstranění databáze Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>Vytvoření zámku prostředku na Azure Cosmos DB databázi, aby se zabránilo odstranění

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a>Odebrání zámku prostředku na Azure Cosmos DB databázi

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB kontejner

Následující části ukazují, jak spravovat Azure Cosmos DB kontejner, včetně:

* [Vytvoření kontejneru Azure Cosmos DB](#create-container)
* [Vytvoření kontejneru Azure Cosmos DB s AutoScale](#create-container-autoscale)
* [Vytvoření kontejneru Azure Cosmos DB s velkým klíčem oddílu](#create-container-big-pk)
* [Získání propustnosti Azure Cosmos DB kontejneru](#get-container-ru)
* [Migrace propustnosti kontejneru do automatického škálování](#migrate-container-ru)
* [Vytvoření kontejneru Azure Cosmos DB s vlastním indexováním](#create-container-custom-index)
* [Vytvoření kontejneru Azure Cosmos DB s vypnutým indexováním](#create-container-no-index)
* [Vytvoření kontejneru Azure Cosmos DB s jedinečným klíčem a hodnotou TTL](#create-container-unique-key-ttl)
* [Vytvoření kontejneru Azure Cosmos DB s řešením konfliktů](#create-container-lww)
* [Vypsat všechny kontejnery Azure Cosmos DB v databázi](#list-containers)
* [Získání jednoho Azure Cosmos DB kontejneru v databázi](#get-container)
* [Odstranění kontejneru Azure Cosmos DB](#delete-container)
* [Vytvoření zámku prostředku na kontejneru Azure Cosmos DB, aby se zabránilo odstranění](#create-container-lock)
* [Odebrání zámku prostředků na Azure Cosmos DB kontejneru](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Vytvoření kontejneru Azure Cosmos DB

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>Vytvoření kontejneru Azure Cosmos DB s AutoScale

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Vytvoření kontejneru Azure Cosmos DB s velkou velikostí klíče oddílu

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Získání propustnosti Azure Cosmos DB kontejneru

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>Migrace propustnosti kontejneru do automatického škálování

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Vytvoření kontejneru Azure Cosmos DB s vlastními zásadami indexů

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Vytvoření kontejneru Azure Cosmos DB s vypnutým indexováním

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Vytvoření kontejneru Azure Cosmos DB s jedinečnou klíčovou zásadou a hodnotou TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Vytvoření kontejneru Azure Cosmos DB s řešením konfliktů

Pro zápis všech konfliktů do ConflictsFeed a zpracování zvlášť, Pass `-Type "Custom" -Path ""` .

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

Chcete-li vytvořit zásadu řešení konfliktů pro použití uložené procedury, zavolejte `New-AzCosmosDBSqlConflictResolutionPolicy` a předejte parametry `-Type` a `-ConflictResolutionProcedure` .

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Vypsat všechny kontejnery Azure Cosmos DB v databázi

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Získání jednoho Azure Cosmos DB kontejneru v databázi

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Odstranění kontejneru Azure Cosmos DB

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>Vytvoření zámku prostředku na kontejneru Azure Cosmos DB, aby se zabránilo odstranění

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>Odebrání zámku prostředků na Azure Cosmos DB kontejneru

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>Další kroky

* [Všechny ukázky PowerShellu](powershell-samples.md)
* [Jak spravovat účet Azure Cosmos](how-to-manage-database-account.md)
* [Vytvoření kontejneru Azure Cosmos DB](how-to-create-container.md)
* [Konfigurace času na živé v Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell/azure/
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: ../azure-resource-manager/management/overview.md#resource-groups
[azure-resource-tags]: ../azure-resource-manager/management/tag-resources.md
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/