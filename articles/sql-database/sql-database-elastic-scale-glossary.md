---
title: Glosář nástrojů elastické databáze | Dokumentace Microsoftu
description: Vysvětlení termínů použitých pro nástroje pro elastické databáze
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 446203b45744a95c32cd41d9ded26fd960ac8a22
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55561429"
---
# <a name="elastic-database-tools-glossary"></a>Glosář nástrojů elastické databáze

Následující termíny jsou definovány pro [nástrojů Elastic Database](sql-database-elastic-scale-introduction.md), funkce služby Azure SQL Database. Nástroje se používají ke správě [mapy horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md)a zahrnout [Klientská knihovna](sql-database-elastic-database-client-library.md), [nástroj split-merge](sql-database-elastic-scale-overview-split-and-merge.md), [elastické fondy](sql-database-elastic-pool.md)a [dotazy](sql-database-elastic-query-overview.md). 

Tyto podmínky se používají v [přidání horizontálního oddílu používání nástrojů Elastic Database](sql-database-elastic-scale-add-a-shard.md) a [oprava problémů s horizontálními oddíly mapy pomocí třídy RecoveryManager](sql-database-elastic-database-recovery-manager.md).

![Elastické škálování podmínky][1]

**Database**: Databázi SQL Azure. 

**Směrování závislé na datech**: Funkce, které umožňuje připojení k horizontálnímu oddílu přiřazen klíč horizontálního dělení konkrétní aplikaci. Zobrazit [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md). Porovnat s  **[dotazování více horizontálních oddílů](sql-database-elastic-scale-multishard-querying.md)**.

**Mapy horizontálních oddílů globální**: Mapování mezi klíče horizontálního dělení a jejich odpovídajících horizontálních oddílů v rámci **nastavení horizontálními oddíly**. Mapy horizontálních oddílů globální je uložen v **správce mapování horizontálních oddílů**. Porovnat s **mapy horizontálních oddílů místní**.

**Mapy seznamů horizontálních oddílů**: Mapy horizontálních oddílů v které horizontálního dělení klíči jsou namapovány jednotlivě. Porovnat s **mapy horizontálních oddílů v rozsahu**.   

**Mapy horizontálních oddílů místní**: Mapy horizontálních oddílů místní uložený v horizontálním oddílu, obsahuje mapování shardlety, které jsou umístěny v horizontálním oddílu.

**Dotazování více horizontálních oddílů**: Možnost dotazu na více horizontálních oddílů; Nastaví výsledky jsou vráceny pomocí sémantiky UNION ALL (označované také jako "větveného dotazu"). Porovnat s **směrování závislé na datech**.

**Víceklientské** a **jednoho tenanta**: To ukazuje databázi jednoho tenanta a víceklientskou databází:

![Databáze jedné a více tenantů](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Tady je reprezentace **horizontálně dělené** databáze jedné a více tenantů. 

![Databáze jedné a více tenantů](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapy rozsahů horizontálních oddílů**: Mapy horizontálních oddílů, ve kterém je distribuční strategie horizontálního dělení podle více oblastí souvislých hodnot. 

**Referenční tabulky**: Tabulky, které nejsou horizontálně dělené ale se replikují napříč horizontálními oddíly. Například PSČ, mohou být uloženy v referenční tabulce. 

**Horizontální oddíl**: Azure SQL database, která ukládá data z datové sady horizontálně dělené. 

**Skripty elasticity horizontálních**: Umožňuje provést obojí **horizontální škálování** a **vertikální škálování**.

**Horizontálně dělené tabulky**: Tabulky, které jsou horizontálně dělené, to znamená, jejichž data jsou distribuovaná napříč horizontálními oddíly na základě jejich hodnot klíče horizontálního dělení. 

**Klíč horizontálního dělení**: Hodnota sloupce, který určuje, jak jsou data distribuovaná napříč horizontálními oddíly. Typ hodnoty může být jeden z následujících: **int**, **bigint**, **varbinary**, nebo **uniqueidentifier**. 

**Nastavení horizontálními oddíly**: Kolekce horizontální oddíly, které mají atributy do stejné mapy horizontálních oddílů v správce mapování horizontálních oddílů.  

**Shardlet**: Všechna data související s jednu hodnotu klíče horizontálního dělení na horizontální oddíl. Shardletem je nejmenší jednotka přesun dat je možné při další distribuci horizontálně dělené tabulky. 

**Mapy horizontálních oddílů**: Sada mapování mezi klíče horizontálního dělení a jejich odpovídajících horizontálních oddílů.

**Správce mapování horizontálních oddílů**: Správa objektů a dat úložiště, které obsahuje mapám horizontálních oddílů, umístění horizontálních oddílů a mapování pro jednu nebo více sad horizontálních oddílů.

![Mapování][2]

## <a name="verbs"></a>Příkazy
**Horizontální škálování**: V rámci vertikálního navýšení kapacity (nebo v) kolekce horizontálních oddílů přidáním nebo odebráním horizontálních oddílů do mapy horizontálních oddílů, jak je znázorněno níže.

![Vodorovné a svislé škálování][3]

**Sloučit**: Operace přesunu shardletů z dvou horizontálních oddílů do jednoho horizontálního oddílu a odpovídajícím způsobem aktualizuje mapy horizontálních oddílů.

**Přesunutí Shardletu**: V rámci jednoho shardletu Přesun do jiného horizontálního oddílu. 

**Horizontální oddíl**: V rámci vodorovně dělení identicky strukturovaná data ve více databázích na základě klíče horizontálního dělení.

**Rozdělení**: Operace přesunu několik shradletů v jednom horizontálním oddílu do jiného horizontálního oddílu (zpravidla nové). Klíč horizontálního dělení je poskytnutý uživatelem jako bod rozdělení.

**Vertikální škálování**: Operace škálování () velikost výpočetního jednotlivých horizontálních oddílů. Například změna horizontálního oddílu úroveň ze Standard na Premium (což vede k víc výpočetních prostředků). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

