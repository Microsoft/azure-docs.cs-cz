---
title: Migrace aplikace tak, aby používala sadu Azure Cosmos DB .NET SDK 3,0 (com. Azure. Cosmos)
description: Naučte se upgradovat stávající aplikaci .NET ze sady v2 SDK na novější sadu .NET SDK V3 (balíček com. Azure. Cosmos) pro rozhraní API Core (SQL).
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 3cddf76ae0157c6729e6f58e40ee3f1725a9d395
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096661"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Migrace aplikace tak, aby používala sadu Azure Cosmos DB .NET SDK V3
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Další informace o sadě Azure Cosmos DB .NET SDK V3 naleznete v [poznámkách k verzi](sql-api-sdk-dotnet-standard.md), [úložišti GitHub .NET](https://github.com/Azure/azure-cosmos-dotnet-v3), [tipy pro výkon](performance-tips-dotnet-sdk-v3-sql.md)sady .NET SDK v3 a v [Průvodci odstraňováním potíží](troubleshoot-dot-net-sdk.md).
>

Tento článek popisuje některé z informací o tom, jak upgradovat stávající aplikaci .NET na novější Azure Cosmos DB rozhraní API .NET SDK V3 for Core (SQL) API. Azure Cosmos DB .NET SDK V3 odpovídá oboru názvů Microsoft. Azure. Cosmos. Informace uvedené v tomto dokumentu můžete použít při migraci aplikace z některé z následujících Azure Cosmos DB sad .NET SDK:

* Azure Cosmos DB .NET Framework SDK v2 pro SQL API
* Azure Cosmos DB .NET Core SDK v2 pro rozhraní SQL API

Pokyny v tomto článku vám také pomůžou migrovat následující externí knihovny, které jsou teď součástí Azure Cosmos DB rozhraní API .NET SDK v3 pro Core (SQL):

* Knihovna pro změny rozhraní .NET Change feed 2,0
* Knihovna hromadného prováděcího modulu .NET 1,1 nebo vyšší

## <a name="whats-new-in-the-net-v3-sdk"></a>Co je nového v sadě .NET V3 SDK

Sada V3 SDK obsahuje mnoho vylepšení použitelnosti a výkonu, včetně:

* Intuitivní programovací model pojmenovávání
* .NET Standard 2,0 * *
* Zvýšení výkonu prostřednictvím podpory Stream API
* Hierarchie Fluent, která nahrazuje potřebu objektu pro vytváření identifikátorů URI
* Integrovaná podpora pro knihovnu Change feed Processor
* Integrovaná podpora pro hromadné operace
* Rozhraní Mockabale API pro snazší testování částí
* Transakční dávka a podpora Blazor
* Připojitelná serializátory
* Škálování kontejnerů bez oddílů a automatického škálování

* Sada SDK cílí na .NET Standard 2,0, která sjednocuje existující Azure Cosmos DB .NET Framework a sady .NET Core SDK na jednu sadu .NET SDK. Sadu .NET SDK můžete použít na libovolné platformě, která implementuje .NET Standard 2,0, včetně vašich aplikací .NET Framework 4.6.1 + a .NET Core 2.0 +.

Většina sítí, logiky opakování a nižší úrovně sady SDK zůstávají převážně nezměněné.

**Sada Azure Cosmos DB .NET SDK v3 je nyní open source.** Vítáme všechny žádosti o přijetí změn, které budou protokolovat problémy a sledovat názory na [GitHubu.](https://github.com/Azure/azure-cosmos-dotnet-v3/) Budeme pracovat na všech funkcích, které budou vylepšit prostředí pro zákazníky.

## <a name="why-migrate-to-the-net-v3-sdk"></a>Proč migrovat do sady .NET V3 SDK

Kromě mnoha vylepšení použitelnosti a výkonu se nové investice do funkcí v nejnovější sadě SDK nedají přenést na starší verze.

I když nejsou k dispozici žádné okamžité plány k [vyřazení podpory pro sady sdk 2,0](sql-api-sdk-dotnet.md), sady SDK budou v budoucnu nahrazeny novějšími verzemi a sada SDK bude přejít do režimu údržby. Pro nejlepší vývojové prostředí doporučujeme vždy začít s nejnovější podporovanou verzí sady SDK.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>Změny hlavních názvů ze sady v2 SDK na verzi V3 SDK

Následující změny názvů se v rámci sady .NET 3,0 SDK používaly, aby byly v souladu se zásadami vytváření názvů rozhraní API pro základní rozhraní API (SQL).

* `DocumentClient` je přejmenován na `CosmosClient`
* `Collection` je přejmenován na `Container`
* `Document` je přejmenován na `Item`

Všechny objekty prostředků jsou přejmenovány pomocí dalších vlastností, které obsahují název prostředku pro přehlednost.

Níže jsou uvedeny některé změny názvu hlavní třídy:

| Sada .NET v2 SDK | Sada .NET V3 SDK |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>Třídy nahrazené rozhraním .NET V3 SDK

V sadě SDK 3,0 byly nahrazeny následující třídy:

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

Microsoft.Azure.Documents. UriFactory třída byla nahrazena návrhem Fluent. Design Fluent vytváří adresy URL interně a umožňuje `Container` předávat jeden objekt místo `DocumentClient` , `DatabaseName` a `DocumentCollection` .

### <a name="changes-to-item-id-generation"></a>Změny pro generování ID položky

ID položky již není automaticky vyplněno v sadě .NET V3 SDK. Proto ID položky musí specificky zahrnovat vygenerované ID. Podívejte se na následující příklad:

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>Změna výchozího chování pro režim připojení

Sada SDK V3 teď standardně používá přímé režimy připojení + TCP v porovnání s předchozí sadou v2 SDK, která je nastavená na režimy připojení brány + HTTPS. Tato změna poskytuje vyšší výkon a škálovatelnost.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>Změny FeedOptions (QueryRequestOptions v sadě SDK sady v 3.0)

`FeedOptions`Třída v sadě SDK v2 byla nyní přejmenována na `QueryRequestOptions` v sadě SDK v3 a v rámci třídy, některé vlastnosti byly provedeny v názvu a/nebo ve výchozí hodnotě nebo byly zcela odstraněny.  

`FeedOptions.MaxDegreeOfParallelism` bylo přejmenováno na `QueryRequestOptions.MaxConcurrency` výchozí hodnotu a související chování zůstane stejné, operace spustit klienta během paralelního provádění dotazů budou spouštěny sériově bez paralelního zpracování dotazu.

`FeedOptions.EnableCrossPartitionQuery` bylo odebráno a výchozí chování sady SDK 3,0 je, že dotazy na více oddílů budou provedeny bez nutnosti povolit vlastnost konkrétně.

`FeedOptions.PopulateQueryMetrics` je ve výchozím nastavení povolen s výsledky, které jsou k dispozici ve vlastnosti Diagnostics odpovědi.

`FeedOptions.RequestContinuation` byl nyní povýšen na samotné metody dotazů.

Byly odebrány následující vlastnosti:

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>Sestavování klienta

Sada .NET SDK V3 poskytuje třídu Fluent `CosmosClientBuilder` , která nahrazuje potřebu objektu pro vytváření identifikátorů URI sady SDK v2.

Následující příklad vytvoří nový `CosmosClientBuilder` se silným ConsistencyLevelem a seznamem upřednostňovaných umístění:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>Použití rozhraní API Change feed Processor přímo ze sady V3 SDK

Sada V3 SDK obsahuje integrovanou podporu rozhraní API pro změnu kanálu, což vám umožní používat stejnou sadu SDK pro vytváření aplikací a změnu implementace procesoru kanálu. Dřív jste museli používat samostatnou knihovnu Change feed Processor.

Další informace najdete v tématu [migrace z knihovny Change feed Processor do sady Azure Cosmos DB .NET V3 SDK](how-to-migrate-from-change-feed-library.md) .

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>Použití knihovny hromadných prováděcích modulů přímo ze sady V3 SDK

Sada V3 SDK obsahuje integrovanou podporu pro knihovnu hromadného prováděcího modulu, která umožňuje použít stejnou sadu SDK pro sestavení aplikace a provádění hromadných operací. Dříve jste museli použít samostatnou knihovnu hromadných prováděcích modulů.

Další informace najdete v tématu [Postup migrace z knihovny hromadného prováděcího modulu na hromadnou podporu v sadě Azure Cosmos DB .NET V3 SDK](how-to-migrate-from-bulk-executor-library.md) .

## <a name="code-snippet-comparisons"></a>Porovnání fragmentů kódu

Následující fragment kódu ukazuje rozdíly v způsobu vytváření prostředků mezi sadami .NET v2 a V3 SDK:

## <a name="database-operations"></a>Databázové operace

### <a name="create-a-database"></a>Vytvoření databáze

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>Čtení databáze podle ID

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>Odstranění databáze

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>Operace kontejneru

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>Vytvoření kontejneru (automatické škálování + doba do provozu s vypršením platnosti)

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>Načíst vlastnosti kontejneru

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>Odstranění kontejneru

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>Operace s položkami a dotazy

### <a name="create-an-item"></a>Vytvořit položku

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>Načte všechny položky v kontejneru.

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>Položky dotazu

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>Odstranění položky

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>Další kroky

* [Vytvoření konzolové aplikace](sql-api-get-started.md) pro správu Azure Cosmos DB dat rozhraní SQL API pomocí sady V3 SDK
* Další informace o [tom, co můžete dělat se sadou V3 SDK](sql-api-dotnet-v3sdk-samples.md)
