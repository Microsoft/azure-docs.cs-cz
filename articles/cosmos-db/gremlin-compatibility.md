---
title: Azure Cosmos DB kompatibilita Gremlin s funkcemi TinkerPop
description: Problémy s kompatibilitou modulu Graph Documentation Engine
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 7257246f618e3028534f3ebd60eaf6f94a3a4720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092503"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB kompatibilita Gremlin
Modul Azure Cosmos DB Graph úzce sleduje specifikace kroků procházení [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ale existují rozdíly v implementaci, které jsou specifické pro Azure Cosmos DB. Seznam podporovaných kroků Gremlin najdete v článku [Podpora přenosových protokolů GREMLIN API](gremlin-support.md) .

## <a name="behavior-differences"></a>Rozdíly v chování

* Modul graphu pro Azure Cosmos DB pracuje ***s prvními*** průchody, zatímco je TinkerPop Gremlin na hloubku. Toto chování dosahuje lepšího výkonu v horizontálním škálovatelném systému, jako je Cosmos DB. 

## <a name="unsupported-features"></a>Nepodporované funkce

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** je na konkrétním programovacím jazyku nezávislá specifikace pro procházení grafů. Cosmos DB Graph ho ještě nepodporuje. Použijte `GremlinClient.SubmitAsync()` a předejte procházení jako textový řetězec.

* ***`property(set, 'xyz', 1)`*** sada mohutnosti se v současné době nepodporuje. Místo toho použijte `property(list, 'xyz', 1)`. Další informace najdete v tématu [vlastnosti vrcholu pomocí TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* *** `match()` Krok*** není aktuálně k dispozici. Tento krok poskytuje možnosti deklarativního dotazování.

* ***Objekty jako vlastnosti*** na vrcholech nebo hranách nejsou podporovány. Vlastnosti můžou být pouze primitivní typy nebo pole.

* ***Řazení podle vlastností pole*** `order().by(<array property>)` není podporováno. Podporuje se řazení pouze podle primitivních typů.

* ***Neprimitivní typy JSON*** nejsou podporovány. Použijte `string` `number` typy, nebo `true` / `false` . `null` hodnoty nejsou podporovány. 

* Serializátor ***GraphSONv3*** se v tuto chvíli nepodporuje. `GraphSONv2`V konfiguraci připojení použijte třídy serializátoru, čtecího zařízení a zapisovače. Výsledky vracené rozhraním API Azure Cosmos DB Gremlin nemají stejný formát jako formát GraphSON. 

* **Výrazy lambda a funkce** nejsou aktuálně podporovány. To zahrnuje `.map{<expression>}` `.by{<expression>}` funkce, a `.filter{<expression>}` . Další informace a informace o tom, jak je přepsat pomocí Gremlin kroků, najdete v [poznámce pro výrazy lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Transakce*** nejsou podporovány z důvodu distribuované povahy systému.  Nakonfigurujte vhodný model konzistence v účtu Gremlin na číst vlastní zápisy a pomocí optimistické souběžnosti vyřešte konfliktní zápisy.

## <a name="known-limitations"></a>Známá omezení

* **Využití indexu pro dotazy Gremlin pomocí `.V()` kroků středního přecházení**: v současné době pouze první `.V()` volání průchodu použije index k vyřešení všech filtrů nebo predikátů, které jsou k nim připojeny. Následná volání nebudou vyhledávat v indexu, což může zvýšit latenci a náklady na dotaz.
    
    Předpokládá se výchozí indexování. typický dotaz Read Gremlin, který začíná `.V()` krokem, by v rámci svých připojených kroků filtrování používal parametry, jako je `.has()` nebo `.where()` pro optimalizaci nákladů a výkonu dotazu. Například:

    ```java
    g.V().has('category', 'A')
    ```

    Pokud `.V()` je však v dotazu Gremlin obsažen více než jeden krok, nemusí být řešení dat pro dotaz optimální. Jako příklad proveďte následující dotaz:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    Tento dotaz vrátí dvě skupiny vrcholů na základě své vlastnosti s názvem `category` . V takovém případě pouze první volání `g.V().has('category', 'A')` provede použití indexu k vyřešení vrcholů na základě hodnot jejich vlastností.

    Alternativním řešením pro tento dotaz je použití kroků pro procházení, jako jsou `.map()` a `union()` . Toto je exemplified níže:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    Výkon dotazů můžete zkontrolovat pomocí [ `executionProfile()` kroku Gremlin](graph-execution-profile.md).

## <a name="next-steps"></a>Další kroky
* Přejděte na stránku [Cosmos DB hlas uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db) , kde můžete sdílet zpětnou vazbu a pomáhat týmu soustředit se na funkce, které jsou pro vás důležité.
