---
title: Přístup k vlastnostem systémových dokumentů prostřednictvím grafu Azure Cosmos DB Graph
description: Zjistěte, jak číst a psát vlastnosti systémových dokumentů Cosmos DB prostřednictvím rozhraní Gremlin API
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 4ed7e67ae0ef027b260d0e0f0407e4e05ed5a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898309"
---
# <a name="system-document-properties"></a>Vlastnosti systémového dokumentu

Azure Cosmos DB má ```_ts``` [vlastnosti systému,](https://docs.microsoft.com/rest/api/cosmos-db/databases) jako je například , ```_self``` ```_attachments```, ```_rid```, a ```_etag``` na každém dokumentu. Modul Gremlin kromě toho přidává pro hrany vlastnosti ```inVPartition``` a ```outVPartition```. Ve výchozím nastavení jsou tyto vlastnosti k dispozici pro průchod. Je však možné zahrnout specifické vlastnosti nebo všechny z nich v Gremlin traversal.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>Značka entity

Tato vlastnost slouží k řízení optimistické souběžnosti. Pokud aplikace potřebuje přerušit provoz do několika samostatných traversals, můžete použít eTag vlastnost, aby se zabránilo ztrátě dat v souběžných zápisů.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Hodnota TTL (Time-To-Live)

Pokud má kolekce povoleno vypršení ```ttl``` platnosti dokumentu a dokumenty mají nastavenou vlastnost, bude tato vlastnost k dispozici v Gremlin traversal jako normální vertex nebo vlastnost hrany. ```ProjectionStrategy```není nutné povolit čas-k-live expozice vlastností.

Vrchol vytvořený s následujícím procházením se automaticky odstraní za **123 sekund**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Další kroky
* [Optimistická souběžnost ve službě Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Čas na život (TTL)](time-to-live.md) v Azure Cosmos DB
