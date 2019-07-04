---
title: Podpora služby Azure Cosmos DB Gremlin
description: Přečtěte si o jazyce Gremlin od společnosti Apache TinkerPop. Dozvíte se, které funkce a kroky jsou dostupné ve službě Azure Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: db263c1c7f0a8b87b315c5aa6da31336229c9643
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502730"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Podpora grafu Gremlin ve službě Azure Cosmos DB
Azure Cosmos DB podporuje [Apache Tinkerpop](https://tinkerpop.apache.org) grafu procházení jazyka, označované jako [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). Pomocí jazyka Gremlin můžete vytvářet grafové entity (vrcholy a okraje), upravovat vlastnosti v rámci těchto entit, provádět dotazy a přechody a odstraňovat entity. 

V tomto článku jsme poskytují rychlý návod Gremlin a výčet Gremlin funkce, které jsou podporovány pomocí rozhraní Gremlin API nevztahují.

## <a name="compatible-client-libraries"></a>Kompatibilní klientské knihovny

Následující tabulka ukazuje oblíbené ovladače Gremlin, které můžete použít vůči Azure Cosmos DB:

| Ke stažení | source | Začínáme | Podporovaná verze konektoru |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET na GitHubu](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Vytvoření grafu pomocí jazyka .NET](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Vytvoření grafu pomocí jazyka Java](create-graph-java.md) | 3.2.0 nebo novější |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript na GitHubu](https://github.com/jbmusso/gremlin-javascript) | [Vytvoření grafu pomocí jazyka Node.js](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python na GitHubu](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Vytvoření grafu pomocí jazyka Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP na GitHubu](https://github.com/PommeVerte/gremlin-php) | [Vytvoření grafu pomocí jazyka PHP](create-graph-php.md) | 3.1.0 |
| [Konzola Gremlin](https://tinkerpop.apache.org/downloads.html) | [Dokumentace k TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Vytvoření grafu pomocí konzoly Gremlin](create-graph-gremlin-console.md) | 3.2.0 nebo novější |

## <a name="supported-graph-objects"></a>Podporované objekty grafu
TinkerPop je standard, který zahrnuje širokou řadu grafových technologií. Proto má standardní terminologii popisující, které funkce poskytovatel grafu nabízí. Azure Cosmos DB poskytuje trvalou grafovou databázi s možností zápisu a vysokou souběžností, kterou je možné rozdělit na více serverů nebo clusterů. 

V následující tabulce najdete přehled funkcí TinkerPop, které Azure Cosmos DB implementuje: 

| Kategorie | Implementace služby Azure Cosmos DB |  Poznámky | 
| --- | --- | --- |
| Funkce grafů | Poskytuje trvalost a souběžný přístup. Navrženo s podporou transakcí. | Počítačové metody je možné implementovat prostřednictvím konektoru Spark. |
| Funkce proměnných | Podporuje datové typy Boolean, Integer, Byte, Double, Float, Integer, Long, String. | Podporuje primitivní typy, prostřednictvím datového modelu je kompatibilní s komplexními typy. |
| Funkce vrcholů | Podporuje RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty.  | Podporuje vytváření, úpravy a odstraňování vrcholů. |
| Funkce vlastností vrcholů | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Podporuje vytváření, úpravy a odstraňování vlastností vrcholů. |
| Funkce okrajů | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Podporuje vytváření, úpravy a odstraňování okrajů. |
| Funkce vlastností okrajů | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Podporuje vytváření, úpravy a odstraňování vlastností okrajů. |

## <a name="gremlin-wire-format-graphson"></a>Gremlin přenosový formát: GraphSON

Při vracení výsledků z operací Gremlinu služba Azure Cosmos DB používá [formát GraphSON](https://tinkerpop.apache.org/docs/3.3.2/reference/#graphson-reader-writer). GraphSON je standardní formát jazyka Gremlin pro reprezentaci vrcholů, okrajů a vlastností (vlastností s jednou i více hodnotami) pomocí formátu JSON. 

Následující fragment kódu například zobrazuje reprezentaci vrcholu *vráceného klientovi* ze služby Azure Cosmos DB ve formátu GraphSON. 

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

Vlastnosti používané ve GraphSON pro vrcholy, jsou popsané níže:

| Vlastnost | Popis | 
| --- | --- | --- |
| `id` | ID vrcholu. Musí být jedinečné (v kombinaci s hodnotou `_partition` Pokud je k dispozici). Pokud se nezadá žádná hodnota, se budou automaticky získávané s identifikátorem GUID | 
| `label` | Popisek vrcholu. Tato vlastnost se používá k popisu typu entity. |
| `type` | Slouží k odlišení vrcholů od jiných než grafových dokumentů. |
| `properties` | Sada uživatelem definovaných vlastností přidružených k vrcholu. Každá vlastnost může mít více hodnot. |
| `_partition` | Klíč oddílu vrcholu. Používá pro [dělení grafů](graph-partitioning.md). |
| `outE` | Tato vlastnost obsahuje seznam si hrany z vrcholu. Ukládání informací o sousedství spolu s vrcholem umožňuje rychlé procházení. Hrany jsou seskupeny podle svých popisků. |

Hrana obsahuje následující informace, které usnadňují navigaci do ostatních částí grafu.

| Vlastnost | Popis |
| --- | --- |
| `id` | ID okraje. Musí být jedinečné (v kombinaci s hodnotou `_partition` Pokud je k dispozici) |
| `label` | Popisek okraje. Tato vlastnost je volitelná a slouží k popisu typu vztahu. |
| `inV` | Tato vlastnost obsahuje seznam v vrcholů okraj. Ukládání informací o sousedství spolu s okraj umožňuje rychlé procházení. Vrcholy jsou seskupeny podle svých popisků. |
| `properties` | Sada uživatelem definovaných vlastností přidružených k okraji. Každá vlastnost může mít více hodnot. |

Každá vlastnost může ukládat více hodnot v rámci pole. 

| Vlastnost | Popis |
| --- | --- |
| `value` | Hodnota vlastnosti.

## <a name="gremlin-steps"></a>Kroky v jazyce Gremlin
Nyní se podívejme na kroky v jazyce Gremlin, které Azure Cosmos DB podporuje. Úplné referenční informace o jazyce Gremlin najdete v [referenčních materiálech ke standardu TinkerPop](https://tinkerpop.apache.org/docs/3.3.2/reference).

| Krok | Popis | Dokumentace TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Přidá okraj mezi dva vrcholy. | [addE step](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Přidá do grafu vrchol. | [addV step](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Zajišťuje, že všechna procházení vrátí hodnotu. | [and step](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Modulátor kroku pro přiřazení proměnné k výstupu kroku. | [as step](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | Modulátor kroku používaný s krokem `group` a `order`. | [by step](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Vrátí první procházení, které vrátí výsledek. | [coalesce step](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Vrátí konstantní hodnotu. Používá se s krokem `coalesce`.| [constant step](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Vrátí počet procházení. | [count step](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Vrátí hodnoty s odebranými duplicitními objekty. | [dedup step](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Zahodí hodnoty (vrchol/hrana). | [drop step](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Vytvoří popis všech operací generovaných provedený krok Gremlin | [Krok executionProfile](graph-execution-profile.md) |
| `fold` | Slouží jako bariéra, která vypočítá agregaci výsledků.| [fold step](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Seskupí hodnoty na základě zadaných popisků.| [group step](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Slouží k filtrování vlastností, vrcholů a okrajů. Podporuje varianty `hasLabel`, `hasId`, `hasNot` a `has`. | [has step](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Vloží hodnoty do streamu.| [inject step](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Slouží k filtrování pomocí logického výrazu. | [is step](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Slouží k omezení počtu položek v procházení.| [limit step](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Místně zabalí oddíl procházení podobně jako u vnořeného dotazu. | [local step](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Slouží k vytvoření negace filtru. | [not step](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Vrátí výsledek zadaného procházení, pokud vrací výsledek, jinak vrátí volající element. | [optional step](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Zajišťuje, že alespoň jedno procházení vrátí hodnotu. | [or step](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Vrátí výsledky v zadaném pořadí řazení. | [order step](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Vrátí úplnou cestu procházení. | [path step](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Zobrazí vlastnosti jako mapu. | [project step](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Vrátí vlastnosti zadaných popisků. | [properties step](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Vyfiltruje zadaný rozsah hodnot.| [range step](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Opakuje krok po zadaný počet opakování. Slouží k vytváření cyklů. | [repeat step](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Slouží k zobrazení ukázkových výsledků z procházení. | [sample step](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Slouží k zobrazení výsledků z procházení. |  [select step](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Slouží k zobrazení neblokujících agregací z procházení. | [store step](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Řetězec funkce filtrování. Tato funkce se používá jako predikát pro `has()` krok k přiřazení vlastnosti se začátkem daný řetězec | [Predikáty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Řetězec funkce filtrování. Tato funkce se používá jako predikát pro `has()` krok k přiřazení vlastnosti s ukončení zadaného řetězce | [Predikáty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Řetězec funkce filtrování. Tato funkce se používá jako predikát pro `has()` krok k přiřazení vlastnosti s obsahem zadaného řetězce | [Predikáty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Řetězec funkce filtrování. Tato funkce se používá jako predikát pro `has()` krok tak, aby odpovídaly vlastnost, která nezačíná daným řetězcem. | [Predikáty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Řetězec funkce filtrování. Tato funkce se používá jako predikát pro `has()` krok tak, aby odpovídaly vlastnost, která ale nekončí daným řetězcem. | [Predikáty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Řetězec funkce filtrování. Tato funkce se používá jako predikát pro `has()` krok tak, aby odpovídaly vlastnost, která neobsahuje daný řetězec | [Predikáty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Agreguje cesty z vrcholu do stromu. | [tree step](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Rozbalí iterátor jako krok.| [unfold step](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Sloučí výsledky z více procházení.| [union step](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Zahrnuje kroky nutné pro procházení mezi vrcholy a okraji: `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` a `otherV`. | [vertex steps](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Slouží k filtrování výsledků z procházení. Podporuje operátory `eq`, `neq`, `lt`, `lte`, `gt`, `gte` a `between`.  | [where step](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Modul optimalizovaný pro zápis, který Azure Cosmos DB poskytuje, podporuje ve výchozím nastavení automatické indexování všech vlastností v rámci vrcholů a okrajů. Proto se dotazy s filtry, rozsahové dotazy, řazení nebo agregace u všech vlastností zpracovávají z indexu a efektivně předávají. Další informace o tom, jak funguje indexování ve službě Azure Cosmos DB, najdete v našem dokumentu, který se věnuje [indexování bez schémat](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Další postup
* Pusťte se do vytváření grafové aplikace [pomocí našich sad SDK](create-graph-dotnet.md). 
* Přečtěte si další informace o [podpoře grafu](graph-introduction.md) ve službě Azure Cosmos DB.
