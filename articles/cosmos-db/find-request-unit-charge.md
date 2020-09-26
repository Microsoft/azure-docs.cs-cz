---
title: Vyhledá poplatek za jednotku žádosti (RU) v Azure Cosmos DB
description: Naučte se, jak najít poplatek za jednotku žádosti (RU) pro všechny operace provedené proti Cosmos kontejneru Azure.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2019
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 81660bee32cace458424c9975c74ca7980148e29
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316184"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Najděte poplatek za jednotku žádosti v Azure Cosmos DB

V tomto článku jsou uvedeny různé způsoby, jak můžete najít spotřebu [žádosti](request-units.md) (ru) pro všechny operace provedené proti kontejneru v Azure Cosmos DB. V současné době můžete tuto spotřebu změřit pouze pomocí Azure Portal nebo kontrolou odpovědi odeslané z Azure Cosmos DB prostřednictvím jedné ze sad SDK.

## <a name="sql-core-api"></a>Rozhraní SQL (Core) API

Pokud používáte rozhraní API SQL, máte k dispozici několik možností, jak pro operaci s kontejnerem Azure Cosmos vyhledat spotřebu RU.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

V současné době můžete v Azure Portal vyhledat poplatek za požadavek pouze pro dotaz SQL.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account) a zajistěte ho s daty nebo vyberte existující účet Azure Cosmos, který už obsahuje data.

1. Otevřete podokno **Průzkumník dat** a pak vyberte kontejner, na kterém chcete pracovat.

1. Vyberte **Nový dotaz SQL**.

1. Zadejte platný dotaz a pak vyberte **Spustit dotaz**.

1. Vyberte možnost **statistiky dotazů** a zobrazte si skutečný poplatek za požadavek na vámi prováděnou žádost.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Snímek obrazovky s požadavkem na požadavek na dotaz SQL v Azure Portal":::

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Objekty, které jsou vráceny ze [sady .NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) vystavují `RequestCharge` vlastnost:

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Objekty, které jsou vráceny ze [sady .NET SDK V3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) vystavení `RequestCharge` vlastnosti:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Další informace najdete v tématu [rychlý Start: Vytvoření webové aplikace .NET pomocí účtu SQL API v Azure Cosmos DB](create-sql-api-dotnet.md).

---

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Objekty, které jsou vráceny ze [sady Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) , zveřejňují `getRequestCharge()` metodu:

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

