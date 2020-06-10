---
title: Jak nakonfigurovat vícenásobný hlavní server v Azure Cosmos DB
description: Naučte se konfigurovat více hlavních pro aplikace pomocí různých sad SDK v Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.custom: tracking-python
ms.openlocfilehash: 70b9f1aaeaedd7968aeaf09aebe4e53682a60019
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629362"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Nakonfigurujte ve svých aplikacích více hlavních serverů, které používají Azure Cosmos DB

Jakmile je účet vytvořený s povoleným více oblastmi zápisu, musíte ve své aplikaci udělat dvě změny do ConnectionPolicy pro DocumentClient a povolit tak možnosti více hlavních a více domovských stránek v Azure Cosmos DB. V rámci ConnectionPolicy nastavte UseMultipleWriteLocations na hodnotu true a předejte název oblasti, do které je aplikace nasazena, do SetCurrentLocation. Tím se naplní vlastnost PreferredLocations na základě geografické blízkosti z předaného umístění. Pokud se k účtu později přidá nová oblast, aplikace se nebude muset aktualizovat ani znovu nasadit, ale automaticky rozpozná bližší oblast a automaticky se na ni připojí, pokud se k ní dojde v místní události.

> [!Note]
> Účty Cosmos, které jsou zpočátku nakonfigurované s jednou oblastí zápisu, se dají nakonfigurovat na více oblastí zápisu (tj. na vícenásobné hlavní hodnoty) s nulovým časem. Další informace najdete v tématu [Konfigurace oblastí s vícenásobným zápisem](how-to-manage-database-account.md#configure-multiple-write-regions) .

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Pokud chcete v aplikaci povolit více hlavních serverů, nastavte `UseMultipleWriteLocations` na `true` . Nastavte také `SetCurrentLocation` na oblast, ve které se aplikace nasazuje a kam se Azure Cosmos DB replikují:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK V3

Pokud chcete ve své aplikaci povolit více hlavních serverů, nastavte `ApplicationRegion` na oblast, ve které se aplikace nasazuje a kam se Cosmos DB replikují:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Volitelně můžete použít `CosmosClientBuilder` a `WithApplicationRegion` k dosažení stejného výsledku:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a>Sada Java v4 SDK

Chcete-li ve své aplikaci povolit více hlavních serverů, zavolejte `.multipleWriteRegionsEnabled(true)` a `.preferredRegions(preferredRegions)` v Tvůrci klienta, kde `preferredRegions` je `List` obsahující jeden element – to je oblast, ve které se aplikace nasazuje a kde se Cosmos DB replikuje:

### <a id="java4-multi-master"></a>
#### <a name="async"></a>[Async](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

#### <a name="sync"></a>[Synchronizovat](#tab/api-sync)

   [Sada Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk-maven-commicrosoftazureazure-cosmosdb"></a><a id="java"></a>[Async Java v2 SDK](sql-api-sdk-async-java.md) (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

Pokud chcete ve své aplikaci povolit více hlavních serverů, nastavte `policy.setUsingMultipleWriteLocations(true)` a nastavte `policy.setPreferredLocations` na oblast, ve které se aplikace nasazuje a kam se Cosmos DB replikují:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Sady SDK pro Node. js, JavaScript a TypeScript

Pokud chcete v aplikaci povolit více hlavních serverů, nastavte `connectionPolicy.UseMultipleWriteLocations` na `true` . Nastavte také `connectionPolicy.PreferredLocations` na oblast, ve které se aplikace nasazuje a kam se Cosmos DB replikují:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

Pokud chcete v aplikaci povolit více hlavních serverů, nastavte `connection_policy.UseMultipleWriteLocations` na `true` . Nastavte také `connection_policy.PreferredLocations` na oblast, ve které se aplikace nasazuje a kam se Cosmos DB replikují.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články:

* [Použití tokenů relací ke správě konzistence v Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Typy konfliktů a zásady řešení v Azure Cosmos DB](conflict-resolution-policies.md)
* [Vysoká dostupnost v Azure Cosmos DB](high-availability.md)
* [Úrovně konzistence v Azure Cosmos DB](consistency-levels.md)
* [Vyberte správnou úroveň konzistence v Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisy konzistence, dostupnosti a výkonu v Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Zřízená propustnost globálního škálování](scaling-throughput.md)
* [Globální distribuce: pod digestoří](global-dist-under-the-hood.md)
