---
title: Zřizování propustnosti automatického škálování v Azure Cosmos DB SQL API
description: Naučte se, jak zřídit propustnost automatického škálování na úrovni kontejneru a databáze v Azure Cosmos DB SQL API pomocí Azure Portal, CLI, PowerShellu a různých dalších sad SDK.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 52904296df77d9097a6180345388e8e702e2bca0
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357615"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db---sql-api"></a>Zřizování propustnosti automatického škálování v databázi nebo kontejneru v Azure Cosmos DB-SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek vysvětluje, jak zřídit propustnost automatického škálování pro databázi nebo kontejner (kolekci, graf nebo tabulku) v Azure Cosmos DB SQL API. Můžete povolit automatické škálování v jednom kontejneru nebo zřídit propustnost automatického škálování databáze a sdílet ji mezi všemi kontejnery v databázi.

Pokud používáte jiné rozhraní API, přečtěte si téma [rozhraní API pro MongoDB](how-to-provision-throughput-mongodb.md), [rozhraní API Cassandra](how-to-provision-throughput-cassandra.md), články [Gremlin API](how-to-provision-throughput-gremlin.md) pro zajištění propustnosti.

## <a name="azure-portal"></a>portál Azure

### <a name="create-new-database-or-container-with-autoscale"></a>Vytvoření nové databáze nebo kontejneru pomocí automatického škálování

1. Přihlaste se k [Azure Portal](https://portal.azure.com) nebo [Azure Cosmos DB Exploreru.](https://cosmos.azure.com/)

1. Přejděte na účet Azure Cosmos DB a otevřete kartu **Průzkumník dat** .

1. Vyberte **Nový kontejner.** Zadejte název databáze, kontejneru a klíč oddílu. V části **propustnost** vyberte možnost **automatického škálování** a nastavte [maximální propustnost (ru/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) , na kterou se má databáze nebo kontejner škálovat.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="Vytvoření kontejneru a konfigurace zřízené propustnosti automatického škálování":::

1. Vyberte **OK**.

Pokud chcete zřídit automatické škálování sdílené databáze propustnosti, vyberte při vytváření nové databáze možnost **zřídit propustnost databáze** . 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Povolit automatické škálování u existující databáze nebo kontejneru

> [!IMPORTANT]
> V aktuální verzi je Azure Portal jediným způsobem, jak migrovat mezi zřízenou propustností automatického škálování a standardní (ruční). 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) nebo [Azure Cosmos DB Exploreru.](https://cosmos.azure.com/)

1. Přejděte na účet Azure Cosmos DB a otevřete kartu **Průzkumník dat** .

1. Vyberte možnost **škálování a nastavení** pro váš kontejner nebo **Škálujte** pro vaši databázi.

1. V části **škálování** vyberte možnost **automatického škálování** a **uložte**.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="Povolení automatického škálování u existujícího kontejneru":::

> [!NOTE]
> Pokud povolíte automatické škálování u existující databáze nebo kontejneru, počáteční hodnota pro max. RU/s se určí systémem na základě vašeho aktuálního ručního zřízeného nastavení propustnosti a úložiště. Po dokončení operace můžete v případě potřeby změnit maximální RU/s. [Další informace](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk"></a>Sada Azure Cosmos DB .NET V3 SDK

Ke správě prostředků automatického škálování použijte [verzi 3,9 nebo vyšší](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) z Azure Cosmos DB .NET SDK pro SQL API. 

> [!IMPORTANT]
> K vytvoření nových prostředků automatického škálování můžete použít sadu .NET SDK. Sada SDK nepodporuje migraci mezi automatické škálování a standardní (ruční) propustností. Scénář migrace je aktuálně podporován pouze v Azure Portal. 

### <a name="create-database-with-shared-throughput"></a>Vytvoření databáze se sdílenou propustností

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Vytvořit kontejner s vyhrazenou propustností

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>Přečtěte si aktuální propustnost (RU/s).

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>Změna maximální propustnosti automatického škálování (RU/s)

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk"></a>Azure Cosmos DB SDK pro Java v4

Ke správě prostředků automatického škálování můžete použít [verzi 4,0 nebo vyšší](https://mvnrepository.com/artifact/com.azure/azure-cosmos) z Azure Cosmos DB Java SDK pro SQL API.

> [!IMPORTANT]
> K vytvoření nových prostředků automatického škálování můžete použít sadu Java SDK. Sada SDK nepodporuje migraci mezi automatické škálování a standardní (ruční) propustností. Scénář migrace je aktuálně podporován pouze v Azure Portal.

### <a name="create-database-with-shared-throughput"></a>Vytvoření databáze se sdílenou propustností

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[Synchronizovat](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Vytvořit kontejner s vyhrazenou propustností

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[Synchronizovat](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>Přečtěte si aktuální propustnost (RU/s).

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[Synchronizovat](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>Změna maximální propustnosti automatického škálování (RU/s)

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[Synchronizovat](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Šablony Azure Resource Manager lze použít ke zřízení propustnosti automatického škálování pro databáze nebo prostředky na úrovni kontejneru pro všechna rozhraní API Azure Cosmos DB. Ukázky najdete v tématu [šablony Azure Resource Manager pro Azure Cosmos DB](./templates-samples-sql.md) .

## <a name="azure-cli"></a>Azure CLI

Azure CLI se dá využít ke zřízení propustnosti automatického škálování u databáze nebo prostředků na úrovni kontejneru pro všechna Azure Cosmos DB rozhraní API. Ukázky najdete v [ukázkách Azure CLI pro Azure Cosmos DB](cli-samples.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell lze použít ke zřízení propustnosti automatického škálování u databáze nebo prostředků na úrovni kontejneru pro všechna rozhraní API Azure Cosmos DB. Ukázky najdete v tématu [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [výhodách zřízené propustnosti pomocí automatického škálování](provision-throughput-autoscale.md#benefits-of-autoscale).
* Naučte se [vybírat mezi ruční a propustností automatického škálování](how-to-choose-offer.md).
* Přečtěte si [Nejčastější dotazy k automatickému škálování](autoscale-faq.md).
