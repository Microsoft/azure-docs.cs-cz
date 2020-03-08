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
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898309"
---
# <a name="system-document-properties"></a>Vlastnosti systémového dokumentu

Azure Cosmos DB v každém dokumentu obsahuje [systémové vlastnosti](https://docs.microsoft.com/rest/api/cosmos-db/databases) , jako jsou ```_ts```, ```_self```, ```_attachments```, ```_rid```a ```_etag```. Modul Gremlin kromě toho přidává pro hrany vlastnosti ```inVPartition``` a ```outVPartition```. Ve výchozím nastavení jsou tyto vlastnosti k dispozici pro procházení. Je však možné do průchodu Gremlin zahrnout konkrétní vlastnosti nebo všechny.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>Elektronická značka

Tato vlastnost slouží k řízení optimistické souběžnosti. Pokud aplikace potřebuje přerušit operaci do několika samostatných procházení, může použít vlastnost eTag, aby se předešlo ztrátě dat v souběžných zápisech.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Hodnota TTL (Time-To-Live)

Pokud má kolekce zapnuté vypršení platnosti dokumentu a pro ně jsou ```ttl``` nastavené vlastnosti, bude tato vlastnost k dispozici v Gremlin procházení jako běžná vlastnost vrcholu nebo Edge. ```ProjectionStrategy``` není nutné povolit vystavení vlastností Time to Live.

Vrchol vytvořený s následujícím procházením se automaticky odstraní za **123 sekund**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Další kroky
* [Optimistická souběžnost ve službě Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Hodnota TTL (Time to Live)](time-to-live.md) v Azure Cosmos DB
