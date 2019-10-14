---
title: Podpora Azure Cosmos DB Gremlin
description: Přečtěte si o jazyce Gremlin z Apache TinkerPop. Zjistěte, které funkce a kroky jsou k dispozici v Azure Cosmos DB
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: 0c1ca054f9d28bb81c6f8acf6c0f43b134a596ed
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293792"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Podpora grafu Azure Cosmos DB Gremlin
Azure Cosmos DB podporuje jazyk procházení grafů [Apache Tinkerpop](https://tinkerpop.apache.org) , který se označuje jako [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). Jazyk Gremlin můžete použít k vytváření entit grafu (vrcholů a hran), úpravám vlastností v těchto entitách, provádění dotazů a procházení a odstraňování entit. 

V tomto článku poskytujeme rychlý názor na Gremlin a výčet funkcí Gremlin podporovaných rozhraním API Gremlin.

## <a name="compatible-client-libraries"></a>Kompatibilní klientské knihovny

V následující tabulce jsou uvedené oblíbené ovladače Gremlin, které můžete použít proti Azure Cosmos DB:

| Stáhnout | Zdroj | Začínáme | Podporovaná verze konektoru |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET na GitHubu](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Vytvoření grafu pomocí .NET](create-graph-dotnet.md) | 3.4.0 – RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Vytvoření grafu pomocí jazyka Java](create-graph-java.md) | 3.2.0 + |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript na GitHubu](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Vytvoření grafu pomocí Node. js](create-graph-nodejs.md) | 3.3.4 + |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin – Python na GitHubu](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Vytvoření grafu pomocí Pythonu](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin – PHP na GitHubu](https://github.com/PommeVerte/gremlin-php) | [Vytvoření grafu pomocí PHP](create-graph-php.md) | 3.1.0 |
| [Konzola Gremlin](https://tinkerpop.apache.org/downloads.html) | [TinkerPop docs](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Vytvoření grafu pomocí konzoly Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Podporované objekty grafu
TinkerPop je standard, který pokrývá široké spektrum technologií grafů. Proto má standardní terminologii pro popis funkcí poskytovaných poskytovatelem grafu. Azure Cosmos DB poskytuje trvalou a vysokou souběžnou databázi grafů s možností zápisu, která může být rozdělená na více serverů nebo clusterů. 

Následující tabulka obsahuje seznam funkcí TinkerPop implementovaných pomocí Azure Cosmos DB: 

| Kategorie | Azure Cosmos DB implementace |  Poznámky | 
| --- | --- | --- |
| Funkce grafu | Poskytuje stálost a ConcurrentAccess. Navrženo pro podporu transakcí | Metody počítačů je možné implementovat pomocí konektoru Spark. |
| Funkce proměnných | Podporuje logickou hodnotu, celé číslo, Byte, Double, float, Integer, Long, String. | Podporuje primitivní typy, je kompatibilní se složitými typy prostřednictvím datového modelu. |
| Funkce vrcholu | Podporuje RemoveVertices, MetaProperties, AddVertices, více vlastností, StringIds, UserSuppliedIds, AddProperty, RemoveProperty.  | Podporuje vytváření, úpravy a odstraňování vrcholů. |
| Funkce vlastností vrcholu | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Podporuje vytváření, úpravy a mazání vlastností vrcholu. |
| Funkce Edge | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Podporuje vytváření, úpravy a odstraňování hran. |
| Funkce hraničních vlastností | Vlastnosti, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Podporuje vytváření, úpravy a odstraňování vlastností Edge. |

## <a name="gremlin-wire-format-graphson"></a>Formát Gremlin pro přenos: GraphSON

Azure Cosmos DB používá [Formát GraphSON](http://tinkerpop.apache.org/docs/current/reference/#graphson) při vracení výsledků z operací Gremlin. Azure Cosmos DB aktuálně podporuje verzi "GraphSONv2". GraphSON je standardní formát Gremlin pro reprezentaci vrcholů, hran a vlastností (vlastnosti jedné a více hodnot) pomocí JSON.

Například následující fragment kódu ukazuje reprezentaci GraphSON vrcholu *vráceného klientovi* z Azure Cosmos DB. 

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

Vlastnosti používané GraphSON pro vrcholy jsou popsány níže:

| Vlastnost | Popis | 
| --- | --- | --- |
| `id` | ID vrcholu Musí být jedinečný (v kombinaci s hodnotou `_partition`, pokud je k dispozici). Pokud není zadána žádná hodnota, bude automaticky doplněna identifikátorem GUID. | 
| `label` | Popisek vrcholu Tato vlastnost slouží k popisu typu entity. |
| `type` | Slouží k odlišení vrcholů od jiných dokumentů než grafů. |
| `properties` | Penalta uživatelsky definovaných vlastností přidružených k vrcholu. Každá vlastnost může mít více hodnot. |
| `_partition` | Klíč oddílu vrcholu Používá se pro [dělení grafů](graph-partitioning.md). |
| `outE` | Tato vlastnost obsahuje seznam okrajů z vrcholu. Ukládání informací o sousedství pomocí vrcholu umožňuje rychlé spuštění procházení. Okraje jsou seskupeny podle jejich popisků. |

A hrana obsahuje následující informace, které vám pomůžou s navigací k ostatním částem grafu.

| Vlastnost | Popis |
| --- | --- |
| `id` | ID okraje Musí být jedinečný (v kombinaci s hodnotou `_partition`, pokud je k dispozici). |
| `label` | Popisek okraje Tato vlastnost je volitelná a používá se k popisu typu vztahu. |
| `inV` | Tato vlastnost obsahuje seznam vrcholů pro hranici. Ukládání informací o sousedství s hranou umožňuje rychlé spuštění procházení. Vrcholy jsou seskupeny podle jejich popisků. |
| `properties` | Penalta uživatelsky definovaných vlastností, které jsou přidružené k Edge. Každá vlastnost může mít více hodnot. |

Každá vlastnost může ukládat více hodnot v rámci pole. 

| Vlastnost | Popis |
| --- | --- |
| `value` | Hodnota vlastnosti

## <a name="gremlin-steps"></a>Gremlin kroky
Teď se podíváme na kroky Gremlin podporované Azure Cosmos DB. Úplný odkaz na Gremlin naleznete v tématu [TinkerPop reference](https://tinkerpop.apache.org/docs/3.3.2/reference).

| Krok | Popis | Dokumentace k TinkerPop 3,2 |
| --- | --- | --- |
| `addE` | Přidá okraj mezi dva vrcholy. | [Krok addE](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Přidá vrchol do grafu. | [Krok addV](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Zajišťuje, aby všechny procházení vracely hodnotu. | [a krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Modulátor kroku pro přiřazení proměnné k výstupu kroku | [jako krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | Modulátor kroku použitý s `group` a `order` | [podle kroku](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Vrátí první průchod, který vrátí výsledek. | [Krok COALESCE](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Vrátí konstantní hodnotu. Používá se s `coalesce`| [konstantní krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Vrátí počet z procházení. | [Krok výpočtu](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Vrátí hodnoty s odebranými duplicitami. | [krok odstranění duplicitních dat](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Přenechá hodnoty (vrcholů a hran). | [vynechat krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Vytvoří popis všech operací generovaných spuštěným Gremlin krokem. | [Krok executionProfile](graph-execution-profile.md) |
| `fold` | Slouží jako bariéra, která počítá agregované výsledky.| [Krok skládání](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Seskupí hodnoty na základě zadaných popisků.| [Krok skupiny](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Slouží k filtrování vlastností, vrcholů a hran. Podporuje varianty `hasLabel`, `hasId`, `hasNot` a `has`. | [má krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Vložení hodnot do datového proudu| [Krok vložení](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Slouží k provedení filtru pomocí logického výrazu. | [je krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Slouží k omezení počtu položek v průchodu.| [Krok omezení](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Místní zabalí část procházení podobně jako poddotaz. | [místní krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Slouží k vytvoření negace filtru. | [není krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Vrátí výsledek zadaného přecházení, pokud výsledkem je výsledek, který vrátí jiný volající element. | [volitelný krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Zajistí, aby aspoň jedna z procházení vrátila hodnotu. | [nebo krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Vrátí výsledky v zadaném pořadí řazení. | [pořadí kroků](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Vrátí úplnou cestu procházení. | [krok cesty](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Projektuje vlastnosti jako mapu. | [Krok projektu](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Vrátí vlastnosti pro zadané popisky. | [Krok vlastností](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtruje zadaný rozsah hodnot.| [Rozsah – krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Opakuje krok po zadaném počtu opakování. Používá se pro smyčky. | [opakovat krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Slouží k vzorkování výsledků z průchodu | [Ukázkový krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Používá se k prohledání výsledků z procházení. |  [vybrat krok](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Používá se pro neblokované agregace z průchodu | [Krok úložiště](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Funkce filtrování řetězců. Tato funkce se používá jako predikát pro krok `has()`, aby odpovídal vlastnosti začínající na začátku daného řetězce. | [TextP predikáty](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Funkce filtrování řetězců. Tato funkce se používá jako predikát pro krok `has()`, aby odpovídal vlastnosti s koncem daného řetězce. | [TextP predikáty](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Funkce filtrování řetězců. Tato funkce se používá jako predikát pro krok `has()`, aby odpovídal vlastnosti s obsahem daného řetězce. | [TextP predikáty](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Funkce filtrování řetězců. Tato funkce se používá jako predikát pro krok `has()`, aby odpovídal vlastnosti, která nezačíná zadaným řetězcem. | [TextP predikáty](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Funkce filtrování řetězců. Tato funkce se používá jako predikát pro krok `has()`, aby odpovídal vlastnosti, která nekončí daným řetězcem. | [TextP predikáty](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Funkce filtrování řetězců. Tato funkce se používá jako predikát pro krok `has()`, aby odpovídal vlastnosti, která neobsahuje daný řetězec. | [TextP predikáty](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Agregace cest z vrcholu do stromové struktury | [Krok stromu](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Odvolání iterátoru jako kroku| [Krok odložení](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Sloučit výsledky z více procházení| [Krok sjednocení](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Zahrnuje kroky nutné pro procházení mezi vrcholy a okraji `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, 0 a 1 pro | [kroky vrcholu](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Slouží k filtrování výsledků z procházení. Podporuje operátory `eq`, `neq`, `lt`, `lte`, `gt`, `gte` a `between`.  | [Krok WHERE](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Modul optimalizovaný pro zápis, který poskytuje Azure Cosmos DB, podporuje automatické indexování všech vlastností v rámci vrcholů a hran ve výchozím nastavení. Proto jsou dotazy s filtry, dotazy na rozsah, řazení nebo agregace na jakékoli vlastnosti zpracovány z indexu a efektivně obsluhovány. Další informace o tom, jak funguje indexování v Azure Cosmos DB, najdete v našem dokumentu o [indexování – schéma – nezávislá](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Další kroky
* Začínáme s vytvářením aplikace grafu [pomocí našich sad SDK](create-graph-dotnet.md) 
* Další informace o [podpoře grafů](graph-introduction.md) v Azure Cosmos DB
