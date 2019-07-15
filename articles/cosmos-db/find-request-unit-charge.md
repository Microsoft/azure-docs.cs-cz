---
title: Najít jednotky žádosti (RU) poplatek ve službě Azure Cosmos DB
description: Zjistěte, jak najít jednotky žádosti (RU) poplatků pro všechny operace pro kontejner služby Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/14/2019
ms.author: thweiss
ms.openlocfilehash: 3d088da4c771c828db9788817e424c4d89586dd6
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986148"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Najít zátěž jednotky žádostí ve službě Azure Cosmos DB

Tento článek představuje různé způsoby, jak můžete najít [jednotky žádosti](request-units.md) spotřebu (RU) pro všechny operace pro kontejner ve službě Azure Cosmos DB. V současné době můžete měřit toto využití pouze pomocí webu Azure portal nebo zkontrolováním odpověď odesílanou obnovení z Azure Cosmos DB pomocí jedné ze sad SDK.

## <a name="sql-core-api"></a>Rozhraní SQL (Core) API

Pokud používáte rozhraní SQL API, máte několik možností pro vyhledání spotřebu RU pro operace kontejneru Azure Cosmos.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

V současné době najdete poplatek za žádost na webu Azure Portal pouze pro dotaz SQL.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account) a kanál s daty, nebo vyberte existující účet Azure Cosmos, který již obsahuje data.

1. Přejděte **Průzkumník dat** podokně a pak vyberte kontejner, kterým chcete pracovat v.

1. Vyberte **nový příkaz jazyka SQL**.

1. Zadejte platný dotaz a pak vyberte **provést dotaz**.

1. Vyberte **statistiky dotazů** zobrazit skutečnou žádost poplatek za žádost jste spustili.

