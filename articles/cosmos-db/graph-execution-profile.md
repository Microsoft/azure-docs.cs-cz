---
title: Použití profilu spuštění k vyhodnocení dotazů v rozhraní API Azure Cosmos DB Gremlin
description: Zjistěte, jak řešit potíže a vylepšovat gremlinské dotazy pomocí kroku profilu spuštění.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441856"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Vyhodnocování dotazů Gremlin s využitím kroku profilu spuštění

Tento článek obsahuje přehled použití kroku profilu spuštění pro grafové databáze rozhraní Gremlin API služby Azure Cosmos DB. Tento krok poskytuje relevantní informace pro účely řešení potíží a optimalizace dotazů a je kompatibilní se všemi dotazy Gremlin, které je možné spustit pro účet rozhraní Gremlin API služby Cosmos DB.

Chcete-li použít tento krok, jednoduše připojit volání `executionProfile()` funkce na konci dotazu Gremlin. **Gremlin dotaz bude proveden** a výsledek operace vrátí objekt odpovědi JSON s profilem spuštění dotazu.

Například:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Po volání `executionProfile()` kroku bude odpovědí objekt JSON, který zahrnuje provedený krok Gremlin, celkovou dobu trvalou a pole operátorů runtime Cosmos DB, které výsledkem příkazu.

> [!NOTE]
> Tato implementace pro profil spuštění není definována ve specifikaci Apache Tinkerpop. Je specifické pro implementaci rozhraní API Azure Cosmos DB Gremlin.


## <a name="response-example"></a>Příklad odpovědi

Následuje anotovaný příklad výstupu, který bude vrácen:

> [!NOTE]
> Tento příklad je anotován s komentáři, které vysvětlují obecnou strukturu odpovědi. Skutečné spuštěníProfil odpověď nebude obsahovat žádné komentáře.

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
> SpuštěníProfil krok spustí dotaz Gremlin. To zahrnuje `addV` `addE`nebo kroky, které budou mít za následek vytvoření a potvrdí změny zadané v dotazu. V důsledku toho budou účtovány také jednotky požadavků generované dotazem Gremlin.

## <a name="execution-profile-response-objects"></a>Objekty odpovědi profilu spuštění

Odpověď funkce executionProfile() přinese hierarchii objektů JSON s následující strukturou:
  - **Objekt operace Gremlin**: Představuje celou operaci Gremlin, která byla provedena. Obsahuje následující vlastnosti.
    - `gremlin`: Explicitní Gremlin prohlášení, které bylo provedeno.
    - `totalTime`: Čas v milisekundách, že provádění kroku vznikly v. 
    - `metrics`: Pole, které obsahuje každý z operátorů runtime Cosmos DB, které byly provedeny ke splnění dotazu. Tento seznam je seřazen v pořadí provádění.
    
  - **Operátory runtime Cosmos DB**: Představuje každou součást celé operace Gremlin. Tento seznam je seřazen v pořadí provádění. Každý objekt obsahuje následující vlastnosti:
    - `name`: Název operátora. Toto je typ kroku, který byl vyhodnocen a proveden. Přečtěte si více v tabulce níže.
    - `time`: Doba v milisekundách, kterou daný operátor zabral.
    - `annotations`: Obsahuje další informace, specifické pro operátor, který byl proveden.
    - `annotations.percentTime`: Procento z celkové doby, kterou trvalo provedení konkrétního operátora.
    - `counts`: Počet objektů, které byly vráceny z vrstvy úložiště tímto operátorem. To je obsaženo `counts.resultCount` v skalární hodnotě uvnitř.
    - `storeOps`: Představuje operaci úložiště, která může span jeden nebo více oddílů.
    - `storeOps.fanoutFactor`: Představuje počet oddílů, které tato konkrétní operace úložiště přistupovat.
    - `storeOps.count`: Představuje počet výsledků, které tato operace úložiště vrácena.
    - `storeOps.size`: Představuje velikost v bajtů výsledek dané operace úložiště.

