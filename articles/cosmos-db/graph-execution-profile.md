---
title: Vyhodnocení svých dotazů s spuštění profilu funkce pro rozhraní Gremlin API služby Azure Cosmos DB
description: Zjistěte, jak odstraňovat potíže a zlepšit vaše dotazy Gremlin použití kroku spuštění profilu.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 2f3967c64e79b2bc7b01b35eff26f5ac0d4e3db4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288603"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Použití kroku spuštění profilu vyhodnotit své dotazy Gremlin

Tento článek poskytuje přehled o tom, jak pomocí kroku spuštění profilu pro databáze grafů Gremlin API služby Azure Cosmos DB. Tento krok obsahuje důležité informace pro řešení potíží a optimalizace dotazů který je kompatibilní s Gremlin dotaz, který jde provést proti účtu Cosmos DB Gremlin API.

Chcete-li použít tento krok, jednoduše přidejte `executionProfile()` volání na konec dotazu Gremlin funkce. **Spustí dotaz Gremlin** a vrátí výsledek operace odpovědi objekt JSON s profilem provádění dotazu.

Příklad:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Po volání `executionProfile()` kroku odpověď bude objekt JSON, který zahrnuje provedený krok Gremlin, celková doba, jakou trvalo a pole operátory prostředí runtime Cosmos DB, jejichž výsledkem příkazu.

> [!NOTE]
> Tato implementace pro profil spuštění není definována specifikací Apache Tinkerpop. Je specifický pro implementaci Azure Cosmos DB Gremlin API.


## <a name="response-example"></a>Příklad odpovědi

Následuje příklad výstupu, který bude vrácen s poznámkami:

> [!NOTE]
> V tomto příkladu je opatřen poznámkou poznámky, které popisují obecnou strukturu odpovědi. Skutečný executionProfile odpověď nebude obsahovat žádné komentáře.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
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
> Krok executionProfile spustí dotazu Gremlin. Jedná se o `addV` nebo `addE`kroky, které bude mít za následek vytvoření a bude potvrzení změn, zadaný v dotazu. V důsledku toho se také účtuje jednotek žádostí generovaných dotazů Gremlin.

## <a name="execution-profile-response-objects"></a>Objekty odpovědi profilu spuštění

Odpověď funkce executionProfile() předá hierarchii objektů JSON s následující strukturou:
  - **Objekt operace gremlin**: Představuje celou operaci Gremlin, který se spustil. Obsahuje následující vlastnosti.
    - `gremlin`: Explicitní Gremlin příkazu, který se spustil.
    - `totalTime`: Čas, v milisekundách, která provedení kroku vzniklé při. 
    - `metrics`: Pole, které obsahuje všechny operátory prostředí runtime Cosmos DB, které byly spuštěny ke splnění dotaz. Tento seznam je seřazen v pořadí spouštění.
    
  - **Operátory prostředí runtime cosmos DB**: Představuje všechny komponenty celá operace Gremlin. Tento seznam je seřazen v pořadí spouštění. Každý objekt obsahuje následující vlastnosti:
    - `name`: Název operátoru. Toto je typ kroku, který byl vyhodnotit a spustit. Další informace v následující tabulce.
    - `time`: Množství času v milisekundách, která trvala daný operátor.
    - `annotations`: Obsahuje další informace, které jsou specifické pro operátor, který se spustil.
    - `annotations.percentTime`: Procento z celkového času, které trvalo provedení konkrétní operátor.
    - `counts`: Počet objektů vrácených z úložné vrstvy tímto operátorem. To je součástí `counts.resultCount` skalární hodnoty v rámci.
    - `storeOps`: Představuje operaci úložiště, která může zahrnovat jeden nebo více oddílů.
    - `storeOps.fanoutFactor`: Představuje počet oddílů, ke kterým přistupuje operaci konkrétní úložiště.
    - `storeOps.count`: Představuje počet výsledků, které vrátila tato operace úložiště.
    - `storeOps.size`: Představuje velikost v bajtech výsledek operace daného úložiště.

