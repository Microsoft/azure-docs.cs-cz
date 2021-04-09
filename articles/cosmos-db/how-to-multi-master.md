---
title: Jak nakonfigurovat zápisy ve více oblastech v Azure Cosmos DB
description: Naučte se konfigurovat zápisy ve více oblastech pro aplikace pomocí různých sad SDK v Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, "seo-nov-2020"
ms.openlocfilehash: 08d50b18605fd833e6b0efca987338d0ca1eef8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102488507"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurace zápisů ve více oblastech v aplikacích, které používají Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Jakmile je účet vytvořený s povoleným více oblastmi zápisu, musíte v aplikaci udělat dvě změny do ConnectionPolicy pro klienta Cosmos a povolit tak zápisy do více oblastí v Azure Cosmos DB. V rámci ConnectionPolicy nastavte UseMultipleWriteLocations na hodnotu true a předejte název oblasti, do které je aplikace nasazena, do ApplicationRegion. Tím se naplní vlastnost PreferredLocations na základě geografické blízkosti z předaného umístění. Pokud se k účtu později přidá nová oblast, aplikace se nebude muset aktualizovat ani znovu nasadit, ale automaticky rozpozná bližší oblast a automaticky se na ni připojí, pokud se k ní dojde v místní události.

> [!Note]
> Účty Cosmos s počáteční konfigurací s jednou oblastí zápisu můžou být nakonfigurované na více oblastí zápisu s nulovým časem. Další informace najdete v tématu [Konfigurace oblastí s vícenásobným zápisem](how-to-manage-database-account.md#configure-multiple-write-regions) .

## <a name="azure-portal"></a><a id="portal"></a> Azure Portal

Pokud chcete povolit zápisy ve více oblastech z Azure Portal, použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Přejděte k účtu Azure Cosmos a v nabídce otevřete podokno **replikace dat globálně** .

1. V možnosti **zápisy ve více oblastech** vyberte **Povolit**. Automaticky přidá existující oblasti do oblastí pro čtení a zápis.

1. Můžete přidat další oblasti tak, že vyberete ikony na mapě nebo vyberete tlačítko **Přidat oblast** . Všechny oblasti, které přidáte, budou mít povolený čtení i zápis.

1. Po aktualizaci seznamu oblast vyberte **Uložit** , aby se změny projevily.

   :::image type="content" source="./media/how-to-multi-master/enable-multi-region-writes.png" alt-text="Snímek obrazovky umožňující zápis ve více oblastech pomocí Azure Portal" lightbox="./media/how-to-multi-master/enable-multi-region-writes.png":::

## <a name="net-sdk-v2"></a><a id="netv2"></a>Sada .NET SDK v2

Pokud chcete v aplikaci povolit zápisy ve více oblastech, nastavte `UseMultipleWriteLocations` na `true` . Nastavte také `SetCurrentLocation` na oblast, ve které se aplikace nasazuje a kam se Azure Cosmos DB replikují:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>Sada .NET SDK v3

Pokud chcete v aplikaci povolit zápisy ve více oblastech, nastavte `ApplicationRegion` na oblast, ve které se aplikace nasazuje a kam se Cosmos DB replikují:

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

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> Sada Java v4 SDK

Chcete-li v aplikaci povolit zápisy ve více oblastech, zavolejte `.multipleWriteRegionsEnabled(true)` a `.preferredRegions(preferredRegions)` v Tvůrci klienta, kde `preferredRegions` je `List` obsahující jeden element – to je oblast, ve které se aplikace nasazuje a kde se Cosmos DB replikují:

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Synchronizovat](#tab/api-sync)

   [Sada Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a> Async Java v2 SDK

Sada Java v2 SDK používala Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Pokud chcete v aplikaci povolit zápisy ve více oblastech, nastavte `policy.setUsingMultipleWriteLocations(true)` a nastavte `policy.setPreferredLocations` na oblast, ve které se aplikace nasazuje a kam se Cosmos DB replikují:

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

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a> Sady SDK proNode.js, JavaScript a TypeScript

Pokud chcete v aplikaci povolit zápisy ve více oblastech, nastavte `connectionPolicy.UseMultipleWriteLocations` na `true` . Nastavte také `connectionPolicy.PreferredLocations` na oblast, ve které se aplikace nasazuje a kam se Cosmos DB replikují:

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

Pokud chcete v aplikaci povolit zápisy ve více oblastech, nastavte `connection_policy.UseMultipleWriteLocations` na `true` . Nastavte také `connection_policy.PreferredLocations` na oblast, ve které se aplikace nasazuje a kam se Cosmos DB replikují.

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
* [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md)
* [Vyberte správnou úroveň konzistence v Azure Cosmos DB](./consistency-levels.md)
* [Kompromisy konzistence, dostupnosti a výkonu v Azure Cosmos DB](./consistency-levels.md)
* [Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence](./consistency-levels.md)
* [Zřízená propustnost globálního škálování](./request-units.md)
* [Globální distribuce: pod digestoří](global-dist-under-the-hood.md)