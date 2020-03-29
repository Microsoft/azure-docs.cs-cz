---
title: Zápis uložených procedur a aktivačních událostí pomocí rozhraní API dotazu JavaScriptu v Azure Cosmos DB
description: Zjistěte, jak psát uložené procedury a aktivační události pomocí rozhraní Api pro dotaz y JavaScriptu v Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 221a3118808a044ef1b1b822b9c95772bf792f34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441712"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Jak psát uložené procedury a aktivační události v Azure Cosmos DB pomocí rozhraní API dotazu JavaScript

Azure Cosmos DB umožňuje provádět optimalizované dotazy pomocí plynulého rozhraní JavaScript bez znalosti jazyka SQL, který lze použít k zápisu uložené procedury nebo aktivační události. Další informace o podpoře rozhraní API JavaScriptquery API v Azure Cosmos DB najdete v článku [Práce s rozhraním API integrovaného javascriptového jazyka v článku dotazovacího rozhraní Azure Cosmos DB.](javascript-query-api.md)

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>Uložená procedura pomocí rozhraní API dotazu JavaScript

Následující ukázka kódu je příkladem použití rozhraní API dotazu JavaScript v kontextu uložené procedury. Uložená procedura vloží položku Azure Cosmos, která je určena vstupním parametrem, a aktualizuje dokument metadat pomocí `__.filter()` metody s parametrem minSize, maxSize a totalSize na základě vlastnosti velikosti vstupní položky.

> [!NOTE]
> `__`(double-underscore) je alias `getContext().getCollection()` při použití javascriptového api dotazu.

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

V následujících článcích se dozvíte o uložených procedurách, aktivačních událostech a uživatelem definovaných funkcích v Azure Cosmos DB:

* [Jak pracovat s uloženými procedurami, aktivačními událostmi a uživatelem definovanými funkcemi v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Jak zaregistrovat a používat uložené procedury v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Jak zaregistrovat a používat [předběžné aktivační události](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) a následné aktivační [události](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) v Azure Cosmos DB

* [Jak zaregistrovat a používat uživatelem definované funkce v Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Syntetické klíče oddílů ve službě Azure Cosmos DB](synthetic-partition-keys.md)
