---
title: Kompatibilita azure cosmos DB Gremlin s funkcemi TinkerPop
description: Referenční dokumentace Problémy s kompatibilitou modulu modulu
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 581bc813ca27067b1f27ab9866a45df3084dbbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644728"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Kompatibilita s Azure Cosmos DB Gremlin
Azure Cosmos DB Graph modul úzce sleduje [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) traversal kroky specifikace, ale existují rozdíly.

## <a name="behavior-differences"></a>Rozdíly v chování

* Azure Cosmos DB Graph modul běží ***šířka první*** průchod, zatímco TinkerPop Gremlin je hloubka první. Toto chování dosahuje lepší výkon v horizontálně škálovatelné systému, jako je Cosmos DB. 

## <a name="unsupported-features"></a>Nepodporované funkce

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** je na konkrétním programovacím jazyku nezávislá specifikace pro procházení grafů. Cosmos DB Graph to ještě nepodporuje. Použijte `GremlinClient.SubmitAsync()` a předáte průchod jako textový řetězec.

* ***`property(set, 'xyz', 1)`*** nastavená mohutnost není dnes podporována. Místo toho použijte `property(list, 'xyz', 1)`. Další informace naleznete [v tématu Vlastnosti Vertex s TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** umožňuje dotazování grafů pomocí deklarativníporovnávání vzorů. Tato funkce není k dispozici.

* ***Objekty jako vlastnosti*** vrcholů nebo hran nejsou podporovány. Vlastnosti můžou být pouze primitivní typy nebo pole.

* ***Řazení podle vlastností*** `order().by(<array property>)` pole není podporováno. Podporuje se řazení pouze podle primitivních typů.

* ***Neprimitivní typy JSON*** nejsou podporovány. Použijte `string` `number`, `true` / `false` nebo typy. `null`hodnoty nejsou podporovány. 

* ***Serializátor GraphSONv3*** není aktuálně podporován. V `GraphSONv2` konfiguraci připojení použijte třídy Serializer, Reader a Writer.

* **Lambda výrazy a funkce** nejsou aktuálně podporovány. To zahrnuje `.map{<expression>}`, `.by{<expression>}`, `.filter{<expression>}` a funkce. Další informace a informace o tom, jak je přepsat pomocí gremlinové kroky, najdete v [tématu Poznámka k Lambdasovi](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* Transakce nejsou ***podporovány*** z důvodu distribuované povahy systému.  Nakonfigurujte vhodný model konzistence na účtu Gremlin tak, aby "četl vlastní zápisy" a k vyřešení konfliktních zápisů použijte optimistickou souběžnost.

## <a name="next-steps"></a>Další kroky
* Navštivte hlasovou stránku [uživatele Cosmos DB,](https://feedback.azure.com/forums/263030-azure-cosmos-db) kde můžete sdílet zpětnou vazbu a pomoci týmu zaměřit se na funkce, které jsou pro vás důležité.
