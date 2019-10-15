---
title: Azure Cosmos DB kompatibilita Gremlin s funkcemi TinkerPop
description: Problémy s kompatibilitou modulu Graph Documentation Engine
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 0ed5824859b8463919a809861993f9f98a4f9251
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327038"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB kompatibilita Gremlin
Modul Azure Cosmos DB Graph úzce sleduje specifikace kroků procházení [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ale existují i rozdíly.

## <a name="behavior-differences"></a>Rozdíly v chování

* Modul graphu pro Azure Cosmos DB pracuje ***s prvními*** průchody, zatímco je TinkerPop Gremlin na hloubku. Toto chování dosahuje lepšího výkonu v horizontálním škálovatelném systému, jako je Cosmos DB. 

## <a name="unsupported-features"></a>Nepodporované funkce

* ***[Gremlin Bytecode](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** je na konkrétním programovacím jazyku nezávislá specifikace pro procházení grafů. Cosmos DB Graph ho ještě nepodporuje. Použijte ```GremlinClient.SubmitAsync()``` a předejte průchod jako textový řetězec.

* nastavení mohutnosti ***```property(set, 'xyz', 1)```*** se v současné době nepodporuje. Místo toho použijte ```property(list, 'xyz', 1)```.

* ***```match()```*** umožňuje dotazování grafů pomocí deklarativního porovnávání vzorů. Tato možnost není k dispozici.

* ***Objekty jako vlastnosti*** na vrcholech nebo hranách nejsou podporovány. Vlastnosti můžou být pouze primitivní typy nebo pole.

* ***Řazení podle vlastností pole*** ```.order().by(<array property>)``` není podporováno. Podporuje se řazení pouze podle primitivních typů.

* ***Neprimitivní typy JSON*** nejsou podporovány. Použijte typy ```string```, ```number``` nebo ```true``` @ no__t-3 @ no__t-4. hodnoty ```null``` nejsou podporovány. 

* Serializátor ***GraphSONv3*** není dnes k dispozici.

* ***Transakce*** nejsou podporovány z důvodu distribuované povahy systému.  Nakonfigurujte vhodný model konzistence v účtu Gremlin na číst vlastní zápisy a pomocí optimistické souběžnosti vyřešte konfliktní zápisy.

## <a name="next-steps"></a>Další kroky
* Přejděte na stránku [Cosmos DB hlas uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db) , kde můžete sdílet zpětnou vazbu a pomáhat týmu soustředit se na funkce, které jsou pro vás důležité.
