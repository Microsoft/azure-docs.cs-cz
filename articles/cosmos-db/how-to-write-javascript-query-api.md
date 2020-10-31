---
title: Zapište uložené procedury a triggery pomocí rozhraní API pro dotazy jazyka JavaScript v Azure Cosmos DB
description: Naučte se zapisovat uložené procedury a triggery pomocí rozhraní API pro dotazy jazyka JavaScript v Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/07/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 2ad2b9bcbfdd4353c16d890d1857727b685a5e59
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098854"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Postup zápisu uložených procedur a triggerů v Azure Cosmos DB pomocí rozhraní API pro dotazy jazyka JavaScript
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB umožňuje provádět optimalizované dotazy pomocí rozhraní Fluent JavaScript bez znalosti jazyka SQL, které lze použít k zápisu uložených procedur nebo triggerů. Další informace o podpoře rozhraní API dotazů JavaScript v Azure Cosmos DB najdete v článku [práce s integrovaným rozhraním API pro integrované dotazy jazyka JavaScript v článku Azure Cosmos DB](javascript-query-api.md) .

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>Uložená procedura s použitím rozhraní API pro dotazy jazyka JavaScript

Následující ukázka kódu je příkladem použití rozhraní API pro dotazování jazyka JavaScript v kontextu uložené procedury. Uložená procedura vloží položku Azure Cosmos, která je určená vstupním parametrem, a aktualizuje dokument metadat pomocí `__.filter()` metody, parametru minSize, MaxSize a TotalSize v závislosti na vlastnosti Size položky Input.

> [!NOTE]
> `__` (dvojité podtržítko) je alias pro `getContext().getCollection()` při použití rozhraní API pro dotazování jazyka JavaScript.

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>Další kroky

V následujících článcích najdete informace o uložených procedurách, triggerech a uživatelem definovaných funkcích v Azure Cosmos DB:

* [Jak pracovat s uloženými procedurami, triggery, uživatelsky definovanými funkcemi v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Postup při registraci a používání uložených procedur v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Postup při registraci a používání [předaktivačních událostí](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) a [následných triggerů](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) v Azure Cosmos DB

* [Postup při registraci a používání uživatelem definovaných funkcí v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Syntetické klíče oddílů ve službě Azure Cosmos DB](synthetic-partition-keys.md)
