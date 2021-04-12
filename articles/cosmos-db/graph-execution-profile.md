---
title: Použití profilu spuštění k vyhodnocení dotazů v rozhraní Azure Cosmos DB API Gremlin
description: Přečtěte si, jak řešit a zdokonalovat dotazy Gremlin pomocí kroku profil spuštění.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 03/27/2019
ms.author: chrande
ms.openlocfilehash: 18cefb1dd80368a8ccdad9f6f3ffc30881a8a889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087481"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Vyhodnocování dotazů Gremlin s využitím kroku profilu spuštění
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Tento článek obsahuje přehled použití kroku profilu spuštění pro grafové databáze rozhraní Gremlin API služby Azure Cosmos DB. Tento krok poskytuje relevantní informace pro účely řešení potíží a optimalizace dotazů a je kompatibilní se všemi dotazy Gremlin, které je možné spustit pro účet rozhraní Gremlin API služby Cosmos DB.

Chcete-li použít tento krok, stačí na `executionProfile()` konci dotazu Gremlin připojit volání funkce. **Dotaz Gremlin se** spustí a výsledek operace vrátí objekt odpovědi JSON s profilem spuštění dotazu.

Například:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Po volání `executionProfile()` kroku bude odpověď objekt JSON, který obsahuje spuštěný krok Gremlin, celkový čas, který trval, a pole operátorů runtime Cosmos DB, jejichž výsledkem byl příkaz.

> [!NOTE]
> Tato implementace profilu spuštění není definovaná ve specifikaci Apache Tinkerpop. Je specifická pro Azure Cosmos DB implementaci rozhraní API Gremlin.


## <a name="response-example"></a>Příklad odpovědi

V následujícím příkladu je zobrazený příklad výstupu s poznámkami, který se vrátí:

> [!NOTE]
> V tomto příkladu jsou poznámky, které vysvětlují obecnou strukturu odpovědi. Skutečná odpověď executionProfile neobsahuje žádné komentáře.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> Krok executionProfile spustí dotaz Gremlin. To zahrnuje `addV` kroky nebo `addE` , které budou mít za následek vytvoření a budou potvrzovat změny zadané v dotazu. V důsledku toho budou také účtovány jednotky žádostí vygenerované dotazem Gremlin.

## <a name="execution-profile-response-objects"></a>Objekty odezvy profilu spuštění

Odezva funkce executionProfile () bude vracet hierarchii objektů JSON s následující strukturou:
  - **Objekt operace Gremlin**: představuje celou spuštěnou operaci Gremlin. Obsahuje následující vlastnosti.
    - `gremlin`: Explicitní příkaz Gremlin, který byl proveden.
    - `totalTime`: Čas (v milisekundách), po který se krok uskutečnil. 
    - `metrics`: Pole obsahující všechny operátory modulu runtime Cosmos DB, které byly provedeny pro splnění dotazu. Tento seznam je seřazen v pořadí podle spuštění.
    
  - **Cosmos DB operátory runtime**: představují všechny součásti celé operace Gremlin. Tento seznam je seřazen v pořadí podle spuštění. Každý objekt obsahuje následující vlastnosti:
    - `name`: Název operátoru. Toto je typ kroku, který byl vyhodnocen a proveden. Další informace najdete v následující tabulce.
    - `time`: Množství času v milisekundách, které daný operátor trval.
    - `annotations`: Obsahuje další informace, které jsou specifické pro operátor, který byl proveden.
    - `annotations.percentTime`: Procento celkové doby, kterou trvalo spuštění konkrétního operátoru.
    - `counts`: Počet objektů vrácených z vrstvy úložiště tímto operátorem. Tato hodnota je obsažena v `counts.resultCount` skalární hodnotě v rámci.
    - `storeOps`: Představuje operaci úložiště, která může být rozložená na jeden nebo více oddílů.
    - `storeOps.fanoutFactor`: Představuje počet oddílů, které tato konkrétní operace úložiště získala.
    - `storeOps.count`: Představuje počet výsledků, které tato operace úložiště vrátila.
    - `storeOps.size`: Představuje velikost výsledku dané operace úložiště v bajtech.

