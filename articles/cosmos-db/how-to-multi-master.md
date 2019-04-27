---
title: Jak nakonfigurovat více hlavních databází ve službě Azure Cosmos DB
description: Zjistěte, jak konfigurovat více hlavních databází ve vašich aplikacích ve službě Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: b862c59002369662d37b6d6a9de28370b0000497
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61054588"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Jak nakonfigurovat více hlavních databází ve svých aplikacích, které používají službu Azure Cosmos DB

Použití několika hlavními databázemi funkce ve vaší aplikaci, budete muset povolit zápisů ve více oblastech a nakonfigurovat vícenásobné navádění schopností ve službě Azure Cosmos DB. Vícenásobné navádění je nakonfigurovaný tak, že nastavíte oblast, ve kterém je aplikace nasazená.

## <a id="netv2"></a>Sady .NET SDK v2

Povolit více hlavních databází ve vaší sadě aplikací `UseMultipleWriteLocations` na hodnotu true a nakonfigurovat `SetCurrentLocation` do oblasti, ve kterém se nasazuje aplikace a služby Azure Cosmos DB se replikuje.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>Sady .NET SDK v3 (preview)

Chcete-li povolit nakonfigurovat více hlavních databází ve svých aplikacích `UseCurrentRegion` do oblasti, ve kterém se nasazuje aplikace a služby Cosmos DB se replikuje.

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

Povolit více hlavních databází ve vaší sadě aplikací `policy.setUsingMultipleWriteLocations(true)` a nakonfigurujte `policy.setPreferredLocations` do oblasti, ve kterém se nasazuje aplikace a služby Cosmos DB se replikuje.

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

## <a id="javascript"></a>Node.js, JavaScript, TypeScript SDK

Povolit více hlavních databází ve vaší sadě aplikací `connectionPolicy.UseMultipleWriteLocations` na hodnotu true a nakonfigurovat `connectionPolicy.PreferredLocations` do oblasti, ve kterém se nasazuje aplikace a služby Cosmos DB se replikuje.

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

## <a id="python"></a>Python SDK

Povolit více hlavních databází ve vaší sadě aplikací `connection_policy.UseMultipleWriteLocations` na hodnotu true a nakonfigurovat `connection_policy.PreferredLocations` do oblasti, ve kterém se nasazuje aplikace a služby Cosmos DB se replikuje.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Další postup

Dále si můžete přečíst následující články:

* [Využívat relace tokeny pro správu konzistence ve službě Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Zásady řešení ve službě Azure Cosmos DB a typy konfliktů](conflict-resolution-policies.md)
* [Vysoká dostupnost ve službě Azure Cosmos DB](high-availability.md)
* [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md)
* [Volba úrovně konzistence správné ve službě Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisy konzistencí, dostupností a výkonem ve službě Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Globální škálování zřízená propustnost](scaling-throughput.md)
* [Globální distribuce - pod pokličkou](global-dist-under-the-hood.md)