![Snímek obrazovky žádosti poplatek dotaz SQL na webu Azure Portal](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK
### <a name="net-v2-sdk"></a>.Net V2 SDK

Objekty, které jsou vráceny z [sady .NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) vystavit `RequestCharge` vlastnost:

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

### <a name="net-v3-sdk"></a>.Net V3 SDK

Objekty, které jsou vráceny z [sady .NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) vystavit `RequestCharge` vlastnost:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Další informace najdete v tématu [rychlý start: Vytvoření webové aplikace .NET s využitím účtu rozhraní SQL API ve službě Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Objekty, které jsou vráceny z [sady Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) vystavit `getRequestCharge()` metody:

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

Další informace najdete v tématu [rychlý start: Vytvoření aplikace Java pomocí účtu rozhraní SQL API služby Azure Cosmos DB](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Použití sady SDK pro Node.js

Objekty, které jsou vráceny z [sady Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) vystavit `headers` podobjekt, který mapuje všechny hlavičky vrácený základního rozhraní API protokolu HTTP. Poplatek za žádost je k dispozici v rámci `x-ms-request-charge` klíč:

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

Další informace najdete v tématu [rychlý start: Vytvoření aplikace Node.js pomocí účtu rozhraní SQL API služby Azure Cosmos DB](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Použít Python SDK

`CosmosClient` Objektu z [Python SDK](https://pypi.org/project/azure-cosmos/) zpřístupňuje `last_response_headers` slovník, který mapuje všechny hlavičky vrácený základního rozhraní HTTP API pro poslední operaci provést. Poplatek za žádost je k dispozici v rámci `x-ms-request-charge` klíč:

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

Další informace najdete v tématu [rychlý start: Sestavení aplikace v Pythonu pomocí účtu rozhraní SQL API služby Azure Cosmos DB](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

Poplatek za RU je zveřejněný prostřednictvím vlastní [databáze příkaz](https://docs.mongodb.com/manual/reference/command/) s názvem `getLastRequestStatistics`. Příkaz vrátí dokument, který obsahuje název poslední operaci provést, jeho zátěž žádostí a jeho trvání. Pokud používáte pro MongoDB API služby Azure Cosmos DB, máte několik možností pro načtení poplatek RU.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

V současné době najdete poplatek za žádost na webu Azure Portal pouze pro dotaz.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) a kanál s daty, nebo vyberte existující účet, který již obsahuje data.

1. Přejděte **Průzkumník dat** podokně a pak vyberte kolekce, kterým chcete pracovat v.

1. Vyberte **Nový dotaz**.

1. Zadejte platný dotaz a pak vyberte **provést dotaz**.

1. Vyberte **statistiky dotazů** zobrazit skutečnou žádost poplatek za žádost jste spustili.

![Snímek obrazovky žádosti poplatek dotazu MongoDB na webu Azure Portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Použití ovladače MongoDB .NET

Při použití [oficiální ovladač MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/), můžete spustit příkazy voláním `RunCommand` metodu na `IMongoDatabase` objektu. Tato metoda vyžaduje implementace `Command<>` abstraktní třídy:

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

Další informace najdete v tématu [rychlý start: Vytvoření webové aplikace .NET pomocí rozhraní API Azure Cosmos DB pro MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Použít ovladači MongoDB Java


Při použití [oficiální ovladači MongoDB Java](https://mongodb.github.io/mongo-java-driver/), můžete spustit příkazy voláním `runCommand` metodu na `MongoDatabase` objektu:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Další informace najdete v tématu [rychlý start: Vytvoření webové aplikace pomocí rozhraní Azure Cosmos DB API pro MongoDB a sady Java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Použijte ovladač MongoDB Node.js

Při použití [oficiální ovladač MongoDB Node.js](https://mongodb.github.io/node-mongodb-native/), můžete spustit příkazy voláním `command` metodu na `db` objektu:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Další informace najdete v tématu [rychlý start: Migrace stávající webové aplikace Node.js s MongoDB ke službě Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Rozhraní Cassandra API

Při provádění operací s Cassandra API služby Azure Cosmos DB RU poplatek se vrátí v příchozí datové části jako pole s názvem `RequestCharge`. Máte několik možností pro načtení poplatek RU.

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK

Při použití [sady .NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/), můžete načíst příchozí datové části v rámci `Info` vlastnost `RowSet` objektu:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Další informace najdete v tématu [rychlý start: Sestavení aplikace Cassandra pomocí .NET SDK a Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Při použití [sady Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), příchozí datovou část můžete získat pomocí volání `getExecutionInfo()` metodu na `ResultSet` objektu:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Další informace najdete v tématu [rychlý start: Sestavení aplikace Cassandra pomocí sady Java SDK a Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>Rozhraní Gremlin API

Pokud používáte rozhraní Gremlin API nevztahují, máte několik možností pro vyhledání spotřebu RU pro operace kontejneru Azure Cosmos. 

### <a name="use-drivers-and-sdk"></a>Použití ovladače a sady SDK

Záhlaví vrácené rozhraní Gremlin API nevztahují se mapují na vlastní stav atributy, které jsou aktuálně prezentované Gremlin .NET a sady Java SDK. Poplatek za žádost je k dispozici v rámci `x-ms-request-charge` klíč.

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK

Při použití [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/), jsou k dispozici v části atributy stav `StatusAttributes` vlastnost `ResultSet<>` objektu:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Další informace najdete v tématu [rychlý start: Vytvoření aplikace .NET Framework nebo .NET Core pomocí účtu Azure Cosmos DB Gremlin API](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Při použití [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), můžete načíst stav atributy voláním `statusAttributes()` metodu `ResultSet` objektu:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Další informace najdete v tématu [rychlý start: Vytvořit databázi grafu ve službě Azure Cosmos DB pomocí sady Java SDK](create-graph-java.md).

## <a name="table-api"></a>Rozhraní Table API

V současné době je pouze sadu SDK, která vrací RU poplatky za operace s tabulkou [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). `TableResult` Objektu zpřístupňuje `RequestCharge` vlastnost, která je vyplněn sady SDK při použití s Azure Cosmos DB Table API:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Další informace najdete v tématu [rychlý start: Sestavení aplikace Table API s použitím sady .NET SDK a Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Další postup

Další informace o optimalizaci spotřebu RU, najdete v těchto článcích:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizujte náklady dotazu ve službě Azure Cosmos DB](optimize-cost-queries.md)
* [Globálně škálujte zřízená propustnost](scaling-throughput.md)
* [Zřizování propustnosti na kontejnerech a databází](set-throughput.md)
* [Zřizování propustnosti kontejneru](how-to-provision-container-throughput.md)
* [Monitorování a ladění s využitím metrik ve službě Azure Cosmos DB](use-metrics.md)
