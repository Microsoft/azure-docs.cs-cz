---
title: Změna datových proudů v rozhraní API Služby Azure Cosmos DB pro MongoDB
description: Zjistěte, jak používat datové proudy změn n rozhraní API Azure Cosmos DB pro MongoDB k získání změn provedených v datech.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467773"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Změna datových proudů v rozhraní API Služby Azure Cosmos DB pro MongoDB

[Podpora kanálu změn](change-feed.md) v rozhraní API Azure Cosmos DB pro MongoDB je dostupná pomocí rozhraní API datových proudů změn. Pomocí rozhraní API datových proudů změn, vaše aplikace můžete získat změny provedené v kolekci nebo položky v jednom oddílu. Později můžete provést další akce na základě výsledků. Změny položek v kolekci jsou zachyceny v pořadí jejich čas úpravy a pořadí řazení je zaručena na klíč střepu.

> [!NOTE]
> Chcete-li použít datové proudy změn, vytvořte účet s verzí 3.6 rozhraní API Azure Cosmos DB pro MongoDB nebo novější verzi. Pokud spustíte příklady datového proudu změn proti `Unrecognized pipeline stage name: $changeStream` starší verzi, může se zobrazit chyba. 

Následující příklad ukazuje, jak získat datové proudy změn na všechny položky v kolekci. Tento příklad vytvoří kurzor pro sledování položek při jejich vložení, aktualizaci nebo nahrazení. Fáze $match, fáze $project a fullDocument možnost jsou nutné získat datové proudy změn. Sledování operací odstranění pomocí datových proudů změn není aktuálně podporováno. Jako řešení můžete přidat měkkou značku na položky, které jsou odstraněny. Můžete například přidat atribut v položce s názvem "odstraněno" a nastavit jej na hodnotu "true" a nastavit ttl na položku, takže ji můžete automaticky odstranit a sledovat.

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

Následující příklad ukazuje, jak získat změny položek v jednom šiřidlo. Tento příklad získá změny položek, které mají klíč střepu rovná "a" a hodnota klíče střepu rovná "1".

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Aktuální omezení

Následující omezení platí při použití datových proudů změn:

* `operationType` Vlastnosti `updateDescription` a ještě nejsou podporovány ve výstupním dokumentu.
* Typy `insert` `update`, `replace` a operace jsou aktuálně podporovány. Operace odstranění nebo jiné události ještě nejsou podporovány.

Z důvodu těchto omezení jsou vyžadovány $match fáze, $project fáze a fullDocument možnosti, jak je znázorněno v předchozích příkladech.

## <a name="error-handling"></a>Zpracování chyb

Při použití datových proudů změn jsou podporovány následující kódy chyb a zprávy:

* **Kód chyby HTTP 429** - Při omezení datového proudu změn vrátí prázdnou stránku.

* **NamespaceNotFound (OperationType Invalidate)** - Pokud spustíte datový proud změny v kolekci, `NamespaceNotFound` která neexistuje, nebo pokud je kolekce vynechána, je vrácena chyba. Vzhledem `operationType` k tomu, že vlastnost nemůže být `operationType Invalidate` vrácena `NamespaceNotFound` ve výstupním dokumentu, místo chyby je vrácena chyba.

## <a name="next-steps"></a>Další kroky

* [Využijte čas k automatickému vypršení platnosti dat v rozhraní API Azure Cosmos DB pro MongoDB](mongodb-time-to-live.md)
* [Indexování v rozhraní API Služby Azure Cosmos DB pro MongoDB](mongodb-indexing.md)
