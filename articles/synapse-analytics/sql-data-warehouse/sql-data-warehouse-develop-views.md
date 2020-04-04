---
title: Použití zobrazení T-SQL
description: Tipy pro používání zobrazení T-SQL a vývoj řešení ve fondu Synapse SQL.
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
ms.openlocfilehash: 76442368fe4b3e498f622a8a3cd5b5b973f16bd6
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633399"
---
# <a name="views-in-synapse-sql-pool"></a>Zobrazení v fondu Synapse SQL

Zobrazení lze použít v mnoha různými způsoby ke zlepšení kvality vašeho řešení.

Fond SQL podporuje standardní i materializované pohledy. Obě jsou virtuální tabulky vytvořené pomocí výrazů SELECT a prezentované dotazům jako logické tabulky.

Zobrazení zapouzdřují složitost běžného výpočtu dat a přidávají vrstvu abstrakce do změn výpočtu, takže není nutné přepisovat dotazy.

## <a name="standard-view"></a>Standardní zobrazení

Standardní zobrazení vypočítá svá data pokaždé, když je použito zobrazení. Na disku nejsou uložena žádná data. Uživatelé obvykle používají standardní zobrazení jako nástroj, který pomáhá organizovat logické objekty a dotazy v databázi.

Chcete-li použít standardní zobrazení, dotaz musí provést přímý odkaz na něj. Další informace naleznete v dokumentaci [create view.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Zobrazení ve fondu SQL jsou uložena pouze jako metadata. V důsledku toho nejsou k dispozici následující možnosti:

* Neexistuje žádná možnost vazby schématu.
* Základní tabulky nelze aktualizovat v zobrazení.
* Zobrazení nelze vytvořit přes dočasné tabulky
* Neexistuje žádná podpora pro expand / NOEXPAND rady
* Ve fondu SQL nejsou žádná indexovaná zobrazení.

Standardní zobrazení lze využít k vynucení spojení optimalizovaných pro výkon mezi tabulkami. Zobrazení může například zahrnovat redundantní distribuční klíč jako součást kritérií spojení pro minimalizaci pohybu dat.

Další výhodou zobrazení může být vynucení konkrétního dotazu nebo spojení nápovědy. Použití zobrazení tímto způsobem zaručuje, že spojení jsou vždy prováděny optimálním způsobem vyhnout se nutnosti pro uživatele pamatovat správné konstrukce pro jejich spojení.

## <a name="materialized-view"></a>Materializované zobrazení

Materializované zobrazení předem vypočítá, uloží a udržuje svá data ve fondu SQL stejně jako tabulka. Při použití zhmotněné zobrazení není potřeba žádná recomputace.

Jak se data načítají do základních tabulek, fond SQL synchronně aktualizuje materializovaná zobrazení.  Optimalizátor dotazů automaticky používá nasazená materializovaná zobrazení ke zlepšení výkonu dotazu, i když se na ně v dotazu neodkazuje.  

Dotazy, které využívají nejvíce z materializovaných zobrazení, jsou složité dotazy (obvykle dotazy se spojeními a agregacemi) na velké tabulky, které vytvářejí malou sadu výsledků.  

Podrobnosti o syntaxi materializovaného pohledu a dalších požadavcích naleznete v části [VYTVOŘENÍ MATERIALIZOVANÉHO ZOBRAZENÍ JAKO SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Pokyny pro ladění dotazů najdete [v článku Optimalizace výkonu s materializovanými zobrazeními](performance-tuning-materialized-views.md).

## <a name="example"></a>Příklad

Běžným vzorem aplikace je opětovné vytvoření tabulek pomocí příkazu CREATE TABLE AS SELECT (CTAS), po kterém následuje vzor přejmenování objektu při načítání dat.  

Následující příklad přidá nové záznamy kalendářních dat do dimenze data. Všimněte si, jak je nejprve vytvořena nová tabulka, DimDate_New, a poté přejmenována tak, aby nahradila původní verzi tabulky.

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

Tento přístup však může mít za následek tabulky objevují a mizí z pohledu uživatele spolu s vydáním "tabulka neexistuje" chybové zprávy.

Zobrazení lze použít k poskytnutí konzistentní vrstvy prezentace uživatelům, zatímco podkladové objekty jsou přejmenovány. Tím, že uživatelé poskytují přístup k datům prostřednictvím zobrazení, nepotřebují viditelnost podkladových tabulek.

Tato vrstva poskytuje konzistentní uživatelské prostředí a zároveň zajišťuje, že návrháři datového skladu mohou vyvíjet datový model. Možnost vyvíjet základní tabulky znamená, že návrháři mohou používat CTAS k maximalizaci výkonu během procesu načítání dat.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v [tématu přehled vývoje fondu SQL](sql-data-warehouse-overview-develop.md).
