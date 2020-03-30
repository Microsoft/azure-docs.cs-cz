---
title: Vyhledání poplatku za jednotku požadavku (RU) v Azure Cosmos DB
description: Zjistěte, jak najít poplatek jednotky požadavku (RU) pro všechny operace provedené proti kontejneru Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: c5699bb851bd0a818a987228155c62683e93f51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585896"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Vyhledání poplatku za jednotku požadavku v Azure Cosmos DB

Tento článek představuje různé způsoby, jak můžete najít spotřebu [jednotky požadavku](request-units.md) (RU) pro všechny operace provedené proti kontejneru v Azure Cosmos DB. V současné době můžete měřit tuto spotřebu pouze pomocí portálu Azure nebo kontrolou odpovědi odeslané zpět z Azure Cosmos DB prostřednictvím jednoho z sad SDK.

## <a name="sql-core-api"></a>Rozhraní SQL (Core) API

Pokud používáte rozhraní SQL API, máte několik možností pro nalezení spotřeby ru pro operaci proti kontejneru Azure Cosmos.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

V současné době můžete najít poplatek za požadavek na webu Azure Portal jenom pro dotaz SQL.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account) a napájejte ho daty, nebo vyberte existující účet Azure Cosmos, který už data obsahuje.

1. Přejděte do podokna **Průzkumník dat** a vyberte kontejner, na kterém chcete pracovat.

1. Vyberte **Nový dotaz SQL**.

1. Zadejte platný dotaz a vyberte **Spustit dotaz**.

1. Vyberte **Statistiky dotazu,** chcete-li zobrazit skutečný poplatek za požadavek, který jste provedli.

