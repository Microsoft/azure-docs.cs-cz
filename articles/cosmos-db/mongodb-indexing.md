---
title: Správa indexování v rozhraní Azure Cosmos DB API pro MongoDB
description: Tento článek nabízí přehled možností indexování Azure Cosmos DB pomocí rozhraní API Azure Cosmos DB pro MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 10/21/2020
author: timsander1
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 6e084a890dd5c772fbf576ddc50fd26b2d1774f0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487377"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Správa indexování v rozhraní Azure Cosmos DB API pro MongoDB

Rozhraní API pro Azure Cosmos DB MongoDB využívá základní možnosti správy indexů Azure Cosmos DB. Tento článek se zaměřuje na přidání indexů pomocí rozhraní API Azure Cosmos DB pro MongoDB. Můžete si také přečíst [Přehled indexování v Azure Cosmos DB](index-overview.md) relevantním pro všechna rozhraní API.

## <a name="indexing-for-mongodb-server-version-36"></a>Indexování pro MongoDB Server verze 3,6

Rozhraní Azure Cosmos DB API pro MongoDB Server verze 3,6 automaticky indexuje `_id` pole, které nejde vyřadit. Automaticky vynutila jedinečnost `_id` pole na horizontálních oddílů klíč. V rozhraní Azure Cosmos DB API pro MongoDB, horizontálního dělení a indexování jsou oddělené koncepty. Nemusíte indexovat svůj horizontálních oddílů klíč. Pokud se ale u všech dalších vlastností v dokumentu jedná o společný filtr v dotazech, doporučujeme indexovat klíč horizontálních oddílů.

Pokud chcete indexovat další pole, můžete použít příkazy MongoDB index-management. Stejně jako v Azure Cosmos DB MongoDB rozhraní API pro MongoDB automaticky indexuje `_id` pole. Tyto výchozí zásady indexování se liší od rozhraní SQL API služby Azure Cosmos DB, které ve výchozím nastavení indexuje všechna pole.

Chcete-li použít řazení pro dotaz, je nutné vytvořit index pro pole použitá v operaci řazení.

## <a name="index-types"></a>Typy indexů

### <a name="single-field"></a>Jedno pole

Můžete vytvořit indexy pro každé jedno pole. Pořadí řazení indexu jednoho pole nezáleží. Následující příkaz vytvoří index v poli `name` :

`db.coll.createIndex({name:1})`

Jeden dotaz používá více indexů s jedním polem, kde je k dispozici. Pro každý kontejner můžete vytvořit až 500 indexů s jedním polem.

### <a name="compound-indexes-mongodb-server-version-36"></a>Složené indexy (MongoDB Server verze 3,6)

Rozhraní API pro Azure Cosmos DB pro MongoDB podporuje složené indexy pro účty, které používají síťový protokol verze 3,6. Do složeného indexu můžete zahrnout až osm polí. Na rozdíl od MongoDB byste měli vytvořit složený index pouze v případě, že je nutné dotaz efektivně seřadit ve více polích najednou. Pro dotazy s více filtry, které není nutné řadit, je třeba vytvořit více indexů jednoho pole namísto jednoho složeného indexu. 

> [!NOTE]
> Nelze vytvořit složené indexy pro vnořené vlastnosti nebo pole.

Následující příkaz vytvoří složený index pro pole `name` a `age` :

`db.coll.createIndex({name:1,age:1})`

Složené indexy můžete použít k efektivnímu řazení více polí najednou, jak je znázorněno v následujícím příkladu:

`db.coll.find().sort({name:1,age:1})`

Můžete také použít předchozí složený index a efektivně tak řadit dotaz s opačným pořadím řazení u všech polí. Tady je příklad:

`db.coll.find().sort({name:-1,age:-1})`

Nicméně sekvence cest ve složeném indexu se musí přesně shodovat s dotazem. Tady je příklad dotazu, který by vyžadoval další složený index:

`db.coll.find().sort({age:1,name:1})`

