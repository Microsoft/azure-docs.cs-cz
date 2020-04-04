---
title: Indexování v rozhraní API Služby Azure Cosmos DB pro MongoDB
description: Představuje přehled možností indexování s rozhraním API Azure Cosmos DB pro MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: f3f369928270c77557337bfdb1037cc5174c39f2
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637963"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexování pomocí rozhraní API Azure Cosmos DB pro MongoDB

Rozhraní API Azure Cosmos DB pro MongoDB využívá základní možnosti správy indexů Azure Cosmos DB. Tento dokument se zaměřuje na to, jak přidat indexy pomocí rozhraní API Azure Cosmos DB pro MongoDB. Můžete si také přečíst [přehled indexování v Azure Cosmos DB,](index-overview.md) který je relevantní ve všech rozhraní API.

## <a name="indexing-for-version-36"></a>Indexování pro verzi 3.6

Pole `_id` je vždy automaticky indexováno a nelze vypustit. Rozhraní API Azure Cosmos DB pro MongoDB automaticky `_id` vynucuje jedinečnost pole na klíč střepu.

Chcete-li indexovat další pole, měli byste použít příkazy pro správu indexu MongoDB. Stejně jako v MongoDB je automaticky indexováno pouze `_id` pole. Tato výchozí zásada indexování se liší od rozhraní AZURE Cosmos DB SQL API, které ve výchozím nastavení indexuje všechna pole.

Chcete-li použít řazení na dotaz, musí být vytvořen index pro pole použitá v operaci řazení.

## <a name="index-types"></a>Typy indexů

### <a name="single-field"></a>Jedno pole

Indexy můžete vytvořit v libovolném poli. Pořadí řazení indexu jednoho pole nezáleží. Následující příkaz vytvoří index na `name`poli :

`db.coll.createIndex({name:1})`

Jeden dotaz bude využívat více indexů jednoho pole, pokud je k dispozici. Můžete vytvořit až 500 indexů jednoho pole na kontejner.

### <a name="compound-indexes-36"></a>Složené indexy (3.6)

Složené indexy jsou podporovány pro účty pomocí drátového protokolu 3.6. Do složeného indexu můžete zahrnout až 8 polí. Na rozdíl od MongoDB byste měli vytvořit složený index pouze v případě, že dotaz potřebuje efektivně řadit na více polí najednou. Pro dotazy s více filtry, které není nutné řadit, měli byste vytvořit více indexů jednoho pole namísto jednoho složených indexů.

Následující příkaz vytvoří složený index polí `name` `age`a :

`db.coll.createIndex({name:1,age:1})`

Složené indexy lze efektivně řadit na více polí najednou, například:

`db.coll.find().sort({name:1,age:1})`

Výše uvedený složený index lze také použít pro efektivní řazení dotazu s opačným pořadím řazení ve všech polích. Tady je příklad:

`db.coll.find().sort({name:-1,age:-1})`

Pořadí cest ve složeném indexu však musí přesně odpovídat dotazu. Tady je příklad dotazu, který by vyžadoval další složený index:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Víceklíčké indexy

Azure Cosmos DB vytvoří víceklíčové indexy pro indexování obsahu uloženého v polích. Pokud indexujete pole s hodnotou pole, Azure Cosmos DB automaticky indexuje každý prvek v poli.

### <a name="geospatial-indexes"></a>Geoprostorové indexy

Mnoho geoprostorových operátorů bude mít prospěch z geoprostorových indexů. Rozhraní API Azure Cosmos DB pro MongoDB v současné době podporuje `2dsphere` indexy. `2d`indexy ještě nejsou podporovány.

Zde je příklad pro vytvoření geoprostorového `location` indexu v poli:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Textové indexy

Textové indexy nejsou aktuálně podporovány. Pro dotazy pro textové vyhledávání na řetězce, měli byste použít [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) integrace s Azure Cosmos DB.

## <a name="index-properties"></a>Vlastnosti indexu

