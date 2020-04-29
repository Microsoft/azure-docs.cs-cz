---
title: Přístup k vlastnostem systémových dokumentů prostřednictvím Azure Cosmos DB graphu
description: Přečtěte si, jak číst a zapisovat Cosmos DB vlastnosti systémového dokumentu přes rozhraní Gremlin API.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 4ed7e67ae0ef027b260d0e0f0407e4e05ed5a8f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898309"
---
# <a name="system-document-properties"></a>Vlastnosti systémového dokumentu

Azure Cosmos DB má [systémové vlastnosti](https://docs.microsoft.com/rest/api/cosmos-db/databases) ```_ts```, jako jsou ```_self```, ```_attachments```, ```_rid```, a ```_etag``` v každém dokumentu. Modul Gremlin kromě toho přidává pro hrany vlastnosti ```inVPartition``` a ```outVPartition```. Ve výchozím nastavení jsou tyto vlastnosti k dispozici pro procházení. Je však možné do průchodu Gremlin zahrnout konkrétní vlastnosti nebo všechny.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>Značka entity

Tato vlastnost slouží k řízení optimistické souběžnosti. Pokud aplikace potřebuje přerušit operaci do několika samostatných procházení, může použít vlastnost eTag, aby se předešlo ztrátě dat v souběžných zápisech.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Hodnota TTL (Time-To-Live)

Pokud má kolekce zapnuté vypršení platnosti dokumentu ```ttl``` a pro ně je nastavená vlastnost Documents, bude tato vlastnost k dispozici v Gremlin procházení jako běžná vlastnost vrcholu nebo Edge. ```ProjectionStrategy```není nutné povolit expozici vlastností Time to Live.

Vrchol vytvořený s následujícím procházením se automaticky odstraní za **123 sekund**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Další kroky
* [Optimistická souběžnost ve službě Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Hodnota TTL (Time to Live)](time-to-live.md) v Azure Cosmos DB
