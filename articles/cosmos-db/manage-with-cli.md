---
title: Správa prostředků Azure Cosmos DB pomocí Azure CLI
description: Pomocí Azure CLI můžete spravovat Azure Cosmos DB účet, databázi a kontejnery.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2020
ms.author: mjbrown
ms.openlocfilehash: c248755c1f32d41b6926d4492dcc3d0eea2869b8
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91566869"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Správa prostředků Azure Cosmos pomocí Azure CLI

Následující příručka popisuje běžné příkazy pro automatizaci správy účtů, databází a kontejnerů Azure Cosmos DB pomocí Azure CLI. Referenční stránky pro všechny příkazy rozhraní příkazového řádku Azure Cosmos DB jsou k dispozici v [referenčních informacích k Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Další příklady najdete v [ukázkách Azure CLI pro Azure Cosmos DB](cli-samples.md), včetně toho, jak vytvářet a spravovat Cosmos DB účty, databáze a kontejnery pro MongoDB, Gremlin, Cassandra a rozhraní API pro tabulky.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.9.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="azure-cosmos-accounts"></a>Účty Azure Cosmos

Následující části demonstrují, jak spravovat účet Azure Cosmos, včetně:

* [Vytvoření účtu Azure Cosmos](#create-an-azure-cosmos-db-account)
* [Přidání nebo odebrání oblastí](#add-or-remove-regions)
* [Povolit zápisy ve více oblastech](#enable-multiple-write-regions)
* [Nastavit prioritu regionálního převzetí služeb při selhání](#set-failover-priority)
* [Povolit automatické převzetí služeb při selhání](#enable-automatic-failover)
* [Aktivace ručního převzetí služeb při selhání](#trigger-manual-failover)
* [Výpis klíčů účtu](#list-account-keys)
* [Vypsat klíče účtu jen pro čtení](#list-read-only-account-keys)
* [Vypsat připojovací řetězce](#list-connection-strings)
* [Znovu vygenerovat klíč účtu](#regenerate-account-key)

### <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Vytvoření účtu Azure Cosmos DB pomocí rozhraní SQL API, konzistence relace v Západní USA 2 a Východní USA 2 oblastech:

> [!IMPORTANT]
> Název účtu Azure Cosmos musí být malými písmeny a menší než 44 znaků.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 44 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

### <a name="add-or-remove-regions"></a>Přidání nebo odebrání oblastí

Vytvořte účet Azure Cosmos se dvěma oblastmi, přidejte oblast a odstraňte oblast.

> [!NOTE]
> Nemůžete současně přidat ani odebrat oblasti `locations` a změnit další vlastnosti pro účet Azure Cosmos. Úprava oblastí se musí provádět jako samostatná operace, než jakákoli jiná změna prostředku účtu.
> [!NOTE]
> Tento příkaz umožňuje přidat a odebrat oblasti, ale neumožňuje měnit priority převzetí služeb při selhání ani aktivovat ruční převzetí služeb při selhání. Viz [Nastavení priority převzetí služeb při selhání](#set-failover-priority) a [Aktivace ručního převzetí služeb](#trigger-manual-failover)

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

### <a name="enable-multiple-write-regions"></a>Povolit více oblastí zápisu

Povolení zápisů ve více oblastech pro účet Cosmos

```azurecli-interactive
# Update an Azure Cosmos account from single write region to multiple write regions
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

### <a name="set-failover-priority"></a>Nastavení priority převzetí služeb při selhání

Nastavení priority převzetí služeb při selhání pro účet Azure Cosmos nakonfigurovaný pro automatické převzetí služeb při selhání

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2=0' 'South Central US=1' 'East US 2=2'
```

### <a name="enable-automatic-failover"></a>Povolit automatické převzetí služeb při selhání

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

### <a name="trigger-manual-failover"></a>Aktivace ručního převzetí služeb při selhání

> [!CAUTION]
> Změna oblasti s prioritou = 0 spustí ruční převzetí služeb při selhání pro účet Azure Cosmos. Žádná jiná změna priority nebude aktivovat převzetí služeb při selhání.

```azurecli-interactive
# Assume region order is initially 'West US 2=0' 'East US 2=1' 'South Central US=2' for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2=0' 'South Central US=1' 'West US 2=2'
```

### <a name="list-all-account-keys"></a><a id="list-account-keys"></a> Vypsat všechny klíče účtu

Získejte všechny klíče pro účet Cosmos.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

### <a name="list-read-only-account-keys"></a>Vypsat klíče účtu jen pro čtení

Získejte klíče jen pro čtení pro účet Cosmos.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

### <a name="list-connection-strings"></a>Vypsat připojovací řetězce

Získejte připojovací řetězce pro účet Cosmos.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

### <a name="regenerate-account-key"></a>Znovu vygenerovat klíč účtu

Znovu Vygenerujte nový klíč pro účet Cosmos.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB databáze

Následující části demonstrují, jak spravovat databázi Azure Cosmos DB, včetně:

* [Vytvoření databáze](#create-a-database)
* [Vytvoření databáze se sdílenou propustností](#create-a-database-with-shared-throughput)
* [Změna propustnosti databáze](#change-database-throughput)
* [Správa zámků v databázi](#manage-lock-on-a-database)

### <a name="create-a-database"></a>Vytvoření databáze

Vytvořte databázi Cosmos.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

### <a name="create-a-database-with-shared-throughput"></a>Vytvoření databáze se sdílenou propustností

Vytvořte databázi Cosmos se sdílenou propustností.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

### <a name="change-database-throughput"></a>Změna propustnosti databáze

Zvyšte propustnost databáze Cosmos o 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

### <a name="manage-lock-on-a-database"></a>Správa zámku pro databázi

Vložte zámek proti odstranění databáze. Další informace o tom, jak tento postup povolit, najdete v tématu [prevence změn ze sad SDK](role-based-access-control.md#prevent-sdk-changes).

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
databaseName='myDatabase'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
databaseLockName="$databaseName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/sqlDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/sqlDatabases \
        --resource $databaseName \
        --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB kontejner

Následující části ukazují, jak spravovat Azure Cosmos DB kontejner, včetně:

* [Vytvoření kontejneru](#create-a-container)
* [Vytvoření kontejneru pomocí automatického škálování](#create-a-container-with-autoscale)
* [Vytvoření kontejneru s povoleným TTL](#create-a-container-with-ttl)
* [Vytvoření kontejneru s vlastními zásadami indexů](#create-a-container-with-a-custom-index-policy)
* [Změna propustnosti kontejneru](#change-container-throughput)
* [Správa zámků na kontejneru](#manage-lock-on-a-container)

### <a name="create-a-container"></a>Vytvoření kontejneru

Vytvořte kontejner Cosmos s výchozími zásadami indexování, klíč oddílu a RU/s 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

### <a name="create-a-container-with-autoscale"></a>Vytvoření kontejneru pomocí automatického škálování

Vytvořte kontejner Cosmos s výchozími zásadami indexování, klíč oddílu a 4000 automatického škálování RU/s z.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
maxThroughput=4000

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --max-throughput $maxThroughput
```

### <a name="create-a-container-with-ttl"></a>Vytvoření kontejneru s hodnotou TTL

Vytvoří kontejner Cosmos s povoleným TTL.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

### <a name="create-a-container-with-a-custom-index-policy"></a>Vytvoření kontejneru s vlastními zásadami indexů

Vytvořte kontejner Cosmos s vlastními zásadami indexů, prostorový index, složený index, klíč oddílu a RU/s 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

### <a name="change-container-throughput"></a>Změna propustnosti kontejneru

Zvyšte propustnost Cosmos kontejneru o 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

### <a name="manage-lock-on-a-container"></a>Správa zámku na kontejneru

Vložte zámek proti odstranění do kontejneru. Další informace o tom, jak tento postup povolit, najdete v tématu [prevence změn ze sad SDK](role-based-access-control.md#prevent-sdk-changes).

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
databaseName='myDatabase'
containerName='myContainer'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
containerParent="databaseAccounts/$accountName/sqlDatabases/$databaseName"
containerLockName="$containerName-Lock"

# Create a delete lock on container
az lock create --name $containerLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/containers \
    --lock-type $lockType \
    --parent $containerParent \
    --resource $containerName

# Delete lock on container
lockid=$(az lock show --name $containerLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/containers \
        --resource-name $containerName \
        --parent $containerParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="next-steps"></a>Další kroky

Další informace o rozhraní příkazového řádku Azure najdete v těchto tématech:

- [Instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)
- [Reference k rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Další ukázky v Azure CLI pro Azure Cosmos DB](cli-samples.md)
