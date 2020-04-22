---
title: Správa indexování v rozhraní API Azure Cosmos DB pro MongoDB
description: Tento článek představuje přehled možností indexování Azure Cosmos DB pomocí rozhraní API MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732707"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Správa indexování v rozhraní API Azure Cosmos DB pro MongoDB

Rozhraní API Azure Cosmos DB pro MongoDB využívá základní možnosti správy indexů azure cosmos db. Tento článek se zaměřuje na to, jak přidat indexy pomocí rozhraní API Azure Cosmos DB pro MongoDB. Můžete si také přečíst [přehled indexování v Azure Cosmos DB,](index-overview.md) který je relevantní ve všech api.

## <a name="indexing-for-mongodb-server-version-36"></a>Indexování pro server MongoDB verze 3.6

Rozhraní API Azure Cosmos DB pro server MongoDB verze `_id` 3.6 automaticky indexuje pole, které nelze vynechat. Automaticky vynucuje jedinečnost `_id` pole na klíč střepu.

Chcete-li indexovat další pole, použijte příkazy pro správu indexu MongoDB. Stejně jako v MongoDB rozhraní API Azure Cosmos DB `_id` pro MongoDB automaticky indexuje pouze pole. Tato výchozí zásada indexování se liší od rozhraní AZURE Cosmos DB SQL API, které ve výchozím nastavení indexuje všechna pole.

Chcete-li použít řazení na dotaz, musíte vytvořit index pro pole použitá v operaci řazení.

## <a name="index-types"></a>Typy indexů

### <a name="single-field"></a>Jedno pole

Indexy můžete vytvořit v libovolném poli. Pořadí řazení indexu jednoho pole nezáleží. Následující příkaz vytvoří index v `name`poli :

`db.coll.createIndex({name:1})`

Jeden dotaz používá více indexů jednoho pole, pokud je k dispozici. Můžete vytvořit až 500 indexů jednoho pole na kontejner.

### <a name="compound-indexes-mongodb-server-version-36"></a>Složené indexy (MongoDB server verze 3.6)

Rozhraní API Azure Cosmos DB pro MongoDB podporuje složené indexy pro účty, které používají drátový protokol verze 3.6. Do složeného indexu můžete zahrnout až osm polí. Na rozdíl od MongoDB byste měli vytvořit složený index pouze v případě, že dotaz potřebuje efektivně řadit na více polí najednou. Pro dotazy s více filtry, které není nutné řadit, vytvořte více indexů jednoho pole namísto jednoho složených indexů.

Následující příkaz vytvoří složený index `name` polí `age`a :

`db.coll.createIndex({name:1,age:1})`

Složené indexy můžete použít k efektivnímu řazení na více polích najednou, jak je znázorněno v následujícím příkladu:

`db.coll.find().sort({name:1,age:1})`

Předchozí složený index můžete také použít k efektivnímu řazení dotazu s opačným pořadím řazení ve všech polích. Tady je příklad:

`db.coll.find().sort({name:-1,age:-1})`

Pořadí cest ve složeném indexu však musí přesně odpovídat dotazu. Tady je příklad dotazu, který by vyžadoval další složený index:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Víceklíčké indexy

Azure Cosmos DB vytvoří víceklíčové indexy pro indexování obsahu uloženého v polích. Pokud indexujete pole s hodnotou pole, Azure Cosmos DB automaticky indexuje každý prvek v poli.

### <a name="geospatial-indexes"></a>Geoprostorové indexy

Mnoho geoprostorových operátorů bude mít prospěch z geoprostorových indexů. Rozhraní API Azure Cosmos DB pro MongoDB v současné době podporuje `2dsphere` indexy. Rozhraní API ještě `2d` nepodporuje indexy.

Zde je příklad vytvoření geoprostorového indexu `location` v poli:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Textové indexy

Rozhraní API Azure Cosmos DB pro MongoDB aktuálně nepodporuje textové indexy. Pro dotazy pro textové vyhledávání na řetězce, měli byste použít [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) integrace s Azure Cosmos DB.

## <a name="index-properties"></a>Vlastnosti indexu

