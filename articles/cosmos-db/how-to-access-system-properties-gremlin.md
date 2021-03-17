---
title: Přístup k vlastnostem systémových dokumentů prostřednictvím Azure Cosmos DB graphu
description: Přečtěte si, jak číst a zapisovat Cosmos DB vlastnosti systémového dokumentu přes rozhraní Gremlin API.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/10/2019
author: SnehaGunda
ms.author: sngun
ms.openlocfilehash: 61814082ebe9828a08da1e8786890b500c239082
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081837"
---
# <a name="system-document-properties"></a>Vlastnosti systémového dokumentu
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB má [systémové vlastnosti](/rest/api/cosmos-db/databases) , jako jsou ```_ts``` ,, ```_self``` ```_attachments``` , ```_rid``` a ```_etag``` v každém dokumentu. Modul Gremlin kromě toho přidává pro hrany vlastnosti ```inVPartition``` a ```outVPartition```. Ve výchozím nastavení jsou tyto vlastnosti k dispozici pro procházení. Je však možné do průchodu Gremlin zahrnout konkrétní vlastnosti nebo všechny.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>Značka entity

Tato vlastnost slouží k řízení optimistické souběžnosti. Pokud aplikace potřebuje přerušit operaci do několika samostatných procházení, může použít vlastnost eTag, aby se předešlo ztrátě dat v souběžných zápisech.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Hodnota TTL (Time-To-Live)

Pokud má kolekce zapnuté vypršení platnosti dokumentu a ```ttl``` pro ně je nastavená vlastnost Documents, bude tato vlastnost k dispozici v Gremlin procházení jako běžná vlastnost vrcholu nebo Edge. ```ProjectionStrategy``` není nutné povolit expozici vlastností Time to Live.

Vrchol vytvořený s následujícím procházením se automaticky odstraní za **123 sekund** .

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Další kroky
* [Optimistická souběžnost ve službě Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Hodnota TTL (Time to Live)](time-to-live.md) v Azure Cosmos DB