Cosmos DB – operátor běhu Gremlin|Description
---|---
`GetVertices`| Tento krok získá predikátové sady objektů z trvalé vrstvy. 
`GetEdges`| Tento krok získá hrany sousedící se sadou vrcholů. Tento krok může mít za následek jednu nebo více operací úložiště.
`GetNeighborVertices`| Tento krok získá vrcholy, které jsou propojeny se sadou hran. Okraje obsahují klíče oddílu a ID pro jejich zdrojové i cílové vrcholy.
`Coalesce`| Tento krok se používá pro vyhodnocení dvou operací při každém `coalesce()` spuštění Gremlin kroku.
`CartesianProductOperator`| Tento krok vypočítá kartézském produkt mezi dvěma datovými sadami. Obvykle prováděna vždy, když `to()` jsou použity predikáty nebo `from()` .
`ConstantSourceOperator`| Tento krok vypočítá výraz pro vytvoření konstantní hodnoty v důsledku.
`ProjectOperator`| Tento krok připraví a zaserializace odpověď pomocí výsledku předchozích operací.
`ProjectAggregation`| Tento krok připraví a serializace odpověď na agregační operaci.

> [!NOTE]
> Tento seznam bude nadále aktualizován, protože jsou přidány nové operátory.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Příklady, jak analyzovat odpověď profilu spuštění

Následují příklady běžných optimalizací, které je možné Spotted pomocí odpovědi profilu spuštění:
  - Neslepý dotaz na ventilátor
  - Nefiltrovaný dotaz.

### <a name="blind-fan-out-query-patterns"></a>Vzory dotazů na nevidomé ventilátory

Z **rozděleného grafu** Předpokládejme následující odpověď profilu spuštění:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Z nich je možné provést následující závěry:
- Dotaz je jednoduché vyhledávání ID, protože příkaz Gremlin se řídí vzorem `g.V('id')` .
- Z hlediska `time` metriky je latence tohoto dotazu vysoká, protože se jedná o [více než 10ms pro jednu operaci čtení z bodu](./introduction.md#guaranteed-speed-at-any-scale).
- Pokud se do objektu podíváme, vidíte, `storeOps` že `fanoutFactor` je to `5` , což znamená, že tato operace získala [5 oddílů](./partitioning-overview.md) .

V závěru této analýzy můžeme určit, že první dotaz přistupuje k více oddílům, než je potřeba. Dá se to vyřešit zadáním klíče rozdělení do dotazu jako predikátu. To bude mít za následek menší latenci a méně nákladů na dotaz. Přečtěte si další informace o [dělení grafu](graph-partitioning.md). Optimální dotaz by byl `g.V('tt0093640').has('partitionKey', 't1001')` .

### <a name="unfiltered-query-patterns"></a>Nefiltrované vzory dotazů

Porovnejte následující dva odpovědi profilu spuštění. Pro zjednodušení tyto příklady používají jeden graf s oddíly.

Tento první dotaz načte všechny vrcholy s popiskem `tweet` a pak získá jejich sousední vrcholy:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Všimněte si, že profil stejného dotazu, ale teď s dodatečným filtrem, `has('lang', 'en')` před prozkoumáním sousedících vrcholů:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Tyto dva dotazy dosáhly stejného výsledku, ale první z nich bude vyžadovat více jednotek požadavků, protože před dotazem sousedících položek je potřeba iterovat větší počáteční datovou sadu. Indikátory tohoto chování můžeme zobrazit při porovnávání následujících parametrů z obou odpovědí:
- `metrics[0].time`Hodnota je vyšší v první odpovědi, což znamená, že tento jeden krok trvá déle, než se vyřeší.
- `metrics[0].counts.resultsCount`Hodnota je vyšší i v první reakci, což znamená, že počáteční pracovní datová sada byla větší.

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [podporovaných funkcích Gremlin](gremlin-support.md) v Azure Cosmos DB. 
* Přečtěte si další informace o [rozhraní Gremlin API v Azure Cosmos DB](graph-introduction.md).