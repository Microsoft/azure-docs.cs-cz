---
title: Jak napsat uložené procedury a triggery, pomocí dotazu jazyka JavaScript API ve službě Azure Cosmos DB
description: Další informace o zápisu uložených procedur a aktivačních událostí ve službě Azure Cosmos DB pomocí rozhraní API pro dotazy jazyka JavaScript
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/11/2018
ms.author: mjbrown
ms.openlocfilehash: 94f72b716e149b2fa5c31deabf92a8a443eb0bef
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043341"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Jak napsat uložených procedur a aktivačních událostí ve službě Azure Cosmos DB pomocí rozhraní API jazyka JavaScript dotazu

Azure Cosmos DB umožňuje provádět optimalizované dotazy pomocí rozhraní fluent JavaScript bez znalosti jazyka SQL, který můžete použít pro zapsání uložených procedur a aktivačních událostí. Další informace o podpoře rozhraní API pro JavaScript dotazy ve službě Azure Cosmos DB najdete v tématu [práce s jazykem JavaScript integrovaná rozhraní API pro dotazy ve službě Azure Cosmos DB](javascript-query-api.md) článku.

## <a id="stored-procedures"></a>Uložené procedury pomocí dotazu jazyka JavaScript API

Následující vzorový kód je příklad použití dotazu jazyka JavaScript API v rámci uložené procedury. Vloží položku služby Azure Cosmos DB, která je určená jako vstupní parametr a aktualizuje dokumentů metadat pomocí uložené procedury `__.filter()` metodou minSize maxSize a totalSize stupněm MODERATE vlastnost velikost vstupní položky.

> [!NOTE]
> `__` (dvojité podtržítko) je alias `getContext().getCollection()` při použití rozhraní API jazyka JavaScript dotazu.

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

## <a name="next-steps"></a>Další postup

Naleznete v následujících článcích se dozvíte o uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB:

* [Jak pracovat s uložené procedury, aktivační události, uživatelem definovaných funkcí ve službě Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Registrace a používání uložených procedur ve službě Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Registrace a používání [předběžné triggery](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) a [po triggery](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) ve službě Azure Cosmos DB

* [Postup registrace a použití uživatelem definovaných funkcí ve službě Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Syntetické klíče oddílů ve službě Azure Cosmos DB](synthetic-partition-keys.md)
