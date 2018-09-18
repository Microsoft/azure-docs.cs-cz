---
title: Glosář nástrojů elastické databáze | Dokumentace Microsoftu
description: Vysvětlení termínů použitých pro nástroje pro elastické databáze
services: sql-database
documentationcenter: ''
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: sstein
ms.openlocfilehash: 387f40204c8ab07ba0205fd74b5c6a549efff0ef
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45728842"
---
# <a name="elastic-database-tools-glossary"></a>Glosář nástrojů elastické databáze
Následující termíny jsou definovány pro [nástrojů Elastic Database](sql-database-elastic-scale-introduction.md), funkce služby Azure SQL Database. Nástroje se používají ke správě [mapy horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md)a zahrnout [Klientská knihovna](sql-database-elastic-database-client-library.md), [nástroj split-merge](sql-database-elastic-scale-overview-split-and-merge.md), [elastické fondy](sql-database-elastic-pool.md)a [dotazy](sql-database-elastic-query-overview.md). 

Tyto podmínky se používají v [přidání horizontálního oddílu používání nástrojů Elastic Database](sql-database-elastic-scale-add-a-shard.md) a [oprava problémů s horizontálními oddíly mapy pomocí třídy RecoveryManager](sql-database-elastic-database-recovery-manager.md).

![Elastické škálování podmínky][1]

**Databáze**: Azure SQL database. 

**Směrování závislé na datech**: funkce, které umožňuje připojení k horizontálnímu oddílu přiřazen klíč horizontálního dělení konkrétní aplikaci. Zobrazit [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md). Porovnat s  **[dotazování více horizontálních oddílů](sql-database-elastic-scale-multishard-querying.md)**.

**Mapy horizontálních oddílů globální**: mapování mezi klíče horizontálního dělení a jejich odpovídajících horizontálních oddílů v rámci **nastavení horizontálními oddíly**. Mapy horizontálních oddílů globální je uložen v **správce mapování horizontálních oddílů**. Porovnat s **mapy horizontálních oddílů místní**.

**Mapy seznamů horizontálních oddílů**: mapy horizontálních oddílů, v které horizontálního dělení klíči jsou namapovány jednotlivě. Porovnat s **mapy horizontálních oddílů v rozsahu**.   

**Mapy horizontálních oddílů místní**: uložená v horizontálním oddílu, mapy horizontálních oddílů místní obsahuje mapování shardlety, které jsou umístěny v horizontálním oddílu.

**Dotazování více horizontálních oddílů**: možnost dotazu na více horizontálních oddílů; sad výsledků jsou vráceny pomocí sémantiky UNION ALL (označované také jako "větveného dotazu"). Porovnat s **směrování závislé na datech**.

**Víceklientské** a **jednoho tenanta**: Zobrazí databáze s jedním tenantem a víceklientskou databází:

![Databáze jedné a více tenantů](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Tady je reprezentace **horizontálně dělené** databáze jedné a více tenantů. 

![Databáze jedné a více tenantů](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapy rozsahů horizontálních oddílů**: mapy horizontálních oddílů, ve kterém je distribuční strategie horizontálního dělení podle více oblastí souvislých hodnot. 

**Referenční tabulky**: tabulky, které nejsou horizontálně dělené ale se replikují napříč horizontálními oddíly. Například PSČ, mohou být uloženy v referenční tabulce. 

**Horizontální oddíl**: Azure SQL database, která ukládá data z datové sady horizontálně dělené. 

**Skripty elasticity horizontálních**: umožňuje provést obojí **horizontální škálování** a **vertikální škálování**.

**Horizontálně dělené tabulky**: tabulky, které jsou horizontálně dělené, to znamená, jejichž data jsou distribuovaná napříč horizontálními oddíly na základě jejich hodnot klíče horizontálního dělení. 

**Klíč horizontálního dělení**: hodnota sloupce, který určuje, jak jsou data distribuovaná napříč horizontálními oddíly. Typ hodnoty může být jeden z následujících: **int**, **bigint**, **varbinary**, nebo **uniqueidentifier**. 

**Nastavení horizontálními oddíly**: kolekce horizontální oddíly, které mají atributy do stejné mapy horizontálních oddílů v správce mapování horizontálních oddílů.  

**Shardlet**: všechna data související s jednu hodnotu klíče horizontálního dělení na horizontální oddíl. Shardletem je nejmenší jednotka přesun dat je možné při další distribuci horizontálně dělené tabulky. 

**Mapy horizontálních oddílů**: sada mapování mezi klíče horizontálního dělení a jejich odpovídajících horizontálních oddílů.

**Správce mapování horizontálních oddílů**: Správa objektů a dat úložiště, které obsahuje mapám horizontálních oddílů, umístění horizontálních oddílů a mapování pro jednu nebo více sad horizontálních oddílů.

![Mapování][2]

## <a name="verbs"></a>Příkazy
**Horizontální škálování**: act vertikálního navýšení kapacity (nebo v) kolekce horizontálních oddílů přidáním nebo odebráním horizontálních oddílů do mapy horizontálních oddílů, jak je znázorněno níže.

![Vodorovné a svislé škálování][3]

**Sloučit**: operace přesunutí shardletů z dvou horizontálních oddílů do jednoho horizontálního oddílu a odpovídajícím způsobem aktualizuje mapy horizontálních oddílů.

**Přesunutí Shardletu**: v rámci jednoho shardletu Přesun do jiného horizontálního oddílu. 

**Horizontální oddíl**: v rámci stejně jako ve vodorovném směru dělení strukturovaných dat napříč několika databázemi na základě klíče horizontálního dělení.

**Rozdělení**: operace přesunu několik shradletů v jednom horizontálním oddílu do jiného horizontálního oddílu (zpravidla nové). Klíč horizontálního dělení je poskytnutý uživatelem jako bod rozdělení.

**Vertikální škálování**: operace škálování () velikost výpočetního jednotlivých horizontálních oddílů. Například změna horizontálního oddílu úroveň ze Standard na Premium (což vede k víc výpočetních prostředků). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