Následující operace jsou společné pro oba účty obsluhující drátový protokol verze 3.6 a účty obsluhující starší verze drátového protokolu. Další informace o [podporovaných indexech a indexovaných vlastnostech](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Jedinečné indexy

[Jedinečné indexy](unique-keys.md) jsou užitečné k vynucování, aby dva nebo více dokumentů neobsahovalo v indexovaných polích stejnou hodnotu.

>[!Important]
> Jedinečné indexy lze vytvořit pouze v případě, že kolekce je prázdná (neobsahuje žádné dokumenty).

Následující příkaz vytvoří jedinečný index v poli "student_id":

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

Pro rozdělení kolekce vytvoření jedinečný index vyžaduje poskytnutí klíče střepu (oddíl). Jinými slovy, všechny jedinečné indexy u horizontálně dělené kolekce jsou složené indexy, ve kterých je jedno z polí klíčem oddílu.

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

### <a name="ttl-indexes"></a>Indexy TTL

Pokud chcete povolit vypršení platnosti dokumentu v konkrétní kolekci, je potřeba vytvořit [index TTL (Time to Live)](../cosmos-db/time-to-live.md). Index TTL je index u pole _ts s hodnotou expireAfterSeconds.

Příklad:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Předchozí příkaz způsobí odstranění všech dokumentů v kolekci ```db.coll```, které se během posledních 10 sekund neupravily.

> [!NOTE]
> **_ts** je pole specifické pro Azure Cosmos DB a není přístupné z klientů MongoDB. Jedná se o vyhrazenou (systémovou) vlastnost obsahující časové razítko poslední úpravy dokumentu.

## <a name="track-the-index-progress"></a>Sledování průběhu indexu

Verze 3.6 rozhraní API Azure Cosmos DB pro účty `currentOp()` MongoDB podporuje příkaz ke sledování průběhu indexu v instanci databáze. Tento příkaz vrátí dokument, který obsahuje informace o probíhajících operacích v instanci databáze. Příkaz `currentOp` se používá ke sledování všech probíhajících operací v nativním MongoDB, zatímco v rozhraní API Azure Cosmos DB pro MongoDB tento příkaz podporuje pouze sledování operace indexu.

Zde jsou některé příklady, které `currentOp` ukazují, jak pomocí příkazu sledovat průběh indexu:

* Získejte průběh indexu pro kolekci:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Získejte průběh indexu pro všechny kolekce v databázi:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Získejte průběh indexu pro všechny databáze a kolekce v účtu Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Podrobnosti průběhu indexu obsahují procento průběhu pro aktuální operaci indexu. Následující příklad ukazuje formát výstupního dokumentu pro různé fáze průběhu indexu:

1. Pokud operace indexu na 'foo' kolekce a 'bar' databáze, která má 60 % indexování kompletní bude mít následující výstupní dokument. `Inprog[0].progress.total`jako cílové dokončení 100.

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

2. Pro operaci indexu, která právě začala na 'foo' kolekce a 'pruh' databáze, výstupní dokument může zobrazit 0% pokrok, dokud nedosáhne měřitelné úrovně.

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

3. Po dokončení operace indexu probíhající, výstupní dokument zobrazí prázdné inprog operace.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Aktualizace indexu na pozadí

Bez ohledu na hodnotu zadanou pro vlastnost **indexu pozadí,** aktualizace indexu jsou vždy provedeny na pozadí. Aktualizace indexu spotřebovávají ru s nižší prioritou než jiné databázové operace. Změny indexu proto nebudou mít za následek žádné prostoje pro zápisy, aktualizace nebo odstranění.

Při přidávání nového indexu budou dotazy okamžitě využívat. To znamená, že dotazy nemusí vrátit všechny odpovídající výsledky a provede tak bez vrácení chyb. Po dokončení transformace indexu budou konzistentní výsledky dotazu. Průběh [indexu](#track-the-index-progress)můžete sledovat .

## <a name="migrating-collections-with-indexes"></a>Migrace kolekcí s indexy

V současné době je vytváření jedinečných indexů možné pouze v případě, že kolekce neobsahuje žádné dokumenty. Oblíbené nástroje pro migraci MongoDB se pokouší vytvořit jedinečné indexy po importu dat. Chcete-li tento problém obejít, doporučuje se, aby uživatelé ručně vytvořili odpovídající kolekce ```mongorestore``` a jedinečné indexy `--noIndexRestore` namísto povolení nástroje pro migraci (pro toto chování je dosaženo pomocí příznaku v příkazovém řádku).

## <a name="indexing-for-version-32"></a>Indexování pro verzi 3.2

Pro účty Azure Cosmos DB kompatibilní s verzí 3.2 drátového protokolu MongoDB se liší dostupné funkce indexování a výchozí hodnoty. Můžete [zkontrolovat verzi svého účtu](mongodb-feature-support-36.md#protocol-support). Na verzi 3.6 můžete upgradovat podáním [žádosti o podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Pokud používáte verzi 3.2, tato část popisuje klíčové rozdíly s verzí 3.6.

### <a name="dropping-the-default-indexes-32"></a>Uvolnění výchozí indexy (3.2)

Na rozdíl od verze 3.6 rozhraní API Azure Cosmos DB pro MongoDB verze 3.2 indexuje každou vlastnost ve výchozím nastavení. Následující příkaz lze použít k přetažení těchto ```coll```výchozích indexů pro kolekci :

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Po uvolnění výchozí indexy, můžete přidat na další indexy, jak je tomu ve verzi 3.6.

### <a name="compound-indexes-32"></a>Složené indexy (3.2)

Složené indexy obsahují odkazy na více polí dokumentu. Chcete-li vytvořit složený index, upgradujte na verzi 3.6 podáním [žádosti o podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Další kroky

* [Indexování ve službě Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Platnost dat v Azure Cosmos DB automaticky s časem žít](../cosmos-db/time-to-live.md)