![Snímek obrazovky s poplatkem za požadavek na dotaz SQL na webu Azure Portal](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK
### <a name="net-v2-sdk"></a>Sada SDK v síti V2

Objekty, které jsou vráceny z [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) vystavit `RequestCharge` vlastnost:

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="net-v3-sdk"></a>Sada SDK v síti Net V3

Objekty, které jsou vráceny z [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) vystavit `RequestCharge` vlastnost:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Další informace najdete [v tématu Úvodní příručka: Vytvoření webové aplikace .NET pomocí účtu SQL API v Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Objekty, které jsou vráceny z `getRequestCharge()` sady Java [SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) vystavit metodu:

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

Další informace najdete [v tématu Úvodní příručka: Vytvoření aplikace Java pomocí účtu SQL API Azure Cosmos DB](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Použití sady SDK Node.js

Objekty, které jsou vráceny z [sady Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) vystavit `headers` podobjekt, který mapuje všechny hlavičky vrácené základní rozhraní HTTP API. Poplatek za žádost `x-ms-request-charge` je k dispozici pod klíčem:

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

Další informace najdete [v tématu Úvodní příručka: Vytvoření aplikace Node.js pomocí účtu SQL API Azure Cosmos DB](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Použití sady Python SDK

Objekt `CosmosClient` z [Pythonu SDK](https://pypi.org/project/azure-cosmos/) `last_response_headers` zpřístupňuje slovník, který mapuje všechny hlavičky vrácené základní rozhraní HTTP API pro poslední operaci provedeny. Poplatek za žádost `x-ms-request-charge` je k dispozici pod klíčem:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Další informace najdete [v tématu Úvodní příručka: Vytvoření aplikace Pythonu pomocí účtu SQL API Azure Cosmos DB](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

Poplatek ŽP je vystaven příkazem vlastní [databáze](https://docs.mongodb.com/manual/reference/command/) s názvem `getLastRequestStatistics`. Příkaz vrátí dokument, který obsahuje název poslední provedené operace, jeho požadavek poplatek a jeho trvání. Pokud používáte rozhraní API Db Azure Cosmos pro MongoDB, máte několik možností pro načtení poplatku RU.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

V současné době můžete najít poplatek za požadavek na webu Azure Portal jenom pro dotaz.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) a napájejte ho daty nebo vyberte existující účet, který už data obsahuje.

1. Přejděte do podokna **Průzkumník dat** a vyberte kontejner, na kterém chcete pracovat.

1. Vyberte **Nový dotaz**.

1. Zadejte platný dotaz a vyberte **Spustit dotaz**.

1. Vyberte **Statistiky dotazu,** chcete-li zobrazit skutečný poplatek za požadavek, který jste provedli.

![Snímek obrazovky s poplatkem za požadavek na dotaz MongoDB na webu Azure Portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Použití ovladače MongoDB .NET

Při použití [oficiálního ovladače MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/)můžete spouštět příkazy voláním `RunCommand` metody na objektu. `IMongoDatabase` Tato metoda vyžaduje implementaci `Command<>` abstraktní třídy:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Další informace najdete [v tématu Úvodní příručka: Vytvoření webové aplikace .NET pomocí rozhraní API Azure Cosmos DB pro MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Použití ovladače MongoDB Java


Při použití [oficiálního ovladače Jazyka Java MongoDB](https://mongodb.github.io/mongo-java-driver/)můžete `runCommand` spouštět `MongoDatabase` příkazy voláním metody na objektu:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Další informace najdete [v tématu Úvodní příručka: Vytvoření webové aplikace pomocí rozhraní Azure Cosmos DB API pro MongoDB a Java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Použití ovladače MongoDB Node.js

Použijete-li [oficiální ovladač MongoDB Node.js](https://mongodb.github.io/node-mongodb-native/), můžete `command` spouštět `db` příkazy voláním metody na objektu:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Další informace najdete [v tématu Úvodní příručka: Migrace existující webové aplikace MongoDB Node.js do Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Rozhraní Cassandra API

Při provádění operací s rozhraním API Azure Cosmos DB Cassandra je poplatek ru `RequestCharge`vrácen v příchozí datové části jako pole s názvem . Poplatky za RU můžete načíst několika způsoby.

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK

Při použití sady [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)můžete načíst příchozí `Info` datovou `RowSet` část pod vlastnostobjektu:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Další informace najdete [v tématu Úvodní příručka: Vytvoření aplikace Cassandra pomocí .NET SDK a Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Při použití [sady Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)můžete načíst příchozí datovou část voláním `getExecutionInfo()` metody na objekt: `ResultSet`

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Další informace najdete [v tématu Úvodní příručka: Vytvoření aplikace Cassandra pomocí sady Java SDK a Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>Rozhraní Gremlin API

Při použití rozhraní GREMLIN ROZHRANÍ API, máte několik možností pro nalezení spotřeby ru pro operaci proti kontejneru Azure Cosmos. 

### <a name="use-drivers-and-sdk"></a>Použití ovladačů a sady SDK

Záhlaví vrácená rozhraním GREMLIN API jsou mapována na vlastní atributy stavu, které jsou aktuálně vystavovány sadou Gremlin .NET a Java SDK. Poplatek za požadavek `x-ms-request-charge` je k dispozici pod klíčem.

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK

Při použití [sady Gremlin.NET Sady SDK](https://www.nuget.org/packages/Gremlin.Net/)jsou `StatusAttributes` atributy `ResultSet<>` stavu k dispozici pod vlastností objektu:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Další informace najdete [v tématu Úvodní příručka: Vytvoření rozhraní .NET Framework nebo základní aplikace pomocí účtu rozhraní API Azure Cosmos DB Gremlin](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Při použití [sady Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)můžete načíst `statusAttributes()` atributy `ResultSet` stavu voláním metody na objektu:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Další informace najdete [v tématu Úvodní příručka: Vytvoření databáze grafu v Azure Cosmos DB pomocí sady Java SDK](create-graph-java.md).

## <a name="table-api"></a>Rozhraní Table API

V současné době pouze SDK, který vrací ru poplatek za operace tabulky je [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Objekt `TableResult` zpřístupňuje `RequestCharge` vlastnost, která je naplněna sadou SDK při použití proti rozhraní API tabulky Db Azure Cosmos:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Další informace najdete [v tématu Úvodní příručka: Vytvoření aplikace rozhraní TABLE API pomocí sady .NET SDK a Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci spotřeby ru, najdete v těchto článcích:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizace nákladů na dotazování ve službě Azure Cosmos DB](optimize-cost-queries.md)
* [Globální škálování zřízené propustnosti](scaling-throughput.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Zajištění propustnost pro kontejner](how-to-provision-container-throughput.md)
* [Monitorování a ladění metrik v Azure Cosmos DB](use-metrics.md)
