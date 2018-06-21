---
title: Indexování v rozhraní MongoDB API služby Azure Cosmos DB | Microsoft Docs
description: Tento článek představuje přehled možností indexování v rozhraní MongoDB API služby Azure Cosmos DB.
services: cosmos-db
author: orestis-ms
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: orkostak
ms.openlocfilehash: 3979c2a10707936c54c0d55ebcc85c470cabc3aa
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294526"
---
# <a name="indexing-in-the-azure-cosmos-db-mongodb-api"></a>Indexování ve službě Azure Cosmos DB: MongoDB API

Rozhraní MongoDB API služby Azure Cosmos DB využívá možnosti automatické správy indexů ve službě Azure Cosmos DB. Díky tomu mají uživatelé přístup k výchozím zásadám indexování ve službě Azure Cosmos DB. Proto pokud uživatel nedefinoval žádné indexy nebo se žádné indexy nezrušily, ve výchozím nastavení se budou při vložení do kolekce automaticky indexovat všechna pole. Pro většinu scénářů doporučujeme použít výchozí zásady indexování nastavené v účtu.

## <a name="dropping-the-default-indexes"></a>Rušení výchozích indexů

Pomocí následujícího příkazu je možné zrušit výchozí indexy pro kolekci ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>Vytváření složených indexů

Složené indexy obsahují odkazy na více polí dokumentu. Logicky jsou ekvivalentní k vytváření více jednotlivých indexů pro jednotlivá pole. Pokud chcete využít optimalizace, které nabízejí techniky indexování ve službě Cosmos DB, doporučujeme místo jediného (nejedinečného) složeného indexu vytvářet více jednotlivých indexů.

## <a name="creating-unique-indexes"></a>Vytváření jedinečných indexů

[Jedinečné indexy](unique-keys.md) jsou užitečné k vynucování, aby dva nebo více dokumentů neobsahovalo v indexovaných polích stejnou hodnotu. 
>[!important] 
> V současné době je možné jedinečné indexy vytvořit pouze v případě, že je kolekce prázdná (neobsahuje žádné dokumenty). 

Následující příkaz vytvoří jedinečný index u pole student_id:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} ) 
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

V případě horizontálně dělených kolekcí se k vytvoření jedinečného indexu vyžaduje kvůli chování MongoDB zadání klíče horizontálního dělení (oddílu). Jinými slovy, všechny jedinečné indexy u horizontálně dělené kolekce jsou složené indexy, ve kterých je jedno z polí klíčem oddílu.

Následující příkazy vytvoří horizontálně dělenou kolekci ```coll``` (klíč horizontálního dělení je ```university```) s jedinečnými indexy u polí student_id a university:

```JavaScript
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

Ve výše uvedeném příkladu se v případě vynechání klauzule ```"university":1``` vrátí chyba s následující zprávou:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>Indexy TTL

Pokud chcete povolit vypršení platnosti dokumentu v konkrétní kolekci, je potřeba vytvořit [index TTL (Time to Live)](../cosmos-db/time-to-live.md). Index TTL je index u pole _ts s hodnotou expireAfterSeconds.
 
Příklad:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Předchozí příkaz způsobí odstranění všech dokumentů v kolekci ```db.coll```, které se během posledních 10 sekund neupravily. 
 
> [!NOTE]
> Pole **_ts** je pole specifické pro službu Cosmos DB a není přístupné z klientů MongoDB. Jedná se o vyhrazenou (systémovou) vlastnost obsahující časové razítko poslední úpravy dokumentu.
>

## <a name="migrating-collections-with-indexes"></a>Migrace kolekcí s indexy

V současné době je vytváření jedinečných indexů možné pouze v případě, že kolekce neobsahuje žádné dokumenty. Oblíbené nástroje pro migraci MongoDB se pokouší vytvořit jedinečné indexy po importu dat. Pokud chcete tento problém obejít, doporučujeme vytvořit odpovídající kolekce a jedinečné indexy ručně, a nepovolovat nástroj pro migraci (v případě ```mongorestore``` se tohoto chování dosahuje použitím příznaku --noIndexRestore na příkazovém řádku).

## <a name="next-steps"></a>Další postup
* [Jak služba Azure Cosmos DB indexuje data?](../cosmos-db/indexing-policies.md)
* [Automatické vypršení platnosti dat v kolekcích Azure Cosmos DB s využitím hodnoty TTL (Time to Live)](../cosmos-db/time-to-live.md)
* [Vypršení platnosti dat v rozhraní API služby Azure Cosmos DB MongoDB](../cosmos-db/mongodb-ttl.md)