Operátor modulu Runtime cosmos DB Gremlin|Popis
---|---
`GetVertices`| Tento krok získává predicated sadu objektů z vrstvy trvalosti. 
`GetEdges`| Tento krok získá hrany, které jsou vedle sadu vrcholů. Tento krok může vést k jedné nebo mnoha operací úložiště.
`GetNeighborVertices`| Tento krok získá vrcholy, které jsou připojené k sadě hran. Okraje obsahovat oddíl klíče a ID jejich zdroj a cíl vrcholů.
`Coalesce`| Tento krok účty pro testování dvě operace vždy, když `coalesce()` provádí se krok Gremlin.
`CartesianProductOperator`| Tento krok vypočítá kartézský součin mezi dvěma datovými sadami. Obvykle provedeny vždy, když predikáty `to()` nebo `from()` se používají.
`ConstantSourceOperator`| Tento krok vypočítá výraz, který se v důsledku vytvořit konstantní hodnotu.
`ProjectOperator`| Tento krok připravuje a serializuje odpověď s použitím výsledek předchozí operace.
`ProjectAggregation`| Tento krok připravuje a serializuje odpovědi pro operaci agregace.

> [!NOTE]
> Tento seznam bude dál aktualizovat, protože se přidají nové operátory.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Příklady o tom, jak analyzovat odpověď profilu spuštění

Následují příklady běžných optimalizace, které mohou být nanese pomocí odpovědi profil spuštění:
  - Skrytá větveného dotazu.
  - Nefiltrované dotazu.

### <a name="blind-fan-out-query-patterns"></a>Skrytá větveného vzory dotazů

Předpokládejme následující odpověď profilu spuštění z **dělené grafu**:

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

Z něj můžete provést následující závěry:
- Dotaz je jediného vyhledání ID, protože příkaz Gremlin odpovídá vzoru `g.V('id')`.
- Obsahovým z `time` metriku, latence tento dotaz zdá se, že se Vysoká, protože jde o [více než 10 ms pro jednu operaci čtení bod](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Když se podíváme do `storeOps` objektu, můžeme vidět, který `fanoutFactor` je `5`, což znamená, že [5 oddíly](https://docs.microsoft.com/azure/cosmos-db/partition-data) získal přístup této operace.

Jako uzavření tuto analýzu můžeme určit, že první dotaz přistupuje více oddílů, než je nezbytné. Můžete to vyřešit tak, že zadáte klíč rozdělení v dotazu jako predikát. To bude mít nižší latenci a nižší náklady na dotazu. Další informace o [dělení grafů](graph-partitioning.md). Více optimální dotazu by `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Vzory nefiltrované dotazů

Porovnejte následující dva spuštění profilu odpovědi. Pro jednoduchost tyto příklady používají jeden graf oddílů.

Tento první dotaz načte všechny vrcholy s popiskem `tweet` a potom získá jejich sousedních vrcholů:

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

Všimněte si, že profil stejný dotaz, ale teď s další filtr `has('lang', 'en')`, prozkoumáte sousedních vrcholů:

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

Tyto dva dotazy dosažení stejného výsledku, první z nich ale budou vyžadovat více jednotek žádostí, protože je potřeba k iteraci původní datové sady větší než dotazování na sousední položky. Můžeme vidět ukazatele tohoto chování při porovnávání z obou odpovědi následující parametry:
- `metrics[0].time` Hodnota je vyšší v první odpověď, což znamená, že trvalo déle, chcete-li vyřešit tento krok.
- `metrics[0].counts.resultsCount` Vyšší i v první odpověď, což znamená, že počáteční pracovní sadu dat byl větší hodnotu.

## <a name="next-steps"></a>Další postup
* Další informace o [podporované funkce Gremlin](gremlin-support.md) ve službě Azure Cosmos DB. 
* Další informace o [rozhraní Gremlin API ve službě Azure Cosmos DB](graph-introduction.md).
