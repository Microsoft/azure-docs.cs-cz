---
title: Změna datových proudů v rozhraní API Služby Azure Cosmos DB pro MongoDB
description: Zjistěte, jak používat datové proudy změn v rozhraní API Azure Cosmos DB pro MongoDB k získání změn provedených v datech.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 38e262abefe5444c1fe7586810f4b971cc7baf6c
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114154"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Změna datových proudů v rozhraní API Služby Azure Cosmos DB pro MongoDB

[Podpora kanálu změn](change-feed.md) v rozhraní API Azure Cosmos DB pro MongoDB je dostupná pomocí rozhraní API datových proudů změn. Pomocí rozhraní API datových proudů změn, vaše aplikace můžete získat změny provedené v kolekci nebo položky v jednom oddílu. Později můžete provést další akce na základě výsledků. Změny položek v kolekci jsou zachyceny v pořadí jejich čas úpravy a pořadí řazení je zaručena na klíč střepu.

> [!NOTE]
> Chcete-li použít datové proudy změn, vytvořte účet s verzí 3.6 rozhraní API Azure Cosmos DB pro MongoDB nebo novější verzi. Pokud spustíte příklady datového proudu změn proti `Unrecognized pipeline stage name: $changeStream` starší verzi, může se zobrazit chyba.

## <a name="current-limitations"></a>Aktuální omezení

Následující omezení platí při použití datových proudů změn:

* `operationType` Vlastnosti `updateDescription` a ještě nejsou podporovány ve výstupním dokumentu.
* Typy `insert` `update`, `replace` a operace jsou aktuálně podporovány. 
* Operace odstranění nebo jiné události ještě nejsou podporovány.

Z důvodu těchto omezení jsou vyžadovány $match fáze, $project fáze a fullDocument možnosti, jak je znázorněno v předchozích příkladech.

Na rozdíl od kanálu změn v rozhraní SQL API služby Azure Cosmos DB neexistuje samostatná [knihovna procesoru kanálu změn,](change-feed-processor.md) která by spotřebovávala datové proudy změn nebo potřeba zapůjčení kontejneru. V současné době není podpora [pro Azure Functions aktivační události](change-feed-functions.md) pro zpracování datových proudů změn.

## <a name="error-handling"></a>Zpracování chyb

Při použití datových proudů změn jsou podporovány následující kódy chyb a zprávy:

* **Kód chyby HTTP 16500** - Při omezení datového proudu změn vrátí prázdnou stránku.

* **NamespaceNotFound (OperationType Invalidate)** - Pokud spustíte datový proud změny v kolekci, `NamespaceNotFound` která neexistuje, nebo pokud je kolekce vynechána, je vrácena chyba. Vzhledem `operationType` k tomu, že vlastnost nemůže být `operationType Invalidate` vrácena `NamespaceNotFound` ve výstupním dokumentu, místo chyby je vrácena chyba.

## <a name="examples"></a>Příklady

Následující příklad ukazuje, jak získat datové proudy změn na všechny položky v kolekci. Tento příklad vytvoří kurzor pro sledování položek při jejich vložení, aktualizaci nebo nahrazení. Fáze, `$match` `$project` fáze a `fullDocument` možnost jsou nutné k získání datových proudů změn. Sledování operací odstranění pomocí datových proudů změn není aktuálně podporováno. Jako řešení můžete přidat měkkou značku na položky, které jsou odstraněny. Můžete například přidat atribut v položce s názvem "odstraněno". Pokud chcete položku odstranit, můžete nastavit "deleted" `true` a nastavit ttl na položku. Vzhledem k tomu, že `true` aktualizace "odstraněno" je aktualizace, bude tato změna viditelná v datovém proudu změn.

### <a name="javascript"></a>Javascript:

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

### <a name="c"></a>C#:

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>Změny v rámci jednoho úlomku

Následující příklad ukazuje, jak získat změny položek v rámci jednoho šmejdu. Tento příklad získá změny položek, které mají klíč střepu rovná "a" a hodnota klíče střepu rovná "1". Je možné mít různé klienty čtení změn z různých štírových dříčů paralelně.

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

## <a name="next-steps"></a>Další kroky

* [Využijte čas k automatickému vypršení platnosti dat v rozhraní API Azure Cosmos DB pro MongoDB](mongodb-time-to-live.md)
* [Indexování v rozhraní API Služby Azure Cosmos DB pro MongoDB](mongodb-indexing.md)
