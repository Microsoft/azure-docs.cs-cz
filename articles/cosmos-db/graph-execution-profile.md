---
title: Vyhodnoťte dotazy pomocí funkce profilu spuštění pro Azure Cosmos DB rozhraní Gremlin API
description: Přečtěte si, jak řešit a zdokonalovat dotazy Gremlin pomocí kroku profil spuštění.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: ab5c55105eeb912281f35e3d6094c0c43a76f89a
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915890"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Jak použít krok profilu spuštění k vyhodnocení dotazů Gremlin

Tento článek poskytuje přehled toho, jak použít krok profilu spuštění pro Azure Cosmos DB databáze Gremlin API. Tento krok poskytuje relevantní informace pro řešení potíží a optimalizace dotazů a je kompatibilní s jakýmkoli dotazem Gremlin, který se dá provést na účtu rozhraní Gremlin API pro Cosmos DB.

Chcete-li použít tento krok, stačí `executionProfile()` na konci dotazu Gremlin připojit volání funkce. **Dotaz Gremlin se** spustí a výsledek operace vrátí objekt odpovědi JSON s profilem spuštění dotazu.

Příklad:

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
> Krok executionProfile spustí dotaz Gremlin. To zahrnuje `addV` kroky nebo `addE`, které budou mít za následek vytvoření a budou potvrzovat změny zadané v dotazu. V důsledku toho budou také účtovány jednotky žádostí vygenerované dotazem Gremlin.

## <a name="execution-profile-response-objects"></a>Objekty odezvy profilu spuštění

Odezva funkce executionProfile () bude vracet hierarchii objektů JSON s následující strukturou:
  - **Objekt operace Gremlin**: Představuje úplnou operaci Gremlin, která byla provedena. Obsahuje následující vlastnosti.
    - `gremlin`: Explicitní příkaz Gremlin, který byl proveden.
    - `totalTime`: Čas (v milisekundách), po který se krok uskutečnil. 
    - `metrics`: Pole, které obsahuje všechny operátory modulu runtime Cosmos DB, které byly provedeny pro splnění dotazu. Tento seznam je seřazen v pořadí podle spuštění.
    
  - **Cosmos DB operátory runtime**: Představuje každou součást celé operace Gremlin. Tento seznam je seřazen v pořadí podle spuštění. Každý objekt obsahuje následující vlastnosti:
    - `name`: Název operátora Toto je typ kroku, který byl vyhodnocen a proveden. Další informace najdete v následující tabulce.
    - `time`: Množství času (v milisekundách), které daný operátor trval.
    - `annotations`: Obsahuje další informace, které jsou specifické pro operátor, který se spustil.
    - `annotations.percentTime`: Procentuální podíl celkového času, který trvalo provedení konkrétního operátoru.
    - `counts`: Počet objektů, které byly vráceny z vrstvy úložiště tímto operátorem. Tato hodnota je obsažena `counts.resultCount` v skalární hodnotě v rámci.
    - `storeOps`: Představuje operaci úložiště, která může být rozložená na jeden nebo více oddílů.
    - `storeOps.fanoutFactor`: Představuje počet oddílů, které tato konkrétní operace úložiště získala.
    - `storeOps.count`: Představuje počet výsledků, které tato operace úložiště vrátila.
    - `storeOps.size`: Představuje velikost výsledku dané operace úložiště v bajtech.

Cosmos DB – operátor běhu Gremlin|Popis
---|---
`GetVertices`| Tento krok získá predikátové sady objektů z trvalé vrstvy. 
`GetEdges`| Tento krok získá hrany sousedící se sadou vrcholů. Tento krok může mít za následek jednu nebo více operací úložiště.
`GetNeighborVertices`| Tento krok získá vrcholy, které jsou propojeny se sadou hran. Okraje obsahují klíče oddílu a ID pro jejich zdrojové i cílové vrcholy.
`Coalesce`| Tento krok se používá pro vyhodnocení dvou operací při každém `coalesce()` spuštění Gremlin kroku.
`CartesianProductOperator`| Tento krok vypočítá kartézském produkt mezi dvěma datovými sadami. Obvykle prováděna vždy, když `to()` jsou `from()` použity predikáty nebo.
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

Z **rozděleného grafu**Předpokládejme následující odpověď profilu spuštění:

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
- Dotaz je jednoduché vyhledávání ID, protože příkaz Gremlin se řídí vzorem `g.V('id')`.
- Z `time` hlediska metriky je latence tohoto dotazu vysoká, protože se jedná o [více než 10ms pro jednu operaci čtení z bodu](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Pokud se do `storeOps` objektu podíváme, vidíte, `fanoutFactor` že je `5`to, což znamená, že tato operace získala [5 oddílů](https://docs.microsoft.com/azure/cosmos-db/partition-data) .

V závěru této analýzy můžeme určit, že první dotaz přistupuje k více oddílům, než je potřeba. Dá se to vyřešit zadáním klíče rozdělení do dotazu jako predikátu. To bude mít za následek menší latenci a méně nákladů na dotaz. Přečtěte si další informace o [dělení grafu](graph-partitioning.md). Optimální dotaz by byl `g.V('tt0093640').has('partitionKey', 't1001')`.

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

Všimněte si, že profil stejného dotazu, ale teď s dodatečným filtrem `has('lang', 'en')`, před prozkoumáním sousedících vrcholů:

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
- `metrics[0].time` Hodnota je vyšší v první odpovědi, což znamená, že tento jeden krok trvá déle, než se vyřeší.
- `metrics[0].counts.resultsCount` Hodnota je vyšší i v první reakci, což znamená, že počáteční pracovní datová sada byla větší.

## <a name="next-steps"></a>Další postup
* Přečtěte si o [podporovaných funkcích Gremlin](gremlin-support.md) v Azure Cosmos DB. 
* Přečtěte si další informace o [rozhraní Gremlin API v Azure Cosmos DB](graph-introduction.md).
