---
title: Indexování v rozhraní API Azure Cosmos DB pro MongoDB
description: Nabízí přehled možností indexování s rozhraním API Azure Cosmos DB pro MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: c8879884cf3d882e6a6b441244ed139072bedeeb
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029465"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexování pomocí rozhraní API Azure Cosmos DB pro MongoDB

Rozhraní API Azure Cosmos DB pro MongoDB využívá automatické možnosti správy indexů Cosmos DB. Výsledkem je, že uživatelé mají přístup k výchozím zásadám indexování Cosmos DB. Takže pokud uživatel nedefinoval žádné indexy nebo žádné indexy nebyly vyřazeny, pak budou všechna pole při vložení do kolekce automaticky indexována. Pro většinu scénářů doporučujeme použít výchozí zásady indexování nastavené v účtu.

## <a name="indexing-for-version-36"></a>Indexování pro verzi 3,6

Účty obsluhující síťový protokol verze 3,6 poskytují jiné výchozí zásady indexování než zásady poskytované staršími verzemi. Ve výchozím nastavení je indexováno pouze pole _id. Aby bylo možné indexovat další pole, musí uživatel použít příkazy pro správu indexu MongoDB. Chcete-li použít řazení pro dotaz, je v současné době nutné vytvořit index pro pole použitá v operaci řazení.

### <a name="dropping-the-default-indexes-36"></a>Vyřazení výchozích indexů (3,6)

U účtů obsluhujících síťový protokol verze 3,6 se _id jediný výchozí index, který nelze vyřadit.

### <a name="creating-a-compound-index-36"></a>Vytvoření složeného indexu (3,6)

U účtů využívajících přenosový protokol 3,6 jsou podporovány i složené složené indexy. Následující příkaz vytvoří složený index pro pole a a b: `db.coll.createIndex({a:1,b:1})`

Složené indexy lze použít k efektivnímu řazení více polí najednou, například: `db.coll.find().sort({a:1,b:1})`

### <a name="track-the-index-progress"></a>Sledovat průběh indexu

Verze 3,6 Azure Cosmos DB API pro účty MongoDB podporuje `currentOp()` příkaz ke sledování průběhu indexu instance databáze. Tento příkaz vrátí dokument, který obsahuje informace o probíhajících operacích instance databáze. Příkaz `currentOp` se používá ke sledování všech probíhajících operací v nativním MongoDB, zatímco v rozhraní Azure Cosmos DB API pro MongoDB tento příkaz podporuje pouze sledování operace indexu.

Zde je několik příkladů, které ukazují, jak použít příkaz `currentOp` ke sledování průběhu indexu:

• Získejte průběh indexu pro kolekci:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

• Získejte průběh indexu pro všechny kolekce v databázi:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

• Získejte průběh indexu pro všechny databáze a kolekce v účtu Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Podrobnosti o průběhu indexu obsahují procento průběhu aktuální operace indexu. Následující příklad ukazuje formát výstupního dokumentu pro různé fáze průběhu indexu:

1. Pokud operace indexu v kolekci foo a na panelu databáze s úplným dokončením indexování 60%, bude mít následující výstupní dokument. `Inprog[0].progress.total` zobrazí 100 jako dokončení cíle.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

2. Pro operaci indexu, která se právě spustila v kolekci ' foo ' a ' bar ', může výstupní dokument zobrazovat 0% průběhu až do dosažení měřitelné úrovně.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

3. Po dokončení probíhající operace indexu se ve výstupním dokumentu zobrazí prázdné operace inoperace.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="indexing-for-version-32"></a>Indexování pro verzi 3,2

### <a name="dropping-the-default-indexes-32"></a>Vyřazení výchozích indexů (3,2)

Pomocí následujícího příkazu je možné zrušit výchozí indexy pro kolekci ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>Vytvoření složeného indexu (3,2)

Složené indexy obsahují odkazy na více polí dokumentu. Logicky jsou ekvivalentní k vytváření více jednotlivých indexů pro jednotlivá pole. Pokud chcete využít optimalizace, které nabízejí techniky indexování ve službě Cosmos DB, doporučujeme místo jediného (nejedinečného) složeného indexu vytvářet více jednotlivých indexů.

## <a name="common-indexing-operations"></a>Běžné operace indexování

Následující operace jsou společné pro oba účty obsluhující protokol drátové verze 3,6 a účty obsluhující starší verze přenosových protokolů. 

## <a name="creating-unique-indexes"></a>Vytváření jedinečných indexů

[Jedinečné indexy](unique-keys.md) jsou užitečné k vynucování, aby dva nebo více dokumentů neobsahovalo v indexovaných polích stejnou hodnotu.

>[!Important]
> V současné době je možné jedinečné indexy vytvořit pouze v případě, že je kolekce prázdná (neobsahuje žádné dokumenty).

Následující příkaz vytvoří jedinečný index v poli student_id:

```shell
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

```shell
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

## <a name="migrating-collections-with-indexes"></a>Migrace kolekcí s indexy

V současné době je vytváření jedinečných indexů možné pouze v případě, že kolekce neobsahuje žádné dokumenty. Oblíbené nástroje pro migraci MongoDB se pokouší vytvořit jedinečné indexy po importu dat. Chcete-li tento problém obejít, doporučujeme, aby uživatelé ručně vytvořili odpovídající kolekce a jedinečné indexy místo toho, aby nástroj pro migraci (pro ```mongorestore``` tohoto chování byl dosažen pomocí příznaku `--noIndexRestore` v příkazovém řádku).

## <a name="next-steps"></a>Další kroky

* [Indexování v Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Automatické vypršení platnosti dat v Azure Cosmos DB s časem až Live](../cosmos-db/time-to-live.md)
