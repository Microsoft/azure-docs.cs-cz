---
title: Najít jednotky žádosti (RU) poplatek ve službě Azure Cosmos DB
description: Zjistěte, jak najít zátěž jednotek žádostí pro všechny operace pro kontejner služby Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: thweiss
ms.openlocfilehash: 833f815f0c84584f084e4d4637c0318f7c2daec0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683830"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>Najít jednotky žádosti (RU) poplatek ve službě Azure Cosmos DB

Tento článek představuje různé způsoby, jak najít [jednotky žádosti](request-units.md) využití pro všechny operace pro kontejner služby Azure Cosmos. Je aktuálně možné měření toto využití pomocí webu Azure portal nebo zkontrolováním odpověď odesílanou obnovení z Azure Cosmos DB pomocí jedné ze sad SDK.

## <a name="sql-core-api"></a>SQL (Core) rozhraní API

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Na webu Azure portal v současné době umožňuje najít zátěž žádostí pro pouze dotaz SQL.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account) a kanál s daty, nebo vyberte existující účet Azure Cosmos, který již obsahuje data.

1. Otevřít **Průzkumník dat** podokně a vyberte kontejner, který chcete pracovat.

1. Klikněte na **nový příkaz jazyka SQL**.

1. Zadejte platný dotaz a potom klikněte na **provést dotaz**.

1. Klikněte na **statistiky dotazů** zobrazit skutečnou žádost poplatek za žádost jste právě spustili.

![Snímek obrazovky s SQL query zátěž žádostí na portálu Azure portal](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Používat .NET SDK V2

Objektů vrácených z [sady .NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (naleznete v tématu [v tomto rychlém startu](create-sql-api-dotnet.md) související s jeho využitím) vystavit `RequestCharge` vlastnost.

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

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Objektů vrácených z [sady Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (naleznete v tématu [v tomto rychlém startu](create-sql-api-java.md) související s jeho využitím) vystavit `getRequestCharge()` metody.

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

### <a name="use-the-nodejs-sdk"></a>Použití sady SDK pro Node.js

Objektů vrácených z [sady Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (naleznete v tématu [v tomto rychlém startu](create-sql-api-nodejs.md) související s jeho využitím) vystavit `headers` dílčí objekt, který mapuje všechny hlavičky vrácený základního rozhraní API protokolu HTTP. Poplatek za žádost je k dispozici v rámci `x-ms-request-charge` klíč.

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

### <a name="use-the-python-sdk"></a>Použít Python SDK

`CosmosClient` Objektu z [Python SDK](https://pypi.org/project/azure-cosmos/) (naleznete v tématu [v tomto rychlém startu](create-sql-api-python.md) související s jeho využitím) poskytuje `last_response_headers` slovník, který mapuje všechny hlavičky vrácený základního rozhraní HTTP API pro poslední operaci provést. Poplatek za žádost je k dispozici v rámci `x-ms-request-charge` klíč.

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

Poplatek za jednotky žádosti je zveřejněný prostřednictvím vlastní [databáze příkaz](https://docs.mongodb.com/manual/reference/command/) s názvem `getLastRequestStatistics.` tento příkaz vrátí dokument obsahující název poslední operaci provést, jeho zátěž žádostí a jeho trvání.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Na webu Azure portal v současné době umožňuje najít zátěž žádostí pro dotaz pouze.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) a kanál s daty, nebo vyberte existující účet, který již obsahuje data.

1. Otevřít **Průzkumník dat** podokně a vyberte kolekci, kterou chcete pracovat.

1. Klikněte na **nový dotaz**.

1. Zadejte platný dotaz a potom klikněte na **provést dotaz**.

1. Klikněte na **statistiky dotazů** zobrazit skutečnou žádost poplatek za žádost jste právě spustili.

![Snímek obrazovky s MongoDB zátěž žádostí dotazu na portálu Azure portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Použití ovladače MongoDB .NET

Při použití [oficiální ovladač MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/) (naleznete v tématu [v tomto rychlém startu](create-mongodb-dotnet.md) související s jeho využitím), příkazy mohou být provedeny voláním `RunCommand` metoda `IMongoDatabase` objektu. Tato metoda vyžaduje implementace `Command<>` abstraktní třídy.

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

### <a name="use-the-mongodb-java-driver"></a>Použít ovladači MongoDB Java

Při použití [oficiální ovladači MongoDB Java](http://mongodb.github.io/mongo-java-driver/) (naleznete v tématu [v tomto rychlém startu](create-mongodb-java.md) související s jeho využitím), příkazy mohou být provedeny voláním `runCommand` metoda `MongoDatabase` objektu.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>Použijte ovladač MongoDB Node.js

Při použití [oficiální ovladač MongoDB Node.js](https://mongodb.github.io/node-mongodb-native/) (naleznete v tématu [v tomto rychlém startu](create-mongodb-nodejs.md) související s jeho využitím), příkazy mohou být provedeny voláním `command` metoda `db` objektu.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>Rozhraní Cassandra API

Při provádění operací s využitím služby Azure Cosmos DB Cassandra API zátěž jednotky žádostí se vrátí v příchozí datové části jako pole s názvem `RequestCharge`.

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK

Při použití [sady .NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) (naleznete v tématu [v tomto rychlém startu](create-cassandra-dotnet.md) související s jeho využitím), příchozí datové části je možné načíst podle `Info` vlastnost `RowSet` objektu.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Při použití [sady Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) (naleznete v tématu [v tomto rychlém startu](create-cassandra-java.md) související s jeho využitím), příchozí datové části je možné načíst pomocí volání `getExecutionInfo()` metodu na `ResultSet` objektu.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>Rozhraní Gremlin API

### <a name="use-drivers-and-sdk"></a>Použití ovladače a sady SDK

Záhlaví vrácené rozhraní Gremlin API nevztahují se mapují na vlastní stav atributy, které jsou aktuálně prezentované Gremlin .NET a sady Java SDK. Poplatek za žádost je k dispozici v rámci `x-ms-request-charge` klíč.

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK

Při použití [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/) (naleznete v tématu [v tomto rychlém startu](create-graph-dotnet.md) související s jeho využitím), jsou k dispozici v části atributy stav `StatusAttributes` vlastnost `ResultSet<>` objektu.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>Použití sady Java SDK

Při použití [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) (naleznete v tématu [v tomto rychlém startu](create-graph-java.md) související s jeho využitím), stav atributů může být načten voláním `statusAttributes()` metodu `ResultSet` objektu.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>Rozhraní Table API

Aktuálně vrací zátěž jednotek žádostí pro operace s tabulkou pouze sada SDK je [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) (naleznete v tématu [v tomto rychlém startu](create-table-dotnet.md) související s jeho využitím). `TableResult` Objektu zpřístupňuje `RequestCharge` vlastnost, která naplní při použití s Azure Cosmos DB Table API SDK.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>Další postup

Naleznete v následujících článcích se dozvíte informace o optimalizaci spotřebu jednotek žádosti:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
* [Optimalizujte náklady zřízenou propustnost v databázi Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizujte náklady dotazu ve službě Azure Cosmos DB](optimize-cost-queries.md)
* [Globálně škálujte zřízená propustnost](scaling-throughput.md)
* [Zřizování propustnosti na kontejnerech a databází](set-throughput.md)
* [Zřízení propustnosti pro kontejner](how-to-provision-container-throughput.md)