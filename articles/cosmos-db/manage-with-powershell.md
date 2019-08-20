---
title: Vytvoření a Správa Azure Cosmos DB pomocí prostředí PowerShell
description: Použijte Azure PowerShell ke správě účtů Azure Cosmos DB, databází, kontejnerů a propustnosti.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/05/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: e8f943ebaa5dfc06e0bfb04dc1097d6794ec6d05
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616833"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Správa prostředků rozhraní SQL API Azure Cosmos DB pomocí PowerShellu

Následující příručka popisuje použití prostředí PowerShell ke skriptování a automatizaci správy prostředků Azure Cosmos DB, včetně účtu, databáze, kontejneru a propustnosti. Správa Azure Cosmos DB je zpracována prostřednictvím rutiny AzResource přímo k poskytovateli prostředků Azure Cosmos DB. Pokud chcete zobrazit všechny vlastnosti, které se dají spravovat pomocí prostředí PowerShell pro poskytovatele prostředků Azure Cosmos DB, přečtěte si téma [schéma poskytovatele prostředků Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) .

Pro správu Azure Cosmos DB pro různé platformy můžete použít rozhraní příkazového [řádku Azure CLI](manage-with-cli.md), [REST API][rp-rest-api]nebo [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Začínáme

Postupujte podle pokynů v tématu [instalace a konfigurace Azure PowerShell][powershell-install-configure] pro instalaci a přihlášení ke svému účtu Azure v prostředí PowerShell.

* Pokud chcete spustit následující příkazy bez nutnosti potvrzení uživatelem, připojte `-Force` příznak, který tento příkaz.
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

### <a id="create-account"></a>Vytvoření účtu Azure Cosmos

Tento příkaz vytvoří účet databáze Azure Cosmos s více oblastmi, [zásadami konzistence](consistency-levels.md)s vazbou na [více oblastí][distribute-data-globally].

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

* `$accountName`Název účtu Azure Cosmos. Musí obsahovat malá písmena, přijímat alfanumerické znaky a znak "-" a mezi 3 a 31 znaky.
* `$location`Umístění prostředku účtu Azure Cosmos
* `$locations`Oblasti repliky pro databázový účet. Pro každý databázový účet musí být jedna oblast zápisu s hodnotou priority převzetí služeb při selhání 0.
* `$consistencyPolicy`Výchozí úroveň konzistence účtu Azure Cosmos. Další informace najdete v tématu [úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties`Hodnoty vlastností předané poskytovateli Cosmos DB Azure Resource Manager ke zřízení účtu.

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
> Tento příkaz umožňuje přidat a odebrat oblasti, ale neumožňuje měnit priority převzetí služeb při selhání nebo měnit oblast pomocí `failoverPriority=0`. Postup úpravy priority převzetí služeb při selhání najdete v tématu [Úprava priority převzetí služeb při selhání pro účet Azure Cosmos](#modify-failover-priority).

```azurepowershell-interactive
# Get an Azure Cosmos Account (assume it has two regions currently West US 2 and East US 2) and add a third region

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="South Central US"; "failoverPriority"=2 }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
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
> Tento příkaz je možné kombinovat s příkazy vytvořit nebo aktualizovat přidáním `-Tags` příznak s odpovídajícím parametrem.

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

### <a id="list-keys"></a> Vypsat klíče účtu

Při vytváření účtu služby Azure Cosmos DB, generuje tato služba dva hlavní přístupové klíče, které se dá použít pro ověření při přístupu k účtu Azure Cosmos DB. Poskytnutím dvou přístupových klíčů služby Azure Cosmos DB umožňuje znovu vygenerovat klíče bez přerušení ke svému účtu Azure Cosmos DB. Klíče jen pro čtení pro ověřování jen pro čtení operace jsou také k dispozici. (Primární i sekundární) existují dva klíče pro čtení i zápis (primární i sekundární) a dva klíče jen pro čtení.

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> Seznam připojovacích řetězců

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

### <a id="modify-failover-priority"></a>Úprava priority převzetí služeb při selhání

Pro účty databáze ve více oblastech můžete změnit pořadí, ve kterém bude účet Cosmos propagovat sekundární repliky pro čtení, pokud dojde k místnímu převzetí služeb při selhání v primární replice pro zápis. Změny `failoverPriority=0` lze také použít k zahájení plánování zotavení po havárii.

V následujícím příkladu Předpokládejme, že účet má aktuální prioritu `West US 2 = 0` převzetí služeb při selhání pro a `East US 2 = 1` a překlopit oblasti.

> [!CAUTION]
> Změna `locationName` pro`failoverPriority=0` spustí ruční převzetí služeb při selhání pro účet Azure Cosmos. Jakékoli další změny priority nebudou aktivovat převzetí služeb při selhání.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0 and "East US 2" = 1

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="East US 2"; "failoverPriority"=0 },
    @{ "locationName"="West US 2"; "failoverPriority"=1 }
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
# Create a container with a unique key policy and TTL
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
        "defaultTtl"= 100;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Vytvoření kontejneru Azure Cosmos s řešením konfliktů

Chcete-li vytvořit zásadu řešení konfliktů pro použití uložené procedury, nastavte `"mode"="custom"` a nastavte cestu řešení jako název uložené procedury,. `"conflictResolutionPath"="myResolverStoredProcedure"` Chcete-li zapsat všechny konflikty do ConflictsFeed a zpracovat samostatně, `"mode"="custom"` nastavte a`"conflictResolutionPath"=""`

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
