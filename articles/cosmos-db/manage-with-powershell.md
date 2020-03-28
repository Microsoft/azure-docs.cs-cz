---
title: Vytváření a správa Azure Cosmos DB pomocí PowerShellu
description: Azure Powershell můžete spravovat své účty, databáze, kontejnery a propustnost Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: c8e833a4ba18520d8e354398cfd0d00525594d15
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365764"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Správa prostředků SQL API Azure Cosmos DB pomocí PowerShellu

Následující příručka popisuje, jak pomocí PowerShellu skriptovat a automatizovat správu prostředků Azure Cosmos DB, včetně účtů, databází, kontejnerů a propustnosti.

> [!NOTE]
> Ukázky v `Get-AzResource` tomto `Set-AzResource` článku použití a Rutiny Powershell pro operace prostředků Azure, stejně jako Rutiny správy [Az.CosmosDB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) `Az.CosmosDB`rutiny jsou stále ve verzi Preview a mohou se změnit dříve, než jsou obecně dostupné. Všechny aktualizace příkazů naleznete na referenční stránce rozhraní API [Az.CosmosDB.](https://docs.microsoft.com/powershell/module/az.cosmosdb)

Pokud chcete zobrazit všechny vlastnosti, `Get-Resource` / `Set-AzResource` které lze spravovat pomocí rutin prostředí PowerShell, přečtěte si článek [Schéma zprostředkovatele prostředků Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Pro správu napříč platformami Azure `Az` Cosmos `Az.CosmosDB` DB můžete použít rutiny a rutiny s [multiplatformním powershellem](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)a také [rozhraní matné rozhraní Azure](manage-with-cli.md), rozhraní REST [API][rp-rest-api]nebo [portál Azure](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Začínáme

Postupujte podle pokynů v části [Jak nainstalovat a nakonfigurovat Azure PowerShell][powershell-install-configure] pro instalaci a přihlášení k účtu Azure v PowerShellu.

* `Set-AzureResource`se používá níže. Požádá o potvrzení uživatele.  Pokud dáváte přednost spuštění bez nutnosti `-Force` potvrzení uživatele, připojte příznak k příkazu.

## <a name="azure-cosmos-accounts"></a>Účty Azure Cosmos

Následující části ukazují, jak spravovat účet Azure Cosmos, včetně:

* [Vytvoření účtu Azure Cosmos](#create-account)
* [Aktualizace účtu Azure Cosmos](#update-account)
* [Seznam všech účtů Azure Cosmos v předplatném](#list-accounts)
* [Získání účtu Azure Cosmos](#get-account)
* [Odstranění účtu Azure Cosmos](#delete-account)
* [Aktualizace značek pro účet Azure Cosmos](#update-tags)
* [Seznam klíčů pro účet Azure Cosmos](#list-keys)
* [Regenerovat klíče pro účet Azure Cosmos](#regenerate-keys)
* [Seznam připojovacích řetězců pro účet Azure Cosmos](#list-connection-strings)
* [Změna priority převzetí služeb při selhání pro účet Azure Cosmos](#modify-failover-priority)
* [Aktivace ručního převzetí služeb při selhání pro účet Azure Cosmos](#trigger-manual-failover)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Vytvoření účtu Azure Cosmos

Tento příkaz vytvoří databázový účet Azure Cosmos DB s [více oblastmi][distribute-data-globally], [automatické převzetí služeb při selhání](how-to-manage-database-account.md#automatic-failover) a zásady konzistence ohraničené neaktuárnosti . [consistency policy](consistency-levels.md)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000

New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName`Skupina prostředků Azure, do které chcete nasadit účet Cosmos. Už musí existovat.
* `$locations`Oblasti pro databázový účet, počínaje oblastí zápisu a seřazené podle priority převzetí služeb při selhání.
* `$accountName`Název účtu Azure Cosmos. Musí být jedinečný, malá písmena, obsahovat pouze alfanumerické a '-' znaky a mezi 3 a 31 znaků na délku.
* `$apiKind`Typ účtu Cosmos, který chcete vytvořit. Další informace naleznete [v tématu API v Cosmos DB](introduction.md#develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis).
* `$consistencyPolicy`, `$maxStalenessInterval`a `$maxStalenessPrefix` Výchozí úroveň konzistence a nastavení účtu Azure Cosmos. Další informace najdete [v tématu Úrovně konzistence v Azure Cosmos DB](consistency-levels.md).

Účty Azure Cosmos lze nakonfigurovat pomocí ip firewallu, koncových bodů služby Virtuální sítě a privátních koncových bodů. Informace o konfiguraci brány IP firewall pro Azure Cosmos DB naleznete v [tématu Konfigurace brány IP Firewall](how-to-configure-firewall.md). Informace o povolení koncových bodů služby pro Azure Cosmos DB najdete [v tématu Konfigurace přístupu z virtuálních sítí](how-to-configure-vnet-service-endpoint.md). Informace o tom, jak povolit privátní koncové body pro Azure Cosmos DB, [najdete v tématu Konfigurace přístupu z privátní koncové body](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a>Seznam všech účtů Azure Cosmos ve skupině prostředků

Tento příkaz obsahuje seznam všech účtů Azure Cosmos ve skupině prostředků.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a>Získání vlastností účtu Azure Cosmos

Tento příkaz umožňuje získat vlastnosti existujícího účtu Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a>Aktualizace účtu Azure Cosmos

Tento příkaz umožňuje aktualizovat vlastnosti databázového účtu Azure Cosmos DB. Mezi vlastnosti, které lze aktualizovat, patří následující:

* Přidání nebo odebrání oblastí
* Změna výchozí zásady konzistence
* Změna filtru rozsahu IP adres
* Změna konfigurace virtuální sítě
* Povolení vícehlavních

> [!NOTE]
> Nelze současně přidat nebo `locations` odebrat oblasti a změnit další vlastnosti pro účet Azure Cosmos. Změny oblastí musí být provedeny jako samostatná operace od jakékoli jiné změny účtu.
> [!NOTE]
> Tento příkaz umožňuje přidávat a odebírat oblasti, ale neumožňuje měnit priority převzetí služeb při selhání nebo aktivovat ruční převzetí služeb při selhání. Viz [Změna priority převzetí služeb při selhání](#modify-failover-priority) a [ručnípřevzetí služeb při selhání .](#trigger-manual-failover)

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Region operations
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$apiVersion = "2020-03-01"

# Add a region to the account
$locations2 = @("West US 2", "East US 2", "South Central US")
$locationObjects2 = @()
$i = 0
ForEach ($location in $locations2) { $locationObjects2 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects2;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove a region from the account
$locations3 = @("West US 2", "South Central US")
$locationObjects3 = @()
$i = 0
ForEach ($location in $locations3) { $locationObjects3 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects3;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```
### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-master"></a>Povolení více oblastí zápisu pro účet Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-master
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a>Odstranění účtu Azure Cosmos

Tento příkaz odstraní existující účet Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -PassThru
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a>Aktualizace značek účtu Azure Cosmos

Tento příkaz nastaví [značky prostředků Azure][azure-resource-tags] pro účet Azure Cosmos. Značky lze nastavit jak `New-AzCosmosDBAccount` při vytváření účtu pomocí, tak při aktualizaci účtu pomocí `Update-AzCosmosDBAccount`.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a>Seznam klíčů účtu

Když vytvoříte účet Azure Cosmos, služba generuje dva hlavní přístupové klíče, které se dá použít k ověřování při přístupu k účtu Azure Cosmos. Klíče jen pro čtení pro ověřování operací jen pro čtení jsou také generovány.
Poskytnutím dvou přístupových klíčů umožňuje Azure Cosmos DB regenerovat a otáčet jeden klíč najednou bez přerušení vašeho účtu Azure Cosmos.
Účty Cosmos DB mají dva klíče pro čtení a zápis (primární a sekundární) a dva klíče jen pro čtení (primární a sekundární).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a>Seznam připojovacích řetězců

Následující příkaz načte připojovací řetězce pro připojení aplikací k účtu Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a>Obnovit klíče účtu

Přístupové klíče k účtu Azure Cosmos by měly být pravidelně regenerovány, aby byla připojení zabezpečená. K účtu jsou přiřazeny primární a sekundární přístupové klíče. To umožňuje klientům udržovat přístup, zatímco jeden klíč najednou je obnovena.
Existují čtyři typy klíčů pro účet Azure Cosmos (primární, sekundární, primárníreadonly a sekundárníreadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a>Povolit automatické převzetí služeb při selhání

Následující příkaz nastaví účet Cosmos DB automaticky převzetí služeb při selhání do sekundární oblasti, pokud primární oblast přestane být k dispozici.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-master - cannot have both automatic
# failover and multi-master on an account
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

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a>Změnit prioritu převzetí služeb při selhání

U účtů nakonfigurovaných pomocí automatického převzetí služeb při selhání můžete změnit pořadí, ve kterém cosmos povýší sekundární repliky na primární, pokud primární přestane být k dispozici.

V níže uvedeném příkladu předpokládejme, `East US 2 = 1` `South Central US = 2`že aktuální priorita převzetí služeb při selhání je `West US 2 = 0`, . Příkaz to toto `West US 2 = 0` `South Central US = 1`změní `East US 2 = 2`na , .

> [!CAUTION]
> Změna umístění `failoverPriority=0` pro aktivuje ruční převzetí služeb při selhání pro účet Azure Cosmos. Žádné jiné změny priority nespustí převzetí služeb při selhání.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a>Ruční převzetí služeb při selhání

U účtů nakonfigurovaných pomocí ručního převzetí služeb při selhání můžete `failoverPriority=0`převést na službu převzetí služeb při selhání a povýšit sekundární repliku na primární úpravou aplikace . Tuto operaci lze použít k zahájení cvičení zotavení po havárii k testování plánování zotavení po havárii.

V níže uvedeném příkladu předpokládejme, že `West US 2 = 0` `East US 2 = 1` účet má aktuální prioritu převzetí služeb při selhání a překlopit oblasti.

> [!CAUTION]
> Změna `locationName` `failoverPriority=0` pro aktivuje ruční převzetí služeb při selhání pro účet Azure Cosmos. Žádná jiná změna priority nespustí převzetí služeb při selhání.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

## <a name="azure-cosmos-db-database"></a>Databáze Azure Cosmos DB

Následující části ukazují, jak spravovat databázi Azure Cosmos DB, včetně:

* [Vytvoření databáze Azure Cosmos DB](#create-db)
* [Vytvoření databáze Azure Cosmos DB se sdílenou propustností](#create-db-ru)
* [Získání propustnost databáze Azure Cosmos DB](#get-db-ru)
* [Seznam všech databází Azure Cosmos DB v účtu](#list-db)
* [Získejte jednu databázi Azure Cosmos DB](#get-db)
* [Odstranění databáze Azure Cosmos DB](#delete-db)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Vytvoření databáze Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Set-AzCosmosDBSqlDatabase `
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

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Získání propustnost databáze Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Získání všech databází Azure Cosmos DB v účtu

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Získejte jednu databázi Azure Cosmos DB

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

## <a name="azure-cosmos-db-container"></a>Kontejner Azure Cosmos DB

Následující části ukazují, jak spravovat kontejner Azure Cosmos DB, včetně:

* [Vytvoření kontejneru Azure Cosmos DB](#create-container)
* [Vytvoření kontejneru Azure Cosmos DB s velkým klíčem oddílu](#create-container-big-pk)
* [Získání propustnost kontejneru Azure Cosmos DB](#get-container-ru)
* [Vytvoření kontejneru Azure Cosmos DB s vlastním indexováním](#create-container-custom-index)
* [Vytvoření kontejneru Azure Cosmos DB s vypnutým indexováním](#create-container-no-index)
* [Vytvoření kontejneru Azure Cosmos DB s jedinečným klíčem a ttl](#create-container-unique-key-ttl)
* [Vytvoření kontejneru Azure Cosmos DB s řešením konfliktů](#create-container-lww)
* [Seznam všech kontejnerů Azure Cosmos DB v databázi](#list-containers)
* [Získání jednoho kontejneru Azure Cosmos DB v databázi](#get-container)
* [Odstranění kontejneru Azure Cosmos DB](#delete-container)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Vytvoření kontejneru Azure Cosmos DB

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Vytvoření kontejneru Azure Cosmos DB s velkou velikostí klíče oddílu

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Získání propustnost kontejneru Azure Cosmos DB

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

Set-AzCosmosDBSqlContainer `
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

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Vytvoření kontejneru Azure Cosmos DB s jedinečnými zásadami klíče a ttl

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

Set-AzCosmosDBSqlContainer `
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

Chcete-li vytvořit zásadu řešení konfliktů `"mode"="custom"` pro použití uložené procedury, nastavte a `"conflictResolutionPath"="myResolverStoredProcedure"`nastavte cestu řešení jako název uložené procedury . Chcete-li zapsat všechny konflikty do ConflictsFeed a pracovat samostatně, nastavte `"mode"="custom"` a`"conflictResolutionPath"=""`

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

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Seznam všech kontejnerů Azure Cosmos DB v databázi

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

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Získání jednoho kontejneru Azure Cosmos DB v databázi

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

## <a name="next-steps"></a>Další kroky

* [Všechny ukázky prostředí PowerShell](powershell-samples.md)
* [Jak spravovat účet Azure Cosmos](how-to-manage-database-account.md)
* [Vytvoření kontejneru Azure Cosmos DB](how-to-create-container.md)
* [Konfigurace času do provozu v Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
