---
title: Najít poplatek za jednotku žádosti (RU) pro dotaz SQL v Azure Cosmos DB
description: Naučte se, jak najít poplatek za jednotku žádosti (RU) pro dotazy SQL provedené proti kontejneru Azure Cosmos. K vyhledání poplatků za RU můžete použít jazyky Azure Portal, .NET, Java, Python a Node.js.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: f716245d93727a0447bd1c67924ce7577c70b503
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201301"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>Vyhledá poplatky za jednotky žádosti pro operace spouštěné v Azure Cosmos DB SQL API.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB podporuje mnoho rozhraní API, například SQL, MongoDB, Cassandra, Gremlin a Table. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace jsou v rozsahu od jednoduchých čtení a zápisů do složitých dotazů. Každá databázová operace spotřebovává systémové prostředky na základě složitosti operace.

Azure Cosmos DB normalizuje náklady na všechny databázové operace a vyjadřuje je v jednotkách žádostí (zkráceně RU). Poplatek za požadavek je počet jednotek žádostí spotřebovaných všemi vašimi databázovými operacemi. Ru si můžete představit jako měnu výkonu pro abstrakci systémových prostředků, jako jsou například CPU, IOPS a paměť, které jsou potřeba k provádění databázových operací, které Azure Cosmos DB podporuje. Bez ohledu na to, jaké rozhraní API používáte k interakci s kontejnerem Azure Cosmos, se náklady vždy měří v jednotkách RU. Bez ohledu na to, jestli je databázová operace zápis, čtení z bodu nebo dotazování, se náklady vždycky měří v ru. Další informace najdete v tématu [jednotky žádostí a jejich informace](request-units.md) .

Tento článek popisuje různé způsoby, jak můžete najít spotřebu [jednotky žádosti](request-units.md) (ru) pro všechny operace provedené proti kontejneru v Azure Cosmos DB SQL API. Pokud používáte jiné rozhraní API, přečtěte si téma [rozhraní API pro MongoDB](find-request-unit-charge-mongodb.md), [rozhraní API Cassandra](find-request-unit-charge-cassandra.md), [rozhraní Gremlin API](find-request-unit-charge-gremlin.md)a [rozhraní API pro tabulky](find-request-unit-charge-table.md) články, které vám pomohou najít poplatek za ru/s.

V současné době můžete tuto spotřebu změřit pouze pomocí Azure Portal nebo kontrolou odpovědi odeslané z Azure Cosmos DB prostřednictvím jedné ze sad SDK. Pokud používáte rozhraní API SQL, máte k dispozici několik možností, jak pro operaci s kontejnerem Azure Cosmos vyhledat spotřebu RU.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account) a zajistěte ho s daty nebo vyberte existující účet Azure Cosmos, který už obsahuje data.

1. Otevřete podokno **Průzkumník dat** a pak vyberte kontejner, na kterém chcete pracovat.

1. Vyberte **Nový dotaz SQL**.

1. Zadejte platný dotaz a pak vyberte **Spustit dotaz**.

1. Vyberte možnost **statistiky dotazů** a zobrazte si skutečný poplatek za požadavek na vámi prováděnou žádost.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Snímek obrazovky s požadavkem na požadavek na dotaz SQL v Azure Portal":::

## <a name="use-the-net-sdk"></a>Použití sady .NET SDK

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

## <a name="use-the-java-sdk"></a>Použití sady Java SDK

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

## <a name="use-the-nodejs-sdk"></a>Použití sady Node.js SDK

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

## <a name="use-the-python-sdk"></a>Použití sady Python SDK

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

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci spotřeby RU najdete v těchto článcích:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizace nákladů na dotazování ve službě Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Globální škálování zřízené propustnosti](./request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Zřízení propustnosti pro kontejner](how-to-provision-container-throughput.md)
* [Monitorování a ladění pomocí metrik v Azure Cosmos DB](use-metrics.md)