> [!NOTE]
> Složené indexy se používají pouze v dotazech, které řadí výsledky. Pro dotazy, které mají více filtrů, které nepotřebují řadit, vytvářejte Multipe jednoduché indexy polí.

### <a name="multikey-indexes"></a>Multikey indexy

Azure Cosmos DB vytváří indexy multikey k indexování obsahu uloženého v polích. Pokud indexuje pole s hodnotou pole, Azure Cosmos DB automaticky indexuje každý prvek v poli.

### <a name="geospatial-indexes"></a>Geoprostorové indexy

Mnohé geoprostorové operátory budou využívat geoprostorové indexy. V současné době Azure Cosmos DB rozhraní API pro MongoDB podporuje `2dsphere` indexy. Rozhraní API zatím nepodporuje `2d` indexy.

Tady je příklad vytvoření geoprostorového indexu v `location` poli:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Indexy textu

Rozhraní API pro MongoDB Azure Cosmos DB v současné době nepodporuje textové indexy. Pro dotazy na hledání textu v řetězcích byste měli použít službu [Azure kognitivní hledání](../search/search-howto-index-cosmosdb.md) Integration s Azure Cosmos DB. 

## <a name="wildcard-indexes"></a>Indexy zástupných znaků

Pro podporu dotazů na neznámá pole můžete použít indexy se zástupnými znaky. Představte si, že máte kolekci, která obsahuje data o rodinách.

Tady je část ukázkového dokumentu v této kolekci:

```json
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "grade": "5"
     }
  ]
```

Tady je další příklad, tentokrát s mírně odlišnou sadou vlastností v `children` :

```json
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
      },
      {
        "familyName": "Merriam",
        "givenName": "John",
      }
  ]
```

V této kolekci můžou dokumenty mít mnoho různých vlastností. Pokud jste chtěli indexovat všechna data v poli `children` , máte dvě možnosti: vytvořit samostatné indexy pro každou jednotlivou vlastnost nebo vytvořit jeden index zástupného znaku pro celé `children` pole.

### <a name="create-a-wildcard-index"></a>Vytvoření indexu zástupných znaků

Následující příkaz vytvoří index zástupného znaku u všech vlastností v rámci `children` :

`db.coll.createIndex({"children.$**" : 1})`

Na **rozdíl od MongoDB můžou indexy zástupných znaků podporovat více polí v predikátech dotazů**. Pokud použijete jeden index zástupného znaku namísto vytvoření samostatného indexu pro každou vlastnost, nebudete mít rozdíl ve výkonu dotazů.

Pomocí syntaxe zástupných znaků můžete vytvořit následující typy indexů:

- Jedno pole
- Geoprostorové

### <a name="indexing-all-properties"></a>Indexování všech vlastností

Tady je postup, jak můžete vytvořit index zástupného znaku u všech polí:

`db.coll.createIndex( { "$**" : 1 } )`

> [!NOTE]
> Pokud začínáte s vývojem, **důrazně** doporučujeme začít se zástupným indexem u všech polí. To může zjednodušit vývoj a usnadnit optimalizaci dotazů.

U dokumentů s mnoha poli se může za zápisy a aktualizace účtovat poplatek vysoké jednotky žádosti (RU). Proto pokud máte zatížení náročné na zápis, měli byste se rozhodnout pro použití zástupných indexů na jednotlivé cesty indexů.

### <a name="limitations"></a>Omezení

Indexy zástupných znaků nepodporují žádné z následujících typů indexů nebo vlastností:

- Kombinovanou
- TTL
- Jedinečná

Na **rozdíl od MongoDB**v rozhraní API služby Azure Cosmos DB pro MongoDB **nemůžete** použít indexy zástupných znaků pro:

- Vytvoření indexu zástupných znaků, který obsahuje více konkrétních polí

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection " :
        {
           "children.givenName" : 1,
           "children.grade" : 1
        }
    }
)`

- Vytvoření zástupného znaku, který vylučuje více konkrétních polí

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection" :
        {
           "children.givenName" : 0,
           "children.grade" : 0
        }
    }
)`

