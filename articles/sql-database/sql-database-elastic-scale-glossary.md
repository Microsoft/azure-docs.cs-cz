---
title: Glosář nástroje elastické databáze | Microsoft Docs
description: Vysvětlení termínů používaných pro elastické databáze nástroje
services: sql-database
documentationcenter: ''
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 010d4f6d9f33b8fb6f1622321a3b566f68f28f5f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="elastic-database-tools-glossary"></a>Glosář nástroje elastické databáze
Následující termíny jsou definovány pro [nástroje elastické databáze](sql-database-elastic-scale-introduction.md), funkce Azure SQL Database. Nástroje se používají ke správě [mapuje horizontálního oddílu](sql-database-elastic-scale-shard-map-management.md)a zahrnout [klientské knihovny](sql-database-elastic-database-client-library.md), [nástroji pro sloučení rozdělení](sql-database-elastic-scale-overview-split-and-merge.md), [elastické fondy](sql-database-elastic-pool.md)a [dotazy](sql-database-elastic-query-overview.md). 

Tyto podmínky se používají v [přidání horizontálních, pomocí nástroje elastické databáze](sql-database-elastic-scale-add-a-shard.md) a [pomocí třídy RecoveryManager opravit problémy mapy horizontálního oddílu](sql-database-elastic-database-recovery-manager.md).

![Elastické škálování podmínky][1]

**Databáze**: Azure SQL database. 

**Data závislé směrování**: funkce, která umožňuje aplikaci připojit k horizontálních, přiřazen klíč konkrétní horizontálního dělení. V tématu [závislé směrování dat](sql-database-elastic-scale-data-dependent-routing.md). Porovnání  **[dotazu víc horizontálních](sql-database-elastic-scale-multishard-querying.md)**.

**Globální horizontálních mapy**: mapy mezi horizontálního dělení klíčů a jejich odpovídajících horizontálních oddílů v rámci **horizontálního oddílu sadu**. Mapy globální horizontálního oddílu je uložen v **správce mapy horizontálního oddílu**. Porovnání **místní horizontálních mapy**.

**Mapování horizontálních seznamu**: horizontálního oddílu mapy, ve které horizontálního dělení klíči jsou namapované jednotlivě. Porovnání **rozsahu horizontálního oddílu mapy**.   

**Mapa místního horizontálních**: uložené na horizontálního oddílu, místní horizontálních mapy obsahuje mapování shardlets nacházející se na horizontálního oddílu.

**Dotaz s více horizontálních**: možnost vydávat dotazy na víc horizontálních oddílů; nastaví výsledky se vrátí pomocí sémantiky UNION ALL (také označované jako "fan-out dotaz"). Porovnání **závislé směrování dat**.

**Víceklientské** a **jednoho klienta**: Zobrazí databázi jednoho klienta a víceklientské databáze:

![Databáze jeden a více klientů](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Zde je reprezentace **horizontálně dělené** databáze jednoho a víc klientů. 

![Databáze jeden a více klientů](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapování horizontálních rozsah**: horizontálního oddílu mapy, ve kterém je distribuční strategie horizontálního oddílu podle více oblastí souvislý hodnot. 

**Referenční tabulky**: tabulek, které nejsou horizontálně dělené ale se replikují napříč horizontálních oddílů. Například zip kódy, které mohou být uloženy v referenční tabulce. 

**Horizontálního oddílu**: Azure SQL database, která ukládá data z horizontálně dělené datové sady. 

**Pružnost horizontálního oddílu**: schopnost provádět i **vodorovné škálování** a **svislé škálování**.

**Horizontálně dělené tabulky**: tabulky, které jsou horizontálně dělené, tj., jejichž data se distribuuje do horizontálních oddílů na základě jejich hodnot klíče horizontálního dělení. 

**Klíč horizontálního dělení**: hodnota sloupce, který určuje, jak se data distribuuje do horizontálních oddílů. Typ hodnoty může být jedna z následujících: **int**, **bigint**, **varbinary**, nebo **uniqueidentifier**. 

**Sada horizontálního oddílu**: kolekce horizontálních oddílů, které jsou označené stejné ID horizontálního oddílu mapu ve Správci mapy horizontálního oddílu.  

**Shardlet**: všechna data související s jednu hodnotu klíče horizontálního dělení na horizontálního oddílu. Shardlet je nejmenší jednotkou přesun dat je možné při Redistribuce horizontálně dělené tabulky. 

**Mapování horizontálních**: sada mapování mezi horizontálního dělení klíčů a jejich odpovídajících horizontálních oddílů.

**Správce mapy horizontálního oddílu**: Správa objektů a dat úložiště, které obsahuje map(s) horizontálního oddílu, horizontálního oddílu umístění a mapování pro jednu nebo více sad horizontálního oddílu.

![Mapování][2]

## <a name="verbs"></a>Příkazy
**Vodorovné škálování**: operace škálování out (nebo v) kolekce horizontálních oddílů přidáním nebo odebráním horizontálních oddílů horizontálního oddílu mapu, jak je uvedeno níže.

![Vodorovného a svislého škálování][3]

**Sloučení**: operace přesunutí shardlets ze dvou horizontálních oddílů jeden horizontálního oddílu a odpovídajícím způsobem aktualizace mapy horizontálního oddílu.

**Přesunutí Shardlet**: operace přesunutí jednoho shardlet do různých horizontálního oddílu. 

**Horizontálního oddílu**: v rámci vodorovně dělení stejně jako strukturovaná data napříč více databází na základě klíče horizontálního dělení.

**Rozdělení**: v rámci přesun několik shardlets z jednoho horizontálního oddílu na jiné (obvykle novou) horizontálního oddílu. Uživatel zadal horizontálního dělení klíč jako bod rozdělení.

**Svislé škálování**: operace škálování nahoru (nebo dolů) úroveň výkonu jednotlivých horizontálního oddílu. Například změna horizontálního oddílu z standardní, Premium (což vede k více výpočetní prostředky). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

