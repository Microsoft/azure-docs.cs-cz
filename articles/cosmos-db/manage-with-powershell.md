---
title: Vytvoření a Správa Azure Cosmos DB pomocí prostředí PowerShell
description: Použijte Azure PowerShell ke správě účtů Azure Cosmos DB, databází, kontejnerů a propustnosti.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/23/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 978f37d08275de704dd01c0251dde42665fca552
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238486"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Správa prostředků rozhraní SQL API Azure Cosmos DB pomocí PowerShellu

Následující příručka popisuje, jak pomocí PowerShellu skriptovat a automatizovat správu prostředků Azure Cosmos DB, včetně účtů, databází, kontejnerů a propustnosti. Správa služby Azure Cosmos DB se provádí odesíláním rutin AzResource přímo poskytovateli prostředků služby Azure Cosmos DB. Pokud chcete zobrazit všechny vlastnosti, které se dají spravovat pomocí prostředí PowerShell pro poskytovatele prostředků Azure Cosmos DB, přečtěte si téma [schéma poskytovatele prostředků Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) .

Pro správu Azure Cosmos DB pro různé platformy můžete použít rozhraní příkazového [řádku Azure CLI](manage-with-cli.md), [REST API][rp-rest-api]nebo [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Začínáme

Postupujte podle pokynů v tématu [instalace a konfigurace Azure PowerShell][powershell-install-configure] pro instalaci a přihlášení ke svému účtu Azure v prostředí PowerShell.

* Pokud chcete spustit následující příkazy, aniž byste museli potvrzovat uživatele, přidejte do příkazu příznak `-Force`.
* Následující příkazy jsou synchronní.

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

### <a id="create-account"></a>Vytvoření účtu Azure Cosmos

Tento příkaz vytvoří účet databáze Azure Cosmos s [více oblastmi][distribute-data-globally], [zásadami konzistence](consistency-levels.md)s vazbou na více oblastí.

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` název účtu Azure Cosmos. Musí obsahovat malá písmena, přijímat alfanumerické znaky a znak "-" a mezi 3 a 31 znaky.
* `$location` umístění prostředku účtu Azure Cosmos.
* `$locations` oblasti repliky pro databázový účet. Pro každý databázový účet musí být jedna oblast zápisu s hodnotou priority převzetí služeb při selhání 0.
* `$consistencyPolicy` výchozí úroveň konzistence účtu Azure Cosmos. Další informace najdete v tématu [úrovně konzistence v Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` hodnoty vlastností předané poskytovateli Cosmos DB Azure Resource Manager k zřízení účtu.

Účty Azure Cosmos se dají nakonfigurovat pomocí brány firewall protokolu IP a Virtual Network koncových bodů služby. Informace o tom, jak nakonfigurovat bránu firewall protokolu IP pro Azure Cosmos DB, najdete v tématu [Konfigurace brány firewall protokolu IP](how-to-configure-firewall.md).  Další informace o povolení koncových bodů služby pro Azure Cosmos DB najdete v tématu [Konfigurace přístupu z virtuálních sítí](how-to-configure-vnet-service-endpoint.md).

### <a id="list-accounts"></a>Výpis všech účtů Azure Cosmos v předplatném

Tento příkaz umožňuje zobrazit seznam všech účtů Azure Cosmos v rámci předplatného.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a>Získání vlastností účtu Azure Cosmos

Tento příkaz umožňuje získat vlastnosti stávajícího účtu Azure Cosmos.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a>Aktualizace účtu Azure Cosmos

Tento příkaz umožňuje aktualizovat vlastnosti účtu databáze Azure Cosmos. Mezi vlastnosti, které lze aktualizovat, patří následující:

* Přidávání nebo odebírání oblastí
* Mění se výchozí zásada konzistence.
* Změna filtru rozsahu IP adres
* Změna konfigurace Virtual Network
* Povolení více hlavních serverů

> [!NOTE]
> Nemůžete současně přidat ani odebrat oblasti `locations` a změnit další vlastnosti pro účet Azure Cosmos. Úprava oblastí se musí provádět jako samostatná operace, než jakákoli jiná změna prostředku účtu.
> [!NOTE]
> Tento příkaz umožňuje přidat a odebrat oblasti, ale neumožňuje měnit priority převzetí služeb při selhání ani aktivovat ruční převzetí služeb při selhání. Viz [Upravit prioritu převzetí služeb při selhání](#modify-failover-priority) a [aktivovat ruční převzetí služeb při selhání](#trigger-manual-failover)

```azurepowershell-interactive
# Create an account with 2 regions
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$accountName = "mycosmosaccount" # must be lower case and < 31 characters

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false },
    @{ "locationName"="South Central US"; "failoverPriority"=2, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```
### <a id="multi-master"></a>Povolení více oblastí zápisu pro účet Azure Cosmos

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a>Odstranění účtu Azure Cosmos

Tento příkaz umožňuje odstranit existující účet Azure Cosmos.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a>Aktualizovat značky účtu Azure Cosmos

Následující příklad popisuje, jak nastavit [značky prostředků Azure][azure-resource-tags] pro účet Azure Cosmos.

> [!NOTE]
> Tento příkaz lze kombinovat s příkazy vytvořit nebo aktualizovat připojením příznaku `-Tags` k odpovídajícímu parametru.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a>Výpis klíčů účtu

Při vytváření účtu služby Azure Cosmos DB, generuje tato služba dva hlavní přístupové klíče, které se dá použít pro ověření při přístupu k účtu Azure Cosmos DB. Poskytnutím dvou přístupových klíčů služby Azure Cosmos DB umožňuje znovu vygenerovat klíče bez přerušení ke svému účtu Azure Cosmos DB. Klíče jen pro čtení pro ověřování jen pro čtení operace jsou také k dispozici. (Primární i sekundární) existují dva klíče pro čtení i zápis (primární i sekundární) a dva klíče jen pro čtení.

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Write-Host "PrimaryKey =" $keys.primaryMasterKey
Write-Host "SecondaryKey =" $keys.secondaryMasterKey
```

### <a id="list-connection-strings"></a>Vypsat připojovací řetězce

Pro účty MongoDB můžete načíst připojovací řetězec pro připojení aplikace MongoDB k účtu databáze pomocí následujícího příkazu.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a>Znovu vygenerovat klíče účtu

Přístup ke klíčům k účtu Azure Cosmos by se měl pravidelně znovu vygenerovat, aby se připojení lépe zabezpečilo. K účtu se přiřadí primární a sekundární přístupové klíče. To umožňuje klientům zachovat přístup v době, kdy se druhá znovu vygeneruje. Existují čtyři typy klíčů pro účet Azure Cosmos (primární, sekundární, PrimaryReadonly a SecondaryReadonly).

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="enable-automatic-failover"></a>Povolit automatické převzetí služeb při selhání

Umožňuje účtu Cosmos převzetí služeb při selhání do sekundární oblasti v případě, že primární oblast nebude k dispozici.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="modify-failover-priority"></a>Úprava priority převzetí služeb při selhání

U účtů konfigurovaných s automatickým převzetím služeb při selhání můžete změnit pořadí, ve kterém bude Cosmos povýšit sekundární repliky na primární, pokud primární databáze není k dispozici.

V následujícím příkladu Předpokládejme, že aktuální priorita převzetí služeb při selhání `West US 2 = 0``East US 2 = 1``South Central US = 2`.

> [!CAUTION]
> Změna `locationName` `failoverPriority=0` spustí ruční převzetí služeb při selhání pro účet Azure Cosmos. Jakékoli další změny priority nebudou aktivovat převzetí služeb při selhání.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="South Central US"; "failoverPriority"=1 },
    @{ "locationName"="East US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

### <a id="trigger-manual-failover"></a>Aktivace ručního převzetí služeb při selhání

Pro účty nakonfigurované s ručním převzetím služeb při selhání můžete převzetí služeb při selhání a zvýšení úrovně sekundární repliky na primární úpravou `failoverPriority=0`. Tato operace se dá použít k inicializaci plánování zotavení po havárii při zotavení po havárii.

V následujícím příkladu Předpokládejme, že účet má aktuální prioritu převzetí služeb při selhání `West US 2 = 0` a `East US 2 = 1` a Překlopí oblasti.

> [!CAUTION]
> Změna `locationName` `failoverPriority=0` spustí ruční převzetí služeb při selhání pro účet Azure Cosmos. Žádná jiná změna priority nebude aktivovat převzetí služeb při selhání.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="South Central US"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="West US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Databáze Azure Cosmos

Následující části demonstrují, jak spravovat databázi Azure Cosmos, včetně:

* [Vytvoření databáze Azure Cosmos](#create-db)
* [Vytvoření databáze Azure Cosmos se sdílenou propustností](#create-db-ru)
* [Získání propustnosti databáze Azure Cosmos](#get-db-ru)
* [Vypsat všechny databáze Azure Cosmos v účtu](#list-db)
* [Získání jedné databáze Azure Cosmos](#get-db)
* [Odstranění databáze Azure Cosmos](#delete-db)

### <a id="create-db"></a>Vytvoření databáze Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Vytvoření databáze Azure Cosmos se sdílenou propustností

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>Získání propustnosti databáze Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>Získat všechny databáze Azure Cosmos v účtu

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Získání jedné databáze Azure Cosmos

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Odstranění databáze Azure Cosmos

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos Container

Následující části demonstrují, jak spravovat kontejner Azure Cosmos, včetně:

* [Vytvoření kontejneru Azure Cosmos](#create-container)
* [Vytvoření kontejneru Azure Cosmos s velkým klíčem oddílu](#create-container-big-pk)
* [Získání propustnosti Cosmos kontejneru Azure](#get-container-ru)
* [Vytvoření kontejneru Azure Cosmos se sdílenou propustností](#create-container-ru)
* [Vytvoření kontejneru Azure Cosmos s vlastním indexováním](#create-container-custom-index)
* [Vytvoření kontejneru Azure Cosmos s vypnutým indexováním](#create-container-no-index)
* [Vytvoření kontejneru Azure Cosmos s jedinečným klíčem a hodnotou TTL](#create-container-unique-key-ttl)
* [Vytvoření kontejneru Azure Cosmos s řešením konfliktů](#create-container-lww)
* [Vypsat všechny kontejnery Azure Cosmos v databázi](#list-containers)
* [Získání jednoho kontejneru Azure Cosmos v databázi](#get-container)
* [Odstranění kontejneru Azure Cosmos](#delete-container)

### <a id="create-container"></a>Vytvoření kontejneru Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-big-pk"></a>Vytvoření kontejneru Azure Cosmos s velkou velikostí klíče oddílu

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>Získání propustnosti Cosmos kontejneru Azure

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>Vytvoření kontejneru Azure Cosmos se sdílenou propustností

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Vytvoření kontejneru Azure Cosmos s vlastními zásadami indexů

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Vytvoření kontejneru Azure Cosmos s vypnutým indexováním

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Vytvoření kontejneru Azure Cosmos s jedinečnou klíčovou zásadou a hodnotou TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 86400;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Vytvoření kontejneru Azure Cosmos s řešením konfliktů

Chcete-li vytvořit zásadu řešení konfliktů pro použití uložené procedury, nastavte `"mode"="custom"` a nastavte cestu řešení jako název uložené procedury, `"conflictResolutionPath"="myResolverStoredProcedure"`. Chcete-li zapisovat všechny konflikty do ConflictsFeed a zpracovávat samostatně, nastavte `"mode"="custom"` a `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-containers"></a>Vypsat všechny kontejnery Azure Cosmos v databázi

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Získání jednoho kontejneru Azure Cosmos v databázi

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Odstranění kontejneru Azure Cosmos

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Další kroky

* [Všechny ukázky PowerShellu](powershell-samples.md)
* [Jak spravovat účet Azure Cosmos](how-to-manage-database-account.md)
* [Vytvoření kontejneru Azure Cosmos](how-to-create-container.md)
* [Konfigurace času na živé v Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
