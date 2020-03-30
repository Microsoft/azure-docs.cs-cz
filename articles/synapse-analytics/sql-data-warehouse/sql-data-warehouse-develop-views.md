---
title: Použití zobrazení T-SQL
description: Tipy pro použití zobrazení T-SQL v Azure SQL Data Warehouse pro vývoj řešení.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9eccc225c5473291b7535339ff64a036730894ae
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351515"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Zobrazení v datovém skladu Azure SQL
Zobrazení lze použít v mnoha různými způsoby ke zlepšení kvality vašeho řešení. 

Azure SQL Data Warehouse podporuje standardní a materializovaná zobrazení. Obě jsou virtuální tabulky vytvořené pomocí výrazů SELECT a prezentované dotazům jako logické tabulky. Zobrazení zapouzdřují složitost běžného výpočtu dat a přidávají vrstvu abstrakce do změn výpočtu, takže není nutné přepisovat dotazy.

## <a name="standard-view"></a>Standardní zobrazení
Standardní zobrazení vypočítá svá data pokaždé, když je použito zobrazení. Na disku nejsou uložena žádná data. Uživatelé obvykle používají standardní zobrazení jako nástroj, který pomáhá organizovat logické objekty a dotazy v databázi. Chcete-li použít standardní zobrazení, dotaz musí provést přímý odkaz na něj. Další informace naleznete v dokumentaci [create view.](/sql/t-sql/statements/create-view-transact-sql)

Zobrazení v datovém skladu SQL jsou uložena pouze jako metadata. V důsledku toho nejsou k dispozici následující možnosti:
* Neexistuje žádná možnost vazby schématu.
* Základní tabulky nelze aktualizovat prostřednictvím zobrazení.
* Zobrazení nelze vytvořit přes dočasné tabulky.
* Neexistuje žádná podpora pro expand / NOEXPAND rady
* V datovém skladu SQL nejsou žádná indexovaná zobrazení.

Standardní zobrazení lze využít k vynucení spojení optimalizovaných pro výkon mezi tabulkami. Zobrazení může například zahrnovat redundantní distribuční klíč jako součást kritérií spojení pro minimalizaci pohybu dat. Další výhodou zobrazení může být vynucení konkrétního dotazu nebo spojení nápovědy. Použití zobrazení tímto způsobem zaručuje, že spojení jsou vždy prováděny optimálním způsobem vyhnout se nutnosti pro uživatele pamatovat správné konstrukce pro jejich spojení.

## <a name="materialized-view"></a>Materializované zobrazení
Materializované zobrazení předem vypočítá, uloží a udržuje svá data v Azure SQL Data Warehouse stejně jako tabulka. Při použití zhmotněné zobrazení není potřeba žádná recomputace. Když se data načtou do základních tabulek, Azure SQL Data Warehouse synchronně aktualizuje materializovaná zobrazení.  Optimalizátor dotazů automaticky používá nasazená materializovaná zobrazení ke zlepšení výkonu dotazu, i když se na ně v dotazu neodkazuje.  Dotazy, které využívají nejvíce z materializovaných zobrazení, jsou složité dotazy (obvykle dotazy se spojeními a agregacemi) na velké tabulky, které vytvářejí malou sadu výsledků.  

Podrobnosti o syntaxi materializovaného pohledu a dalších požadavcích naleznete v části [VYTVOŘENÍ MATERIALIZOVANÉHO ZOBRAZENÍ JAKO SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  

Pokyny pro ladění dotazů najdete [v článku Optimalizace výkonu s materializovanými zobrazeními](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views). 

## <a name="example"></a>Příklad
Běžným vzorem aplikace je opětovné vytvoření tabulek pomocí příkazu CREATE TABLE AS SELECT (CTAS), po kterém následuje vzor přejmenování objektu při načítání dat.  Následující příklad přidá nové záznamy kalendářních dat do dimenze data. Všimněte si, jak je nejprve vytvořena nová tabulka, DimDate_New, a poté přejmenována tak, aby nahradila původní verzi tabulky.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```
Tento přístup však může mít za následek tabulky objevují a mizí z zobrazení uživatele, stejně jako "tabulka neexistuje" chybové zprávy. Zobrazení lze použít k poskytnutí konzistentní vrstvy prezentace uživatelům, zatímco základní objekty jsou přejmenovány. Tím, že uživatelé poskytují přístup k datům prostřednictvím zobrazení, nepotřebují viditelnost podkladových tabulek. Tato vrstva poskytuje konzistentní uživatelské prostředí a zároveň zajišťuje, že návrháři datového skladu mohou vyvíjet datový model. Možnost vyvíjet základní tabulky znamená, že návrháři mohou používat CTAS k maximalizaci výkonu během procesu načítání dat.   

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v části [Přehled vývoje SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