Alternativně můžete vytvořit více indexů zástupných znaků.

## <a name="index-properties"></a>Vlastnosti indexu

Následující operace jsou běžné pro účty, které obsluhují síťový protokol verze 3,6 a účty obsluhující starší verze. Můžete si přečíst další informace o [podporovaných indexech a indexovaných vlastnostech](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Jedinečné indexy

[Jedinečné indexy](unique-keys.md) jsou užitečné pro vynucování, že dva nebo více dokumentů neobsahují stejnou hodnotu pro indexovaná pole.

> [!IMPORTANT]
> Jedinečné indexy lze vytvořit pouze v případě, že je kolekce prázdná (neobsahuje žádné dokumenty).

Následující příkaz vytvoří jedinečný index v poli `student_id` :

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

Pro kolekce horizontálně dělené je nutné zadat klíč horizontálních oddílů (partition) pro vytvoření jedinečného indexu. Jinými slovy, všechny jedinečné indexy u horizontálně dělené kolekce jsou složené indexy, ve kterých je jedno z polí klíčem oddílu.

Následující příkazy vytvoří kolekci horizontálně dělené ```coll``` (klíč horizontálních oddílů je ```university``` ) s jedinečným indexem pro pole `student_id` a `university` :

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

V předchozím příkladu vynechá klauzule vynechání ```"university":1``` chybové zprávy s následující zprávou:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>Indexy TTL

Chcete-li povolit vypršení platnosti dokumentu v konkrétní kolekci, je nutné vytvořit [index TTL (Time to Live)](../cosmos-db/time-to-live.md). Index TTL je index `_ts` pole s `expireAfterSeconds` hodnotou.

Příklad:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Předchozí příkaz odstraní všechny dokumenty v ```db.coll``` kolekci, které nebyly během posledních 10 sekund změněny.

> [!NOTE]
> Pole **_ts** je specifické pro Azure Cosmos DB a není dostupné z klientů MongoDB. Jedná se o vyhrazenou (systémovou) vlastnost, která obsahuje časové razítko poslední úpravy dokumentu.

## <a name="track-index-progress"></a>Sledovat průběh indexu

Rozhraní Azure Cosmos DB API pro MongoDB verze 3,6 podporuje `currentOp()` příkaz ke sledování průběhu indexování instance databáze. Tento příkaz vrátí dokument, který obsahuje informace o probíhajících operacích instance databáze. Pomocí příkazu můžete `currentOp` sledovat všechny probíhající operace v nativním MongoDB. V rozhraní Azure Cosmos DB API pro MongoDB tento příkaz podporuje pouze sledování operace indexu.

Tady je několik příkladů, které ukazují, jak pomocí `currentOp` příkazu sledovat průběh indexu:

* Získat průběh indexu pro kolekci:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Získat průběh indexu pro všechny kolekce v databázi:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Získejte průběh indexu pro všechny databáze a kolekce v účtu Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Příklady výstupu průběhu indexu

Podrobnosti o průběhu indexu zobrazují procento průběhu aktuální operace indexu. Tady je příklad, který ukazuje formát výstupního dokumentu pro různé fáze průběhu indexu:

- Operace indexu v kolekci "foo" a "bar" databáze, která je 60% dokončená, bude mít následující výstupní dokument. V `Inprog[0].progress.total` poli se zobrazí 100 jako procento cíle dokončení.

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

- Pokud byla operace indexu právě spuštěna v kolekci "foo" a "bar" databáze, výstupní dokument může zobrazit 0 procent průběhu až do dosažení měřitelné úrovně.

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

- Po dokončení probíhající operace indexu zobrazí výstupní dokument prázdné `inprog` operace.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="background-index-updates"></a>Aktualizace indexu na pozadí

Bez ohledu na hodnotu zadanou pro vlastnost index na **pozadí** jsou aktualizace indexu vždy prováděny na pozadí. Vzhledem k tomu, že aktualizace indexu spotřebovávají jednotky žádostí (ru) s nižší prioritou než jiné databázové operace, nebudou mít změny v indexu žádný výpadek pro zápisy, aktualizace nebo odstranění.

Při přidávání nového indexu nemá žádný vliv na dostupnost čtení. Po dokončení transformace indexu budou dotazy využívat pouze nové indexy. Při transformaci indexu bude modul dotazu dál používat stávající indexy, takže budete mít podobný výkon při čtení během transformace indexování na to, co jste předtím provedli při zahájení změny indexování. Při přidávání nových indexů nehrozí riziko neúplných nebo nekonzistentních výsledků dotazu.

Při odebírání indexů a okamžitém spuštění dotazů mají filtry na vyřazených indexech, výsledky mohou být nekonzistentní a nedokončené, dokud se nedokončí transformace indexu. Pokud odeberete indexy, dotazovací stroj neposkytne konzistentní nebo úplné výsledky, když dotazy filtrují na těchto nově odebraných indexech. Většina vývojářů neprovádí vyřazení indexů a potom se okamžitě pokusí o dotazování, takže v praxi je tato situace nepravděpodobná.

> [!NOTE]
> [Průběh indexu můžete sledovat](#track-index-progress).

## <a name="migrate-collections-with-indexes"></a>Migrace kolekcí s indexy

V současné době je možné vytvořit jedinečné indexy pouze v případě, že kolekce neobsahuje žádné dokumenty. Oblíbené nástroje pro migraci MongoDB se po importu dat pokoušejí vytvořit jedinečné indexy. Pokud chcete tento problém obejít, můžete ručně vytvořit odpovídající kolekce a jedinečné indexy místo toho, aby se mohl nástroj pro migraci vyzkoušet. (Toto chování můžete dosáhnout pro pomocí ```mongorestore``` `--noIndexRestore` příznaku v příkazovém řádku.)

## <a name="indexing-for-mongodb-version-32"></a>Indexování pro MongoDB verze 3,2

Dostupné funkce indexování a výchozí hodnoty se liší pro účty Azure Cosmos, které jsou kompatibilní s verzí 3,2 přenosového protokolu MongoDB. Můžete si [ověřit verzi vašeho účtu](mongodb-feature-support-36.md#protocol-support) a [upgradovat na verzi 3,6](mongodb-version-upgrade.md).

Pokud používáte verzi 3,2, Tato část popisuje klíčové rozdíly ve verzi 3,6.

### <a name="dropping-default-indexes-version-32"></a>Vyřazení výchozích indexů (verze 3,2)

Na rozdíl od verze 3,6 rozhraní API Azure Cosmos DB rozhraní API pro MongoDB, verze 3,2 ve výchozím nastavení indexuje každou vlastnost. K vyřazení těchto výchozích indexů pro kolekci () můžete použít následující příkaz ```coll``` :

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Po vyřazení výchozích indexů můžete přidat další indexy, stejně jako ve verzi 3,6.

### <a name="compound-indexes-version-32"></a>Složené indexy (verze 3,2)

Složené indexy obsahují odkazy na více polí dokumentu. Pokud chcete vytvořit složený index, [upgradujte na verzi 3,6](mongodb-version-upgrade.md).

### <a name="wildcard-indexes-version-32"></a>Indexy zástupných znaků (verze 3,2)

Pokud chcete vytvořit index zástupného znaku, [upgradujte na verzi 3,6](mongodb-version-upgrade.md).

## <a name="next-steps"></a>Další kroky

* [Indexování ve službě Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Automatické vypršení platnosti dat v Azure Cosmos DB s časem až Live](../cosmos-db/time-to-live.md)
* Další informace o vztahu mezi vytvářením oddílů a indexováním najdete v článku postup [dotazování na kontejner Azure Cosmos](how-to-query-container.md) .