Další informace najdete v tématu [rychlý Start: Vytvoření aplikace Java pomocí Azure Cosmos DB účtu rozhraní SQL API](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Použití sady Node.js SDK

Objekty, které jsou vráceny ze [ sady SDKNode.js](https://www.npmjs.com/package/@azure/cosmos) zpřístupňují `headers` podobjekt, který mapuje všechny hlavičky vrácené podkladovým rozhraním API protokolu HTTP. Poplatek za požadavek je k dispozici pod `x-ms-request-charge` klíčem:

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

Další informace najdete v tématu [rychlý Start: vytvoření Node.js aplikace pomocí Azure Cosmos DB účtu rozhraní SQL API](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Použití sady Python SDK

`CosmosClient`Objekt ze [sady Python SDK](https://pypi.org/project/azure-cosmos/) zpřístupňuje `last_response_headers` slovník, který mapuje všechny hlavičky vrácené podkladovým rozhraním API protokolu HTTP pro poslední spuštěnou operaci. Poplatek za požadavek je k dispozici pod `x-ms-request-charge` klíčem:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Další informace najdete v tématu [rychlý Start: Vytvoření aplikace v Pythonu pomocí Azure Cosmos DB účtu rozhraní SQL API](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

Poplatek za RU je vystavený pomocí [příkazu vlastní databáze](https://docs.mongodb.com/manual/reference/command/) s názvem `getLastRequestStatistics` . Příkaz vrátí dokument, který obsahuje název poslední provedené operace, poplatek za požadavek a jeho trvání. Pokud používáte rozhraní Azure Cosmos DB API pro MongoDB, máte k dispozici několik možností, jak poplatek za RU načíst.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

V současné době můžete poplatek za požadavek najít v Azure Portal jenom pro dotaz.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) a zajistěte ho s daty nebo vyberte existující účet, který už obsahuje data.

1. Otevřete podokno **Průzkumník dat** a pak vyberte kontejner, na kterém chcete pracovat.

1. Vyberte **Nový dotaz**.

1. Zadejte platný dotaz a pak vyberte **Spustit dotaz**.

1. Vyberte možnost **statistiky dotazů** a zobrazte si skutečný poplatek za požadavek na vámi prováděnou žádost.

:::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Snímek obrazovky s požadavkem na požadavek na dotaz MongoDB v Azure Portal":::

### <a name="use-the-mongodb-net-driver"></a>Použití ovladače MongoDB .NET

Při použití [oficiálního ovladače MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/)můžete spustit příkazy voláním `RunCommand` metody pro `IMongoDatabase` objekt. Tato metoda vyžaduje implementaci `Command<>` abstraktní třídy:

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

Další informace najdete v tématu [rychlý Start: Vytvoření webové aplikace .NET pomocí rozhraní Azure Cosmos DB API pro MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Použití ovladače MongoDB Java


Použijete-li [oficiální ovladač Java MongoDB](https://mongodb.github.io/mongo-java-driver/), můžete spustit příkazy voláním `runCommand` metody pro `MongoDatabase` objekt:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Další informace najdete v tématu [rychlý Start: Vytvoření webové aplikace pomocí rozhraní Azure Cosmos DB API pro MongoDB a Java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Použití ovladače Node.js MongoDB

Při použití [oficiálního Node.js ovladače MongoDB](https://mongodb.github.io/node-mongodb-native/)můžete spustit příkazy voláním `command` metody pro `db` objekt:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Další informace najdete v tématu [rychlý Start: migrace stávající webové aplikace MongoDB Node.js do Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Rozhraní Cassandra API

Při provádění operací proti Azure Cosmos DB rozhraní API Cassandra se poplatek za RU vrátí do příchozí datové části jako pole s názvem `RequestCharge` . Poplatky za RU můžete načíst několika způsoby.

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK

Pokud používáte [sadu .NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/), můžete načíst příchozí datovou část v rámci `Info` vlastnosti `RowSet` objektu:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Další informace najdete v tématu [rychlý Start: sestavení aplikace Cassandra pomocí sady .NET SDK a Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Při použití [sady Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)můžete načíst příchozí datovou část voláním `getExecutionInfo()` metody pro `ResultSet` objekt:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Další informace najdete v tématu [rychlý Start: sestavení aplikace Cassandra pomocí sady Java SDK a Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>Rozhraní Gremlin API

Když použijete rozhraní Gremlin API, máte několik možností, jak najít spotřebu RU pro operaci s kontejnerem Azure Cosmos. 

### <a name="use-drivers-and-sdk"></a>Použití ovladačů a sady SDK

Záhlaví vrácená rozhraním API Gremlin jsou namapována na vlastní atributy stavu, které jsou aktuálně umístěny na základě Gremlin .NET a Java SDK. Poplatek za požadavek je k dispozici pod `x-ms-request-charge` klíčem.

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK

Když použijete [sadu SDK Gremlin.NET](https://www.nuget.org/packages/Gremlin.Net/), jsou atributy stavu dostupné v rámci `StatusAttributes` vlastnosti `ResultSet<>` objektu:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Další informace najdete v tématu [rychlý Start: sestavení .NET Framework nebo základní aplikace pomocí účtu rozhraní API Azure Cosmos DB Gremlin](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Když použijete [sadu Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), můžete načíst atributy stavu voláním `statusAttributes()` metody `ResultSet` objektu:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Další informace najdete v tématu [rychlý Start: vytvoření databáze grafu v Azure Cosmos DB pomocí sady Java SDK](create-graph-java.md).

## <a name="table-api"></a>Rozhraní Table API

V současné době je jediná sada SDK, která vrací poplatek za RU za operace s tabulkami, [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). `TableResult`Objekt zpřístupňuje `RequestCharge` vlastnost, která je vyplněna sadou SDK při použití v Azure Cosmos DB rozhraní API pro tabulky:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Další informace najdete v tématu [rychlý Start: Vytvoření aplikace rozhraní API pro tabulky pomocí sady .NET SDK a Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci spotřeby RU najdete v těchto článcích:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizace nákladů na dotazování ve službě Azure Cosmos DB](optimize-cost-queries.md)
* [Globální škálování zřízené propustnosti](scaling-throughput.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Zřízení propustnosti pro kontejner](how-to-provision-container-throughput.md)
* [Monitorování a ladění pomocí metrik v Azure Cosmos DB](use-metrics.md)
