---
title: Použití zobrazení T-SQL
description: Tipy pro použití zobrazení T-SQL a vývoj řešení v synapse fondu SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26eb3a495fd1c896416265687d92da66dfc3599b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212288"
---
# <a name="views-in-synapse-sql-pool"></a>Zobrazení ve fondu synapse SQL

Zobrazení lze použít mnoha různými způsoby ke zlepšení kvality řešení.

Fond SQL podporuje standardní i materializovaná zobrazení. Obě jsou virtuální tabulky vytvořené pomocí výrazů SELECT a prezentují se dotazům jako logické tabulky.

Zobrazení zapouzdřují složitost běžných výpočtů dat a přidávají do výpočtů změny, takže není potřeba přepisovat dotazy.

## <a name="standard-view"></a>Standardní zobrazení

Standardní zobrazení vypočítá data při každém použití zobrazení. Na disku nejsou uložená žádná data. Lidé obvykle používají standardní zobrazení jako nástroj, který pomáhá organizovat logické objekty a dotazy v databázi.

Chcete-li použít standardní zobrazení, dotaz musí na něj vytvořit přímý odkaz. Další informace najdete v dokumentaci k [vytváření zobrazení](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

Zobrazení ve fondu SQL jsou ukládána pouze jako metadata. V důsledku toho nejsou k dispozici následující možnosti:

* Neexistuje možnost vazby schématu.
* Základní tabulky nejde aktualizovat prostřednictvím zobrazení.
* Zobrazení nejde vytvořit přes dočasné tabulky.
* Neexistuje žádná podpora pro pomocné parametry pro rozbalení a rozbalení.
* Ve fondu SQL nejsou žádná indexovaná zobrazení.

Standardní zobrazení je možné využít k vykonání spojení optimalizovaného pro výkon mezi tabulkami. Zobrazení může například zahrnovat redundantní distribuční klíč jako součást kritérií spojování pro minimalizaci přesunu dat.

Další výhodou pro zobrazení může být vynucení konkrétního dotazu nebo spojení s nápovědou. Pomocí zobrazení tímto způsobem zaručujete, že se spojení vždy provádějí optimálním způsobem, aby si uživatelé nemuseli pamatovat správnou konstrukci pro jejich spojení.

## <a name="materialized-view"></a>Materializované zobrazení

Materializované zobrazení předběžně počítá, ukládá a udržuje data ve fondu SQL stejně jako tabulka. Při použití materializované zobrazení není nutná žádná recompute.

Vzhledem k načtení dat do základních tabulek fond SQL synchronně aktualizuje materializovaná zobrazení.  Optimalizátor dotazů automaticky používá nasazená materializovaná zobrazení ke zlepšení výkonu dotazů i v případě, že se v dotazu neodkazují na zobrazení.  

Dotazy, které využívají nejvíc z materializovaná zobrazení, jsou složité dotazy (obvykle dotazy s spojeními a agregacemi) ve velkých tabulkách, které vytváří malou sadu výsledků.  

Podrobnosti o syntaxi materializované zobrazení a dalších požadavcích najdete v tématu věnovaném [Vytvoření materializované zobrazení jako příkazu SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Pokyny k ladění dotazů najdete [v ladění výkonu pomocí materializovaná zobrazení](performance-tuning-materialized-views.md).

## <a name="example"></a>Příklad

Běžným vzorem aplikace je opětovné vytvoření tabulek pomocí CREATE TABLE jako SELECT (CTAS) následovaných vzorem přejmenování objektu při načítání dat.  

Následující příklad přidá nové záznamy data do dimenze data. Všimněte si, jak se vytvoří nová tabulka, DimDate_New a pak se přejmenuje, aby se původní verze tabulky nahradila.

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

Tento přístup ale může mít za následek, že se tabulky zobrazí a zmizí ze zobrazení uživatele spolu s vydáním chybové zprávy "Tabulka" neexistuje.

Zobrazení lze použít k poskytnutí konzistentní prezentační vrstvy, zatímco jsou přejmenovány podkladové objekty. Poskytnutím přístupu k datům prostřednictvím zobrazení uživatelé nepotřebují mít přehled o podkladových tabulkách.

Tato vrstva poskytuje konzistentní uživatelské prostředí a současně zajišťuje, aby návrháři datového skladu mohli vyvíjet datový model. Možnost vyvíjet podkladové tabulky znamená, že návrháři můžou pomocí CTAS maximalizovat výkon během procesu načítání dat.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje fondu SQL](sql-data-warehouse-overview-develop.md).
