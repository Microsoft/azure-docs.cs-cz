---
title: Podpora Azure Cosmos DB Gremlin | Microsoft Docs
description: Další informace o jazyka Gremlin z Apache TinkerPop. Další informace, které funkce a kroky jsou k dispozici v Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: LuisBosquez
manager: kfile
ms.assetid: 6016ccba-0fb9-4218-892e-8f32a1bcc590
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/02/2018
ms.author: lbosq
ms.openlocfilehash: c5e904c741c96808555718b0d90368e1bf770ecb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Graf podporu Azure Cosmos DB Gremlin
Podporuje Azure Cosmos DB [Apache Tinkerpop](http://tinkerpop.apache.org) graf traversal jazyk [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), což je rozhraní Graph API pro vytváření entit grafu a provádění operace dotazů grafu. Jazyk Gremlin slouží k vytvoření grafu entit (vrcholy a okraje), změnit vlastnosti v rámci těchto entit, provádět dotazy a traversals a odstranit entity. 

Azure Cosmos DB přináší funkce připravené pro podniky k databázím grafu. To zahrnuje globální distribuční nezávislé škálování úložiště a propustnosti, latenci předvídatelný řádu milisekund, automatické indexování, SLA, přečtěte si dostupnosti pro účty databáze pokrývání uzlů dvou nebo více oblastech Azure. Protože Azure Cosmos DB podporuje TinkerPop/Gremlin, můžete snadno migrovat aplikace napsané v jiné databáze grafu bez nutnosti provádět změny kódu. Kromě toho na základě Gremlin podpory Azure Cosmos DB zajišťuje bezproblémovou integraci s povoleným TinkerPop analytics architektury, jako je [Apache Spark GraphX](http://spark.apache.org/graphx/). 

V tomto článku jsme zadejte rychlé návod Gremlin a výčet Gremlin funkcí a kroků, které podporuje rozhraní Graph API.

## <a name="gremlin-by-example"></a>Gremlin příklad
Abyste pochopili, jak dotazy mohou být vyjádřeny v Gremlin použijeme Ukázka grafu. Následující obrázek znázorňuje obchodní aplikace, která spravuje data o uživatelích, zájmů a zařízení ve formě grafu.  

![Zobrazuje osob, zařízení a zájmů ukázkové databáze](./media/gremlin-support/sample-graph.png) 

Tento graf má následující typy vrchol (nazývané "Popisek" v Gremlin):

- Lidé: Graf má tři osoby, každý s každým Thomas a Ben
- Zájmů: Jejich zájmů, v tomto příkladu hra fotbalové
- Zařízení: Zařízení, která uživatelé použití
- Operační systémy: Operační systémy zařízení se systémem

Jsme představují vztahy mezi tyto entity prostřednictvím následující typy edge/popisky:

- Zná: například "Thomas ví, každý s každým"
- Také zajímat: K zastupování zájmů osob v našem grafu, například "Ben má zájem o fotbalu"
- RunsOS: Přenosný počítač spustí operační systém Windows
- Používá: Představují které zařízení uživatel používá. Například každý s každým používá Motorola telefon se sériovým číslem 77

Umožňuje spustit některé operace pro tento graf pomocí [Gremlin konzoly](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console). Můžete také provést tyto operace pomocí Gremlin ovladačů v platformě podle vašeho výběru (Java, Node.js, Python nebo .NET).  Předtím, než se podíváme na co je podporováno v Azure Cosmos DB, podíváme se na několik příkladů, abyste se seznámili s syntaxe.

První Podíváme se na CRUD. Následující příkaz Gremlin vloží do grafu vrchol "Thomas":

```
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

V dalším kroku následující příkaz Gremlin vloží "ví" okraje mezi Thomas a každý s každým.

```
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Následující dotaz vrátí vrcholy "osoba" v sestupném pořadí podle jejich názvy první:
```
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Kde grafy nám je, když je nutné odpovědět otázky typu "Jaké operační systémy se přátelích Thomas použít?". Můžete spustit tento jednoduchý traversal Gremlin získat tyto informace z grafu:

```
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Nyní Podíváme se na databázi Cosmos Azure poskytuje vývojářům Gremlin.

## <a name="gremlin-features"></a>Funkce gremlin
TinkerPop je standard, který obsahuje širokou škálu technologie grafu. Proto musí standardní terminologii popisu, jaké funkce jsou poskytované poskytovatelem grafu. Azure Cosmos DB poskytuje souběžnosti trvalé, vysoká, databázi zapisovatelné grafu, která může být rozdělený napříč více servery nebo clustery. 

Následující tabulka uvádí TinkerPop funkce, které jsou implementované Cosmos databázi Azure: 

| Kategorie | Azure Cosmos DB implementace |  Poznámky | 
| --- | --- | --- |
| Funkce grafu | Poskytuje trvalosti a ConcurrentAccess. Určeno k podpoře transakce | Metody počítač se dá implementovat prostřednictvím konektoru Spark. |
| Proměnné funkce | Podporuje logická hodnota, celé číslo, bajtů, dvakrát, Float, celé číslo, Long, řetězec | Podporuje primitivní typy, není kompatibilní s komplexní typy prostřednictvím datového modelu |
| Vrchol funkce | Podporuje RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty –, RemoveProperty  | Podporuje vytváření, úpravu a odstranění vrcholy |
| Vrchol vlastnost funkce | StringIds, UserSuppliedIds, AddProperty –, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Podporuje vytváření, úpravy a odstraňování vrchol vlastnosti |
| Funkce edge | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty –, RemoveProperty | Podporuje vytváření, úpravu a odstranění okrajů |
| Funkce vlastnost Edge | Vlastnosti, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Podporuje vytváření, úpravy a odstraňování vlastnosti edge |

## <a name="gremlin-wire-format-graphson"></a>Gremlin přenosový formát: GraphSON

Používá Azure Cosmos DB [GraphSON formát](https://github.com/thinkaurelius/faunus/wiki/GraphSON-Format) při vrácení výsledků z Gremlin operace. GraphSON je standardní formát Gremlin představující vrcholy, okraje a vlastností (jeden a více hodnot vlastnosti) pomocí JSON. 

Například následující fragment kódu ukazuje znázornění GraphSON vrcholu *vrácen do klienta* z Azure Cosmos DB. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

Vlastnosti používané ve GraphSON pro vrcholy jsou následující:

| Vlastnost | Popis |
| --- | --- |
| id | ID pro vrchol. Musí být jedinečné (v kombinaci s hodnotou _partition, pokud je k dispozici) |
| Popisek | Popisek vrchol. Toto je volitelné a slouží k popisu typu entity. |
| type | Rozlišit vrcholy z jiných grafu dokumentů |
| properties | Kontejner uživatelem definované vlastnosti související s vrchol. Každá vlastnost může mít více hodnot. |
| _partition (Konfigurovat) | Klíč oddílu vrcholu. Slouží k škálování grafy pro více serverů |
| outE | Tato položka obsahuje seznam se okraje z vrchol. Ukládání informací o sousedství s vrchol umožňuje rychlé spuštění traversals. Okraje jsou seskupené podle jejich popisky. |

A hranici obsahuje následující informace, které pomůžou s odkazy na další části grafu.

| Vlastnost | Popis |
| --- | --- |
| id | ID pro hranici. Musí být jedinečné (v kombinaci s hodnotou _partition, pokud je k dispozici) |
| Popisek | Popisek okraj. Tato vlastnost je volitelná a slouží k popisu typu relace. |
| inV | Tato položka obsahuje seznam v vrcholy pro okraj. Ukládání informací o sousedství s hranou umožňuje rychlé spuštění traversals. Vrcholy jsou seskupené podle jejich popisky. |
| properties | Kontejner uživatelem definované vlastnosti související s hranou. Každá vlastnost může mít více hodnot. |

Každou vlastnost můžete ukládat víc hodnot v rámci pole. 

| Vlastnost | Popis |
| --- | --- |
| hodnota | Hodnota vlastnosti

## <a name="gremlin-partitioning"></a>Vytváření oddílů gremlin

V Azure Cosmos DB, grafy ukládají v rámci kontejnerů, které je možné škálovat nezávisle z hlediska úložiště a propustnost (vyjádřeno v normalizovaný požadavků za sekundu). Každý kontejner musí definovat volitelný, ale doporučuje vlastnost klíče oddílu, která určuje hranici logický oddíl pro související data. Každý vrchol okraj musí mít `id` vlastnosti, které jsou jedinečné pro entity v rámci této hodnotu klíče oddílu. Podrobnosti jsou popsané v [vytváření oddílů v Azure Cosmos DB](partition-data.md).

Operace gremlin fungují bezproblémově napříč daty grafu, které jsou v rozsahu více oddílů v Azure Cosmos DB. Doporučujeme ale, vyberte klíč oddílu pro vaše grafů, který se často používá jako filtr ve funkci dotazy, obsahuje mnoho různých hodnot a podobné frekvenci přístup tyto hodnoty. 

## <a name="gremlin-steps"></a>Kroky gremlin
Nyní Podíváme se na postup Gremlin nepodporuje Azure Cosmos DB. Úplný odkaz na Gremlin, najdete v části [TinkerPop odkaz](http://tinkerpop.apache.org/docs/current/reference).

| Krok | Popis | TinkerPop 3.2 dokumentace |
| --- | --- | --- |
| `addE` | Přidá okraj mezi dvěma vrcholy | [Krok addE](http://tinkerpop.apache.org/docs/current/reference/#addedge-step) |
| `addV` | Přidá vrchol grafu | [Krok addV](http://tinkerpop.apache.org/docs/current/reference/#addvertex-step) |
| `and` | Zajišťuje, že všechny traversals vrátit hodnotu | [a krok](http://tinkerpop.apache.org/docs/current/reference/#and-step) |
| `as` | Krok jedno přiřadit výstup krok proměnné | [jako krok](http://tinkerpop.apache.org/docs/current/reference/#as-step) |
| `by` | Jedno krok použít s `group` a `order` | [Tímto krokem](http://tinkerpop.apache.org/docs/current/reference/#by-step) |
| `coalesce` | Vrátí první traversal, který vrací výsledek | [sloučení krok](http://tinkerpop.apache.org/docs/current/reference/#coalesce-step) |
| `constant` | Vrátí konstantní hodnotu. Použít s `coalesce`| [konstantní krok](http://tinkerpop.apache.org/docs/current/reference/#constant-step) |
| `count` | Vrátí počet z průchodu | [počet krok](http://tinkerpop.apache.org/docs/current/reference/#count-step) |
| `dedup` | Vrátí hodnoty s odebranými duplikáty | [krok odstraňování duplicitních dat](http://tinkerpop.apache.org/docs/current/reference/#dedup-step) |
| `drop` | Zahodí hodnoty (vrchol nebo edge) | [Přetáhněte krok.](http://tinkerpop.apache.org/docs/current/reference/#drop-step) |
| `fold` | Jednání jako bariéry, která vypočítá agregace výsledků| [fold – krok](http://tinkerpop.apache.org/docs/current/reference/#fold-step) |
| `group` | Skupiny podle hodnoty zadané popisků| [Krok skupiny](http://tinkerpop.apache.org/docs/current/reference/#group-step) |
| `has` | Použít k filtrování vlastností, vrcholy a okrajů. Podporuje `hasLabel`, `hasId`, `hasNot`, a `has` variant. | [má krok](http://tinkerpop.apache.org/docs/current/reference/#has-step) |
| `inject` | Vložení hodnoty do datového proudu| [Vložit krok](http://tinkerpop.apache.org/docs/current/reference/#inject-step) |
| `is` | Používá k provádění filtr pomocí logický výraz | [je krok](http://tinkerpop.apache.org/docs/current/reference/#is-step) |
| `limit` | Používá k omezení počtu položek v průchodu| [limit krok](http://tinkerpop.apache.org/docs/current/reference/#limit-step) |
| `local` | Místní zabalí oddíl traversal podobná poddotazu | [místní krok](http://tinkerpop.apache.org/docs/current/reference/#local-step) |
| `not` | Používá se k vytváření negace filtru | [není krok](http://tinkerpop.apache.org/docs/current/reference/#not-step) |
| `optional` | Vrátí výsledek zadané traversal Pokud vrací výsledek jinak vrátí volání elementu | [volitelný krok](http://tinkerpop.apache.org/docs/current/reference/#optional-step) |
| `or` | Zajišťuje, aby se nejméně jedna z traversals vrací hodnotu | [nebo krok](http://tinkerpop.apache.org/docs/current/reference/#or-step) |
| `order` | Vrátí výsledky v určené pořadí řazení | [Krok pořadí](http://tinkerpop.apache.org/docs/current/reference/#order-step) |
| `path` | Vrátí úplnou cestu průchodu | [Krok cesty](http://tinkerpop.apache.org/docs/current/reference/#path-step) |
| `project` | Projekty vlastnosti jako mapování | [Krok projektu](http://tinkerpop.apache.org/docs/current/reference/#project-step) |
| `properties` | Vrací vlastnosti pro zadaný popisky | [Krok vlastnosti](http://tinkerpop.apache.org/docs/current/reference/#properties-step) |
| `range` | Filtry pro zadaný rozsah hodnot| [Krok rozsahu](http://tinkerpop.apache.org/docs/current/reference/#range-step) |
| `repeat` | V kroku opakuje pro zadaného počtu opakování. Použít pro opakování ve smyčce | [Opakujte krok](http://tinkerpop.apache.org/docs/current/reference/#repeat-step) |
| `sample` | Používá k ukázkové výsledky z průchodu | [Ukázka krok](http://tinkerpop.apache.org/docs/current/reference/#sample-step) |
| `select` | Používá se k projektu výsledky z průchodu |  [Vyberte krok](http://tinkerpop.apache.org/docs/current/reference/#select-step) | |
| `store` | Použít pro neblokující agregace z průchodu | [Krok úložiště](http://tinkerpop.apache.org/docs/current/reference/#store-step) |
| `tree` | Agregační cesty z vrchol do stromu | [Krok stromu](http://tinkerpop.apache.org/docs/current/reference/#tree-step) |
| `unfold` | Nezobrazovaly iterovat jako krok| [unfold – krok](http://tinkerpop.apache.org/docs/current/reference/#unfold-step) |
| `union` | Sloučení výsledky z více traversals| [Union krok](http://tinkerpop.apache.org/docs/current/reference/#union-step) |
| `V` | Obsahuje kroky potřebné pro traversals mezi vrcholy a okrajů `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV`, a `otherV` pro | [vrchol kroky](http://tinkerpop.apache.org/docs/current/reference/#vertex-steps) |
| `where` | Umožňuje filtrovat výsledky z průchodu. Podporuje `eq`, `neq`, `lt`, `lte`, `gt`, `gte`, a `between` operátory  | [kde krok](http://tinkerpop.apache.org/docs/current/reference/#where-step) |

Modul optimalizované zápisu poskytovaných Azure Cosmos DB podporuje automatické indexování všech vlastností v rámci vrcholy a okrajů ve výchozím nastavení. Proto dotazy s filtry, rozsahem dotazy, řazení, nebo agregace na žádnou vlastnost jsou zpracovány od indexu a efektivně obsluhovat. Další informace o tom, jak indexování funguje v Azure Cosmos DB najdete na našem dokumentu [vázané na schéma indexu](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Další postup
* Začínáme vytvoření grafu aplikace [pomocí naší sady SDK](create-graph-dotnet.md) 
* Další informace o [graf podporu](graph-introduction.md) v Azure Cosmos DB
