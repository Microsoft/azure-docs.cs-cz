---
title: Glosář nástrojů pro Elastic Database | Microsoft Docs
description: Vysvětlení pojmů používaných pro nástroje elastické databáze
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 3ed0cc9dce312cb9736b3e32ba46d2cb1cca3ef8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568463"
---
# <a name="elastic-database-tools-glossary"></a>Glosář nástrojů pro Elastic Database

Následující výrazy jsou definovány pro [nástroje elastic Database](sql-database-elastic-scale-introduction.md), což je funkce Azure SQL Database. Nástroje se používají ke správě [map horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md)a zahrnují [knihovnu klienta](sql-database-elastic-database-client-library.md), nástroj pro dělení na [slučování](sql-database-elastic-scale-overview-split-and-merge.md), [elastické fondy](sql-database-elastic-pool.md)a [dotazy](sql-database-elastic-query-overview.md). 

Tyto výrazy se používají při [přidávání horizontálních oddílů pomocí nástrojů elastic Database](sql-database-elastic-scale-add-a-shard.md) a [k řešení problémů s mapou horizontálních oddílů pomocí třídy RecoveryManager](sql-database-elastic-database-recovery-manager.md).

![Elastické výrazy škálování][1]

**Databáze**: Databázi Azure SQL. 

**Směrování závislé na datech**: Funkce, která umožňuje aplikaci připojovat se k horizontálních oddílů pomocí konkrétního horizontálního dělení klíče. Viz [Směrování závislá na datech](sql-database-elastic-scale-data-dependent-routing.md). Porovnejte s **[dotazem multi-horizontálních oddílů](sql-database-elastic-scale-multishard-querying.md)** .

**Globální mapa horizontálních oddílů**: Mapování mezi klíči horizontálního dělení a jejich příslušnými horizontálních oddílů v rámci **horizontálních oddílů sady**. Globální mapa horizontálních oddílů je uložena ve **Správci map horizontálních oddílů**. Porovnat s **místní mapou horizontálních oddílů**

**Zobrazit mapu horizontálních oddílů**: Mapa horizontálních oddílů, ve které jsou klíče horizontálního dělení mapovány individuálně. Porovnejte s **rozsahem horizontálních oddílů map**.   

**Místní Mapa horizontálních oddílů**: Místní Mapa horizontálních oddílů je uložená na horizontálních oddílů, která obsahuje mapování pro shardlety, která se nachází v horizontálních oddílů.

**Dotaz s více horizontálních oddílů**: Možnost vydávat dotaz na více horizontálních oddílů; sady výsledků jsou vráceny pomocí sémantiky UNION ALL (označuje se také jako "dotaz na ventilátor"). Porovnání s **směrováním závislým na datech**.

**Vícenásobný tenant** a **jeden tenant**: Zobrazuje se databáze s jedním klientem a víceklientské databáze:

![Jedna a více tenantů databází](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Tady je reprezentace **horizontálně dělené** jedné a víceklientské databáze. 

![Jedna a více tenantů databází](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapa horizontálních oddílů rozsahu**: Mapa horizontálních oddílů, ve které je strategie distribuce horizontálních oddílů založena na několika oblastech souvislých hodnot. 

**Referenční tabulky**: Tabulky, které nejsou horizontálně dělené, ale jsou replikovány napříč horizontálních oddílů. Například kódy PSČ mohou být uloženy v referenční tabulce. 

**Horizontálních oddílů**: Azure SQL Database, která ukládá data z horizontálně dělené sady dat. 

**Elastická horizontálních oddílů**: Možnost provádět **horizontální** i **vertikální škálování**.

**Tabulky horizontálně dělené**: Tabulky, které jsou horizontálně dělené, tj., jejichž data jsou distribuována napříč horizontálních oddílů na základě jejich hodnot klíčů horizontálního dělení. 

**Horizontálního dělení klíč**: Hodnota sloupce, která určuje, jak jsou data distribuována napříč horizontálních oddílů. Typ hodnoty může být jedna z následujících hodnot: **int**, **bigint**, **varbinary**nebo **uniqueidentifier**. 

**Horizontálních oddílů sada**: Kolekce horizontálních oddílůů, které jsou připsány ke stejné mapě horizontálních oddílů ve Správci map horizontálních oddílů.  

**Shardletu**: Všechna data přidružená k jedné hodnotě horizontálního dělení klíče na horizontálních oddílů. Shardletu je nejmenší dostupná jednotka přesunu dat při redistribuci tabulek horizontálně dělené. 

**Mapa horizontálních oddílů**: Sada mapování mezi klíči horizontálního dělení a jejich příslušnými horizontálních oddílů.

**Správce map horizontálních oddílů**: Objekt správy a úložiště dat, které obsahuje mapy horizontálních oddílů, umístění horizontálních oddílů a mapování pro jednu nebo více horizontálních oddílů sad.

![Mapování][2]

## <a name="verbs"></a>Příkazy
**Horizontální škálování**: Akce horizontálního navýšení kapacity (nebo v) kolekce horizontálních oddílů přidáním nebo odebráním horizontálních oddílů na mapu horizontálních oddílů, jak je znázorněno níže.

![Horizontální a svislé škálování][3]

**Sloučení**: Reakce na přesun shardlety ze dvou horizontálních oddílů na jednu horizontálních oddílů a odpovídajícím způsobem aktualizuje mapu horizontálních oddílů.

**Shardletu přesunout**: Akce přesunu jednoho shardletu do jiného horizontálních oddílů. 

**Horizontálních oddílů**: Jedná se o horizontální dělení identicky strukturovaných dat napříč více databázemi na základě horizontálního dělení klíče.

**Rozdělit**: Reakce na přesun několika shardlety z jedné horizontálních oddílů na jinou (obvykle nová) horizontálních oddílů. Horizontálního dělení klíč poskytuje uživatel jako rozdělený bod.

**Vertikální škálování**: Výpočet velikosti jednotlivých horizontálních oddílů (nebo dolů). Například změna horizontálních oddílů z úrovně Standard na Premium (což vede k dalším výpočetním prostředkům). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

