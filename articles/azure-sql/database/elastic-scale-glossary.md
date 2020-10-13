---
title: Glosář nástrojů pro Elastic Database
description: Vysvětlení pojmů používaných pro nástroje elastické databáze
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 9c229fe6c1ccc7e2ce8e4f46ece51f315c8b7a90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362610"
---
# <a name="elastic-database-tools-glossary"></a>Glosář nástrojů pro Elastic Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pro [elastic Database nástroje](elastic-scale-introduction.md)jsou definovány následující výrazy. Nástroje se používají ke správě [map horizontálních oddílů](elastic-scale-shard-map-management.md)a zahrnují [knihovnu klienta](elastic-database-client-library.md), nástroj pro dělení na [slučování](elastic-scale-overview-split-and-merge.md), [elastické fondy](elastic-pool-overview.md)a [dotazy](elastic-query-overview.md). 

Tyto výrazy se používají při [přidávání horizontálních oddílů pomocí nástrojů elastic Database](elastic-scale-add-a-shard.md) a [k řešení problémů s mapou horizontálních oddílů pomocí třídy RecoveryManager](elastic-database-recovery-manager.md).

![Elastické výrazy škálování][1]

**Databáze**: databáze v Azure SQL Database. 

**Směrování závislé na datech**: funkce, která umožňuje aplikaci připojovat se k horizontálních oddílů pomocí konkrétního horizontálního dělení klíče. Viz [Směrování závislá na datech](elastic-scale-data-dependent-routing.md). Porovnejte s **[dotazem multi-horizontálních oddílů](elastic-scale-multishard-querying.md)**.

**Globální mapa horizontálních oddílů**: mapa mezi horizontálního dělení klíči a jejich příslušnými horizontálních oddílů v rámci **horizontálních oddílů sady**. Globální mapa horizontálních oddílů je uložena ve **Správci map horizontálních oddílů**. Porovnat s **místní mapou horizontálních oddílů**

**List map horizontálních oddílů**: mapování horizontálních oddílů, ve kterém jsou klíče horizontálního dělení namapovány individuálně. Porovnejte s **rozsahem horizontálních oddílů map**.   

**Místní Mapa horizontálních oddílů**: uložená v horizontálních oddílů, místní Mapa horizontálních oddílů obsahuje mapování pro shardlety, která se nachází na horizontálních oddílů.

**Dotaz s více horizontálních oddílů**: možnost vydávat dotaz na více horizontálních oddílů; sady výsledků jsou vráceny pomocí sémantiky UNION ALL (označuje se také jako "dotaz na ventilátor"). Porovnání s **směrováním závislým na datech**.

**Vícenásobný tenant** a **jeden tenant**: zobrazuje se databáze s jedním klientem a víceklientské databáze:

![Jedna a více tenantů databází](./media/elastic-scale-glossary/multi-single-simple.png)

Tady je reprezentace **horizontálně dělené** jedné a víceklientské databáze. 

![Jedna a více tenantů databází](./media/elastic-scale-glossary/shards-single-multi.png)

**Map horizontálních oddílů rozsahu**: mapa horizontálních oddílů, ve které je strategie distribuce horizontálních oddílů založena na několika oblastech souvislých hodnot. 

**Referenční tabulky**: tabulky, které nejsou horizontálně dělené, ale jsou replikovány napříč horizontálních oddílů. Například kódy PSČ mohou být uloženy v referenční tabulce. 

**Horizontálních oddílů**: databáze v Azure SQL Database, která ukládá data ze sady dat horizontálně dělené. 

**Horizontálních oddílů**: schopnost provádět **horizontální** i **vertikální škálování**.

**Tabulky horizontálně dělené**: tabulky, které jsou horizontálně dělené, tj., jejichž data jsou distribuována napříč horizontálních oddílů na základě jejich hodnot horizontálního dělení klíče. 

**Horizontálního dělení klíč**: hodnota sloupce, která určuje, jak jsou data distribuována napříč horizontálních oddílů. Typ hodnoty může být jedna z následujících hodnot: **int**, **bigint**, **varbinary**nebo **uniqueidentifier**. 

**Horizontálních oddílů sada**: kolekce horizontálních oddílů, která je přiřazená stejné mapě horizontálních oddílů ve Správci map horizontálních oddílů.  

**Shardletu**: všechna data přidružená k jedné hodnotě klíče horizontálního dělení na horizontálních oddílů. Shardletu je nejmenší dostupná jednotka přesunu dat při redistribuci tabulek horizontálně dělené. 

**Mapa horizontálních oddílů**: sada mapování mezi horizontálního dělení klíči a jejich příslušnými horizontálních oddílů.

**Správce map horizontálních oddílů**: objekt správy a úložiště dat, které obsahuje mapy horizontálních oddílů, umístění horizontálních oddílů a mapování pro jednu nebo více horizontálních oddílů sad.

![Diagram znázorňuje správce mapy horizontálních oddílů spojený s shardmaps_global, shards_global a shard_mappings_global.][2]

## <a name="verbs"></a>Příkazy
**Horizontální škálování**: Změna velikosti (nebo v) kolekce horizontálních oddílů přidáním nebo odebráním horizontálních oddílů na mapu horizontálních oddílů, jak je znázorněno níže.

![Horizontální a svislé škálování][3]

**Sloučení**: jednání přesunutí shardlety ze dvou horizontálních oddílů na jednu horizontálních oddílů a aktualizace mapování horizontálních oddílů, odpovídajícím způsobem.

**Shardletu Move**: jednání o přesunutí jedné shardletu na jiný horizontálních oddílů. 

**Horizontálních oddílů**: Jedná se o horizontální dělení identicky strukturovaných dat napříč více databázemi na základě horizontálního dělení klíče.

**Split**: jednání přesunu několika shardlety z jedné horizontálních oddílů do druhé (obvykle New) horizontálních oddílů. Horizontálního dělení klíč poskytuje uživatel jako rozdělený bod.

**Vertikální škálování**: Změna velikosti (nebo zmenšení) výpočetní velikosti jednotlivých horizontálních oddílů. Například změna horizontálních oddílů z úrovně Standard na Premium (což vede k dalším výpočetním prostředkům). 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-glossary/glossary.png
[2]: ./media/elastic-scale-glossary/mappings.png
[3]: ./media/elastic-scale-glossary/h_versus_vert.png