Následující operace jsou společné pro účty obsluhující drátový protokol verze 3.6 a účty starší verze. Další informace o [podporovaných indexech a indexovaných vlastnostech](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Jedinečné indexy

[Jedinečné indexy](unique-keys.md) jsou užitečné pro vynucení, že dva nebo více dokumentů neobsahují stejnou hodnotu pro indexovaná pole.

> [!IMPORTANT]
> Jedinečné indexy lze vytvořit pouze v případě, že kolekce je prázdná (neobsahuje žádné dokumenty).

Následující příkaz vytvoří v poli `student_id`jedinečný index :

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

Pro rozdělení kolekce je nutné zadat klíč šišle (oddíl) k vytvoření jedinečného indexu. Jinými slovy, všechny jedinečné indexy u horizontálně dělené kolekce jsou složené indexy, ve kterých je jedno z polí klíčem oddílu.

Následující příkazy vytvoří třídivou kolekci ```coll``` (klíč šibenic ) `student_id` `university` ```university```s jedinečným indexem polí a:

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

V předchozím příkladu vynechání ```"university":1``` klauzule vrátí chybu s následující zprávou:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>Indexy TTL

Chcete-li povolit vypršení platnosti dokumentu v určité kolekci, je třeba vytvořit [index time-to-live (TTL).](../cosmos-db/time-to-live.md) Index TTL je index `_ts` v poli `expireAfterSeconds` s hodnotou.

Příklad:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Předchozí příkaz odstraní všechny dokumenty ```db.coll``` v kolekci, které nebyly změněny v posledních 10 sekundách.

> [!NOTE]
> Pole **_ts** je specifické pro Azure Cosmos DB a není přístupné z klientů MongoDB. Jedná se o vyhrazenou (systémovou) vlastnost, která obsahuje časové razítko poslední změny dokumentu.

## <a name="track-index-progress"></a>Sledovat průběh indexu

Verze 3.6 rozhraní API Azure Cosmos DB pro `currentOp()` MongoDB podporuje příkaz ke sledování průběhu indexu na instanci databáze. Tento příkaz vrátí dokument, který obsahuje informace o probíhajících operacích s instancí databáze. `currentOp` Příkaz slouží ke sledování všech probíhajících operací v nativním MongoDB. V rozhraní API Azure Cosmos DB pro MongoDB tento příkaz podporuje jenom sledování operace indexu.

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

### <a name="examples-of-index-progress-output"></a>Příklady výstupu pokroku indexu

Podrobnosti o průběhu indexu ukazují procento průběhu pro aktuální operaci indexu. Zde je příklad, který ukazuje formát výstupního dokumentu pro různé fáze průběhu indexu:

- Operace indexu na kolekci "foo" a "bar" databáze, která je dokončena 60 procent bude mít následující výstupní dokument. Pole `Inprog[0].progress.total` zobrazuje 100 jako procento dokončení cíle.

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

- Pokud operace indexu právě byla spuštěna v kolekci "foo" a "pruhové" databázi, výstupní dokument může zobrazit 0% průběh, dokud nedosáhne měřitelné úrovně.

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

- Po dokončení operace indexu probíhající, výstupní dokument `inprog` zobrazí prázdné operace.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Aktualizace indexu na pozadí

Bez ohledu na hodnotu zadanou pro vlastnost **indexu pozadí,** aktualizace indexu jsou vždy provedeny na pozadí. Vzhledem k tomu, že aktualizace indexu spotřebovávají jednotky požadavků (RU) s nižší prioritou než jiné databázové operace, změny indexu nebudou mít za následek žádné prostoje pro zápisy, aktualizace nebo odstranění.

Přidáte-li nový index, dotazy budou okamžitě používat index. To znamená, že dotazy nemusí vrátit všechny odpovídající výsledky a provede tak bez vrácení chyb. Po dokončení transformace indexu budou konzistentní výsledky dotazu. Můžete [sledovat průběh indexu](#track-index-progress).

## <a name="migrate-collections-with-indexes"></a>Migrace kolekcí pomocí indexů

V současné době můžete vytvořit pouze jedinečné indexy, pokud kolekce neobsahuje žádné dokumenty. Populární nástroje pro migraci MongoDB se po importu dat pokoušejí vytvořit jedinečné indexy. Chcete-li tento problém obejít, můžete ručně vytvořit odpovídající kolekce a jedinečné indexy namísto povolení nástroje pro migraci. (Toto chování můžete ```mongorestore``` dosáhnout `--noIndexRestore` pomocí příznaku v příkazovém řádku.)

## <a name="indexing-for-mongodb-version-32"></a>Indexování pro MongoDB verze 3.2

Dostupné funkce indexování a výchozí hodnoty se liší pro účty Azure Cosmos, které jsou kompatibilní s verzí 3.2 drátového protokolu MongoDB. Můžete [zkontrolovat verzi svého účtu](mongodb-feature-support-36.md#protocol-support). Na verzi 3.6 můžete upgradovat podáním [žádosti o podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Pokud používáte verzi 3.2, tato část popisuje klíčové rozdíly s verzí 3.6.

### <a name="dropping-default-indexes-version-32"></a>Uvolnění výchozích indexů (verze 3.2)

Na rozdíl od verze 3.6 rozhraní API Azure Cosmos DB pro MongoDB verze 3.2 indexuje každou vlastnost ve výchozím nastavení. Následující příkaz můžete přetáhnout tyto výchozí indexy```coll```pro kolekci ( ):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Po uvolnění výchozí indexy, můžete přidat další indexy, jako byste ve verzi 3.6.

### <a name="compound-indexes-version-32"></a>Složené indexy (verze 3.2)

Složené indexy obsahují odkazy na více polí dokumentu. Chcete-li vytvořit složený index, upgradujte na verzi 3.6 podáním [žádosti o podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Další kroky

* [Indexování ve službě Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Platnost dat v Azure Cosmos DB automaticky s časem žít](../cosmos-db/time-to-live.md)
