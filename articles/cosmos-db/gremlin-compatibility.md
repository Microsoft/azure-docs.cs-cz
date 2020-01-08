---
title: Azure Cosmos DB kompatibilita Gremlin s funkcemi TinkerPop
description: Problémy s kompatibilitou modulu Graph Documentation Engine
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 581bc813ca27067b1f27ab9866a45df3084dbbcc
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644728"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB kompatibilita Gremlin
Modul Azure Cosmos DB Graph úzce sleduje specifikace kroků procházení [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ale existují i rozdíly.

## <a name="behavior-differences"></a>Rozdíly v chování

* Modul graphu pro Azure Cosmos DB pracuje ***s prvními*** průchody, zatímco je TinkerPop Gremlin na hloubku. Toto chování dosahuje lepšího výkonu v horizontálním škálovatelném systému, jako je Cosmos DB. 

## <a name="unsupported-features"></a>Nepodporované funkce

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** je na konkrétním programovacím jazyku nezávislá specifikace pro procházení grafů. Cosmos DB Graph ho ještě nepodporuje. Použijte `GremlinClient.SubmitAsync()` a předejte průchod jako textový řetězec.

* mohutnost ***`property(set, 'xyz', 1)`*** sady se ještě nepodporuje. Místo toho použijte `property(list, 'xyz', 1)`. Další informace najdete v tématu [vlastnosti vrcholu pomocí TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** umožňuje dotazování grafů pomocí deklarativního porovnávání vzorů. Tato možnost není k dispozici.

* ***Objekty jako vlastnosti*** na vrcholech nebo hranách nejsou podporovány. Vlastnosti můžou být pouze primitivní typy nebo pole.

* ***Řazení podle vlastností pole*** `order().by(<array property>)` není podporováno. Podporuje se řazení pouze podle primitivních typů.

* ***Neprimitivní typy JSON*** nejsou podporovány. Použijte `string`typy `false`, `number`nebo `true`/. `null` hodnoty nejsou podporovány. 

* Serializátor ***GraphSONv3*** se v tuto chvíli nepodporuje. V konfiguraci připojení použijte třídy serializátorů, čtecí modul a modul pro zápis `GraphSONv2`.

* **Výrazy lambda a funkce** nejsou aktuálně podporovány. To zahrnuje `.map{<expression>}`, `.by{<expression>}`a `.filter{<expression>}` funkce. Další informace a informace o tom, jak je přepsat pomocí Gremlin kroků, najdete v [poznámce pro výrazy lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Transakce*** nejsou podporovány z důvodu distribuované povahy systému.  Nakonfigurujte vhodný model konzistence v účtu Gremlin na číst vlastní zápisy a pomocí optimistické souběžnosti vyřešte konfliktní zápisy.

## <a name="next-steps"></a>Další kroky
* Přejděte na stránku [Cosmos DB hlas uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db) , kde můžete sdílet zpětnou vazbu a pomáhat týmu soustředit se na funkce, které jsou pro vás důležité.
