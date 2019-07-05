---
title: Vytvoření a Správa služby Azure Cosmos DB pomocí Powershellu
description: Pomocí prostředí Azure Powershell spravovat účty služby Azure Cosmos DB, databází, kontejnerů a propustnost.
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 07/03/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 40f041f1b41077824aa3141f6196901b51415c35
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565924"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Spravovat prostředky SQL API služby Azure Cosmos DB pomocí Powershellu

Následující průvodce popisuje, jak pomocí prostředí PowerShell do skriptu a automatizovat správu prostředků služby Azure Cosmos DB, včetně účtu, databáze, kontejner a propustnost. Správa služby Azure Cosmos DB se zpracovává prostřednictvím rutiny AzResource přímo u poskytovatele prostředků služby Azure Cosmos DB. Chcete-li zobrazit všechny vlastnosti, které je možné spravovat pomocí prostředí PowerShell pro poskytovatele prostředků služby Azure Cosmos DB, najdete v článku [schématu poskytovatele prostředků služby Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Pro různé platformy správy služby Azure Cosmos DB, můžete použít [rozhraní příkazového řádku Azure](manage-with-cli.md), [rozhraní REST API][rp-rest-api], nebo [webu Azure portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Začínáme

Postupujte podle pokynů v [instalace a konfigurace Azure Powershellu][powershell-install-configure] k instalaci a přihlaste se ke svému účtu Azure v prostředí Powershell.

* Pokud chcete spustit následující příkazy bez nutnosti potvrzení uživatelem, připojte `-Force` příznak, který tento příkaz.
* Následující příkazy jsou synchronní.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos Accounts

Následující části ukazují, jak spravovat účet Azure Cosmos, včetně:

* [Vytvoření účtu služby Azure Cosmos](#create-account)
* [Aktualizovat účet Azure Cosmos](#update-account)
* [Vytvoření účtu Azure Cosmos](#get-account)
* [Odstranit účet Azure Cosmos](#delete-account)
* [Aktualizace značky pro účet Azure Cosmos](#update-tags)
* [Vypsat klíče účtu Azure Cosmos](#list-keys)
* [Znovu vygenerovat klíče účtu Azure Cosmos](#regenerate-keys)
* [Seznam připojovacích řetězců pro účet Azure Cosmos](#list-connection-strings)
* [Změna priority převzetí služeb při selhání pro účet Azure Cosmos](#modify-failover-priority)

### <a id="create-account"></a> Vytvoření účtu služby Azure Cosmos

Tento příkaz vytvoří k účtu databáze Azure Cosmos DB s [více oblastí][distribute-data-globally], omezená neaktuálnost [zásady konzistence](consistency-levels.md).

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lower case.

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

* `$accountName` Název účtu Azure Cosmos. Musí obsahovat malá písmena, přijímá alfanumerické znaky a "-" znaků a mezi 3 a nejvýše 31 znaků.
* `$location` Umístění pro prostředek účtu Azure Cosmos.
* `$locations` Oblasti repliky pro databázový účet. Musí existovat jedné oblasti zápisu na jeden účet databáze s hodnotou priority převzetí služeb při selhání z 0.
* `$consistencyPolicy` Výchozí úroveň konzistence účtu Azure Cosmos. Další informace najdete v tématu [úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` Hodnoty vlastností předána zprostředkovateli Cosmos DB Azure Resource Manageru pro zřízení účtu.

Azure Cosmos, které účty se dá nakonfigurovat s brány Firewall protokolu IP, jakož i virtuální síť služby koncové body. Informace o konfiguraci brány Firewall protokolu IP pro službu Azure Cosmos DB najdete v tématu [konfigurace brány Firewall protokolu IP](how-to-configure-firewall.md).  Další informace o tom, jak povolit koncové body služby pro službu Azure Cosmos DB najdete v tématu [konfigurace přístupu z virtuální sítě](how-to-configure-vnet-service-endpoint.md) .

### <a id="get-account"></a> Získání vlastností účtu Azure Cosmos

Tento příkaz umožňuje získat vlastnosti existujícího účtu Azure Cosmos.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Aktualizovat účet Azure Cosmos

Tento příkaz umožňuje aktualizovat vlastnosti svého účtu databáze Azure Cosmos DB. Vlastnosti, které je možné aktualizovat, patří:

* Přidání nebo odebrání oblastí
* Změna zásad výchozí konzistence
* Změna zásad převzetí služeb při selhání
* Změna filtr rozsahu IP adres
* Změna konfigurace virtuální sítě
* Povolení více hlavních databází

> [!NOTE]
> Tento příkaz umožňuje přidávat a odebírat oblastech, ale neumožňuje úpravy priorit převzetí služeb při selhání. Chcete-li změnit prioritu převzetí služeb při selhání, najdete v článku [změnit prioritu převzetí služeb při selhání pro účet Azure Cosmos](#modify-failover-priority).

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Odstranit účet Azure Cosmos

Tento příkaz umožňuje odstranit účet Azure Cosmos.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Aktualizace značky účtu Azure Cosmos

Následující příklad popisuje, jak nastavit [značek prostředků Azure][azure-resource-tags] pro účet Azure Cosmos.

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

### <a id="regenerate-keys"></a> Znovu vygenerovat klíče účtu

Přístupové klíče k účtu Azure Cosmos, by měl obnovovaly pravidelně zabezpečení připojení. Primární a sekundární přístupové klíče se přiřazují k účtu. To umožňuje klientům spravovat přístup, zatímco druhá se znova vygeneroval. Existují čtyři typy klíčů pro účet Azure Cosmos (primární, sekundární, PrimaryReadonly a SecondaryReadonly)

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

### <a id="modify-failover-priority"></a> Změna Priority převzetí služeb při selhání

Pro účty databáze ve více oblastech můžete změnit pořadí, ve kterém účtu Cosmos podporuje sekundární repliky pro čtení regionální převzetí služeb při selhání se budou objevovat v zápisu primární replice. Když oblasti s `failoverPriority=0` je změněn, tento příkaz lze také zahájit zotavení po havárii pro testování, plánování zotavení po havárii.

Pro následujícím příkladu se předpokládá aktuální prioritu převzetí služeb při selhání westus má účet = 0 a eastus = 1 a překlopit oblastí.

> [!CAUTION]
> Tato operace se aktivuje ruční převzetí služeb při selhání pro účet Azure Cosmos.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Databáze Azure Cosmos

Následující části ukazují, jak spravovat databázi Azure Cosmos, včetně:

* [Vytvořit databázi Azure Cosmos](#create-db)
* [Vytvořit databázi Azure Cosmos pomocí sdílených propustnost](#create-db-ru)
* [Zjištění propustnosti databáze Azure Cosmos](#get-db-ru)
* [Vypsat všechny databáze Azure Cosmos v účtu služby](#get-all-db)
* [Získejte izolované databáze Azure Cosmos](#get-db)
* [Odstranit databázi Azure Cosmos](#delete-db)

### <a id="create-db"></a>Vytvořit databázi Azure Cosmos

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

### <a id="create-db-ru"></a>Vytvořit databázi Azure Cosmos pomocí sdílených propustnost

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

### <a id="get-db-ru"></a>Zjištění propustnosti databáze Azure Cosmos

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

### <a id="get-all-db"></a>Získá všechny databáze Azure Cosmos v účtu služby

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Získejte izolované databáze Azure Cosmos

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

### <a id="delete-db"></a>Odstranit databázi Azure Cosmos

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

Následující části ukazují, jak spravovat kontejneru Azure Cosmos, včetně:

* [Vytvoření kontejneru Azure Cosmos](#create-container)
* [Zjištění propustnosti kontejneru Azure Cosmos](#get-container-ru)
* [Vytvoření kontejneru Azure Cosmos pomocí sdílených propustnost](#create-container-ru)
* [Vytvoření kontejneru Azure Cosmos pomocí vlastní indexování](#create-container-custom-index)
* [Vytvoření kontejneru Azure Cosmos pomocí indexování vypnuté](#create-container-no-index)
* [Vytvoření kontejneru Azure Cosmos pomocí jedinečný klíč a hodnota TTL](#create-container-unique-key-ttl)
* [Vytvoření kontejneru Azure Cosmos pomocí řešení konfliktů](#create-container-lww)
* [Vypsat všechny kontejnery Azure Cosmos v databázi](#list-all-container)
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

### <a id="get-container-ru"></a>Zjištění propustnosti kontejneru Azure Cosmos

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

### <a id="create-container-ru"></a>Vytvoření kontejneru Azure Cosmos pomocí sdílených propustnost

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

### <a id="create-container-custom-index"></a>Vytvoření kontejneru Azure Cosmos pomocí zásad vlastní indexu

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

### <a id="create-container-no-index"></a>Vytvoření kontejneru Azure Cosmos pomocí indexování vypnuté

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

### <a id="create-container-unique-key-ttl"></a>Vytvoření kontejneru Azure Cosmos pomocí zásady jedinečného klíče a hodnoty TTL

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

### <a id="create-container-lww"></a>Vytvoření kontejneru Azure Cosmos pomocí řešení konfliktů

Chcete-li vytvořit zásada řešení konfliktů pro použití uložené procedury, nastavte `"mode"="custom"` a nastavení cesty k řešení jako název uložené procedury `"conflictResolutionPath"="myResolverStoredProcedure"`. Chcete-li zapsat všechny konflikty ConflictsFeed a zpracovávat samostatně, nastavte `"mode"="custom"` a `"conflictResolutionPath"=""`

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

### <a id="list-all-container"></a>Vypsat všechny kontejnery Azure Cosmos v databázi

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

## <a name="next-steps"></a>Další postup

* [Všechny ukázky prostředí PowerShell](powershell-samples.md)
* [Jak spravovat účet služby Azure Cosmos](how-to-manage-database-account.md)
* [Vytvoření kontejneru Azure Cosmos](how-to-create-container.md)
* [Konfigurace time-to-live ve službě Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
