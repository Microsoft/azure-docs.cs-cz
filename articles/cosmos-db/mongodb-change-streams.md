---
title: Změna datových proudů v rozhraní Azure Cosmos DB API pro MongoDB
description: Naučte se používat rozhraní Change Streams n Azure Cosmos DB API pro MongoDB k získání změn provedených ve vašich datech.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: fbfce1c107fcf4b6f7d0b5f590a8ddfa64e69190
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184741"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Změna datových proudů v rozhraní Azure Cosmos DB API pro MongoDB

Podpora [kanálů změn](change-feed.md) v rozhraní Azure Cosmos DB API pro MongoDB je k dispozici prostřednictvím rozhraní API Change Streams. Pomocí rozhraní Change Streams API můžou vaše aplikace získat změny provedené v kolekci nebo na položky v jednom horizontálních oddílů. Později můžete na základě výsledků provádět další akce. Změny položek v kolekci jsou zachyceny v pořadí podle doby jejich úpravy a je zaručeno pořadí řazení podle horizontálních oddílů klíče.

Následující příklad ukazuje, jak získat datové proudy změn pro všechny položky v kolekci. Tento příklad vytvoří kurzor pro sledování položek při jejich vložení, aktualizaci nebo nahrazení. Pro získání datových proudů změn se vyžadují $match fáze, $project fáze a možnost fullDocument. Sledování operací odstranění pomocí datových proudů není aktuálně podporováno. Jako alternativní řešení můžete přidat měkké označení pro položky, které se odstraňují. Můžete například přidat atribut do položky s názvem "Deleted" a nastavit ji na hodnotu "true" a nastavit hodnotu TTL pro položku, abyste ji mohli automaticky odstranit a také ji sledovat.

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

Následující příklad ukazuje, jak získat změny položek v jednom horizontálních oddílů. Tento příklad načte změny položek, které mají klíč horizontálních oddílů se rovná "a" a hodnotu klíče horizontálních oddílů rovnající se 1.

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

Při použití datových proudů změn platí následující omezení:

* Ve výstupním dokumentu se zatím nepodporují vlastnosti `operationType` a `updateDescription`.
* Typy operací `insert`, `update`a `replace` se momentálně podporují. Operace odstranění nebo jiné události ještě nejsou podporované.

V důsledku těchto omezení jsou vyžadovány $match fáze, $project fáze a možnosti fullDocument, jak je znázorněno v předchozích příkladech.

## <a name="error-handling"></a>Zpracování chyb

Při použití datových proudů změn jsou podporovány následující chybové kódy a zprávy:

* **Kód chyby HTTP 429** – Pokud je datový proud změny omezený, vrátí prázdnou stránku.

* **NamespaceNotFound (typem operace OperationType unvalidate)** – Pokud spustíte datový proud změn v kolekci, která neexistuje, nebo pokud je kolekce vyřazena, vrátí se `NamespaceNotFound` chyba. Vzhledem k tomu, že vlastnost `operationType` nelze vrátit do výstupního dokumentu namísto `operationType Invalidate` chyby, je vrácena chyba `NamespaceNotFound`.

## <a name="next-steps"></a>Další kroky

* [Využijte čas k automatickému vypršení platnosti dat v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-time-to-live.md)
* [Indexování v rozhraní API Azure Cosmos DB pro MongoDB](mongodb-indexing.md)