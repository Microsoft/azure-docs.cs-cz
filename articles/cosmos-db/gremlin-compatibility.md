---
title: Azure Cosmos DB kompatibilita Gremlin s funkcemi TinkerPop
description: Problémy s kompatibilitou modulu Graph Documentation Engine
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81449880"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB kompatibilita Gremlin
Modul Azure Cosmos DB Graph úzce sleduje specifikace kroků procházení [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ale existují i rozdíly.

## <a name="behavior-differences"></a>Rozdíly v chování

* Modul graphu pro Azure Cosmos DB pracuje ***s prvními*** průchody, zatímco je TinkerPop Gremlin na hloubku. Toto chování dosahuje lepšího výkonu v horizontálním škálovatelném systému, jako je Cosmos DB. 

## <a name="unsupported-features"></a>Nepodporované funkce

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** je na konkrétním programovacím jazyku nezávislá specifikace pro procházení grafů. Cosmos DB Graph ho ještě nepodporuje. Použijte `GremlinClient.SubmitAsync()` a předejte procházení jako textový řetězec.

* ***`property(set, 'xyz', 1)`*** sada mohutnosti se v současné době nepodporuje. Místo toho použijte `property(list, 'xyz', 1)`. Další informace najdete v tématu [vlastnosti vrcholu pomocí TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** umožňuje dotazování grafů pomocí deklarativního porovnávání vzorů. Tato možnost není k dispozici.

* ***Objekty jako vlastnosti*** na vrcholech nebo hranách nejsou podporovány. Vlastnosti můžou být pouze primitivní typy nebo pole.

* ***Řazení podle vlastností*** `order().by(<array property>)` pole se nepodporuje. Podporuje se řazení pouze podle primitivních typů.

* ***Neprimitivní typy JSON*** nejsou podporovány. Použijte `string`typy `number`, nebo `true` / `false` . `null`hodnoty nejsou podporovány. 

* Serializátor ***GraphSONv3*** se v tuto chvíli nepodporuje. V `GraphSONv2` konfiguraci připojení použijte třídy serializátoru, čtecího zařízení a zapisovače. Výsledky vracené rozhraním API Azure Cosmos DB Gremlin nemají stejný formát jako formát GraphSON. 

* **Výrazy lambda a funkce** nejsou aktuálně podporovány. To zahrnuje `.filter{<expression>}` funkce `.map{<expression>}`, `.by{<expression>}`a. Další informace a informace o tom, jak je přepsat pomocí Gremlin kroků, najdete v [poznámce pro výrazy lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Transakce*** nejsou podporovány z důvodu distribuované povahy systému.  Nakonfigurujte vhodný model konzistence v účtu Gremlin na číst vlastní zápisy a pomocí optimistické souběžnosti vyřešte konfliktní zápisy.

## <a name="next-steps"></a>Další kroky
* Přejděte na stránku [Cosmos DB hlas uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db) , kde můžete sdílet zpětnou vazbu a pomáhat týmu soustředit se na funkce, které jsou pro vás důležité.
