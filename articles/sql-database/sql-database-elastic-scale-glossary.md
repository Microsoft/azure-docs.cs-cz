---
title: Glosář nástrojů elastické databáze
description: Vysvětlení termínů používaných pro elastické databázové nástroje
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
ms.openlocfilehash: ab972db78cd213497fb96486b3e16b01f2c4c6eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823632"
---
# <a name="elastic-database-tools-glossary"></a>Glosář nástrojů elastické databáze

Následující termíny jsou definovány pro [nástroje elastické databáze](sql-database-elastic-scale-introduction.md), což je funkce Azure SQL Database. Nástroje se používají ke správě [map úlomků](sql-database-elastic-scale-shard-map-management.md)a zahrnují [klientskou knihovnu](sql-database-elastic-database-client-library.md), [nástroj pro rozdělení sloučení](sql-database-elastic-scale-overview-split-and-merge.md), [elastické fondy](sql-database-elastic-pool.md)a [dotazy](sql-database-elastic-query-overview.md). 

Tyto termíny se používají při [přidání šněru pomocí nástrojů elastické databáze](sql-database-elastic-scale-add-a-shard.md) a [pomocí RecoveryManager třídy opravit problémy s mapou oddílu](sql-database-elastic-database-recovery-manager.md).

![Termíny elastické stupnice][1]

**Databáze**: Databáze Azure SQL. 

**Směrování závislé na datech**: Funkce, která umožňuje aplikaci připojit se k oddílu daného konkrétního klíče pro řícení. Viz [Směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md). Porovnejte **[s dotazem s více oddíly .](sql-database-elastic-scale-multishard-querying.md)**

**Globální mapa úlomků**: Mapa mezi klíči s náloží a jejich příslušnými oddíly v **rámci sady šitrů**. Globální mapa úlomků je uložena ve **správci mapy skřípků**. Porovnejte s **místní mapou úlomku**.

**Seznam mapy štřepů**: Mapa šiřidla, ve kterém jsou jednotlivě mapovány klíče s náloží. Porovnejte s **mapou úlomku rozsahu**.   

**Místní mapa úlomku**: Uloženo na šiřidlo, místní mapa šikříku obsahuje mapování pro shardlety, které jsou umístěny na šiřidlo.

**Dotaz s více úlomky**: Možnost vydat dotaz proti více úlomkům; sady výsledků jsou vráceny pomocí union all sémantiku (také známý jako "fan-out dotaz"). Porovnejte s **směrováním závislým na datech**.

**Víceklientské** a **jednoklientské:** Zobrazí databázi s jedním tenantem a databázi s více klienty:

![Databáze s jedním a více klienty](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Zde je reprezentace **databáze s oddíly** jednoho a více klientů. 

![Databáze s jedním a více klienty](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapa úlomků rozsahu**: Mapa úložného období, ve které je strategie distribuce úlomků založena na více rozsahech sousedících hodnot. 

**Referenční tabulky**: Tabulky, které nejsou rozděleny do platových disponovaných, ale jsou replikovány napříč oddíly šikmy. PsČ lze například uložit do referenční tabulky. 

**Shard:** Databáze Azure SQL, která ukládá data z řídké datové sady. 

**Elasticita horizontálního oddílu**: Schopnost provádět **horizontální i** **svislé měřítko**.

**Tabulky s rozdělením**: Tabulky, které jsou rozděleny podle oddílů, tj. 

**Klíč rozdělení výtažků**: Hodnota sloupce, která určuje způsob distribuce dat mezi oddíly šikmu. Typ hodnoty může být jeden z následujících: **int**, **bigint**, **varbinary**nebo **uniqueidentifier**. 

**Sada úlomků**: Kolekce úlomků, které jsou přiřazeny ke stejné mapě šikmých oddílů ve správci mapy oddílu.  

**Shardlet**: Všechna data spojená s jedinou hodnotou klíče s návěsným nožem na šiřidlu. Shardlet je nejmenší jednotka pohybu dat možné při redistribuci rozdělení tabulky. 

**Mapa šněrování**: Sada mapování mezi klíči s náložemi a jejich příslušnými oddíly.

**Správce mapy síní**: Úložiště objektů a dat pro správu, které obsahuje mapování úlomků, umístění šitrů a mapování pro jednu nebo více sad štřepů.

![Mapování][2]

## <a name="verbs"></a>Příkazy
**Horizontální škálování**: Akt škálování out (nebo v) kolekce horizontálních oddílů přidáním nebo odebráním horizontálních oddílů do mapy horizontálního oddílu, jak je znázorněno níže.

![Vodorovné a svislé měřítko][3]

**Sloučení**: Akt přesunutí shardlets ze dvou úlomků do jednoho úlomku a odpovídajícím způsobem aktualizovat mapu střepů.

**Shardlet move**: Akt přesunutí jednoho shardletu na jiný úlomek. 

**Horizontální oddíl**: Akt vodorovně dělení identicky strukturovaných dat mezi více databází na základě klíče horizontálního dělení.

**Rozdělení**: Akt přesunutí několika shardlets z jednoho úlomku do druhého (obvykle nové) šiřidla. Klíč rozdělení je poskytován uživatelem jako bod rozdělení.

**Vertikální škálování**: Akt škálování nahoru (nebo dolů) výpočetní velikost jednotlivých horizontálního oddílu. Například změna úlomku ze standardního na prémiový (což má za následek více výpočetních prostředků). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