Operátor runtime Cosmos DB Gremlin|Popis
---|---
`GetVertices`| Tento krok získá indikovanou sadu objektů z vrstvy trvalosti. 
`GetEdges`| Tento krok získá hrany, které sousedí se sadou vrcholů. Tento krok může mít za následek jednu nebo více operací úložiště.
`GetNeighborVertices`| Tento krok získá vrcholy, které jsou připojeny k sadě hran. Hrany obsahují klíče oddílu a ID jejich zdrojových i cílových vrcholů.
`Coalesce`| Tento krok účty pro vyhodnocení dvou `coalesce()` operací při každém spuštění kroku Gremlin.
`CartesianProductOperator`| Tento krok vypočítá kartézský produkt mezi dvěma datovými sadami. Obvykle se provádí vždy, `to()` když `from()` predikuje nebo jsou používány.
`ConstantSourceOperator`| Tento krok vypočítá výraz k vytvoření konstantní hodnoty jako výsledek.
`ProjectOperator`| Tento krok připraví a serializuje odpověď pomocí výsledku předchozích operací.
`ProjectAggregation`| Tento krok připraví a serializuje odpověď pro agregační operaci.

> [!NOTE]
> Tento seznam bude i nadále aktualizován jako nové operátory jsou přidány.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Příklady analýzy odpovědi profilu spuštění

Následují příklady běžných optimalizací, které mohou být znatné pomocí odpovědi profilu spuštění:
  - Slepý fan-out dotaz.
  - Nefiltrovaný dotaz.

### <a name="blind-fan-out-query-patterns"></a>Vzory dotazů na slepé fanoušky

Předpokládejme následující odpověď profilu spuštění z **děleného grafu**:

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

Z něj lze vyvozovat následující závěry:
- Dotaz je jedno vyhledávání ID, protože příkaz Gremlin `g.V('id')`následuje za vzorem .
- Soudě `time` podle metriky se latence tohoto dotazu zdá být vysoká, protože je [více než 10 ms pro operaci čtení jednoho bodu](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Podíváme-li `storeOps` se do objektu, `fanoutFactor` `5`vidíme, že je , což znamená, že [5 oddíly](https://docs.microsoft.com/azure/cosmos-db/partition-data) byly přístupné touto operací.

Jako závěr této analýzy můžeme určit, že první dotaz je přístup k více oddílů, než je nutné. To lze vyřešit zadáním klíče dělení v dotazu jako predikátu. To povede k menší latenci a nižší náklady na dotaz. Další informace o [dělení grafů](graph-partitioning.md). Optimálnější `g.V('tt0093640').has('partitionKey', 't1001')`dotaz by byl .

### <a name="unfiltered-query-patterns"></a>Nefiltrované vzorky dotazů

Porovnejte následující dvě odpovědi profilu spuštění. Pro jednoduchost tyto příklady používají jeden graf rozdělený na oddíly.

Tento první dotaz načte všechny vrcholy s popiskem `tweet` a potom získá jejich sousední vrcholy:

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

Všimněte si profilu stejného dotazu, ale `has('lang', 'en')`nyní s dalším filtrem , před prozkoumáním sousedních vrcholů:

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

Tyto dva dotazy dosáhly stejného výsledku, ale první z nich bude vyžadovat více jednotek požadavků, protože je třeba iterát větší počáteční datové sady před dotazem na sousední položky. Můžeme vidět ukazatele tohoto chování při porovnávání následujících parametrů z obou odpovědí:
- Hodnota `metrics[0].time` je vyšší v první odpovědi, což znamená, že tento jediný krok trvalo déle vyřešit.
- Hodnota `metrics[0].counts.resultsCount` je vyšší také v první odpovědi, což znamená, že počáteční pracovní datová sada byla větší.

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [podporovaných funkcích Gremlin](gremlin-support.md) v Azure Cosmos DB. 
* Další informace o [rozhraní Gremlin API v Azure Cosmos DB](graph-introduction.md).
