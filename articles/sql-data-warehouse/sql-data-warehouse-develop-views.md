---
title: Použití zobrazení T-SQL v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro použití zobrazení T-SQL v Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1fd406243f0f2f5339c4170c4ec17286fcf2ef6d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901710"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Zobrazení v Azure SQL Data Warehouse
Zobrazení lze použít mnoha různými způsoby ke zlepšení kvality řešení. 

Azure SQL Data Warehouse podporuje standardní a materializovaná zobrazení. Obě jsou virtuální tabulky vytvořené pomocí výrazů SELECT a prezentují se dotazům jako logické tabulky. Zobrazení zapouzdřují složitost běžných výpočtů dat a přidávají do výpočtů změny, takže není potřeba přepisovat dotazy.

## <a name="standard-view"></a>Standardní zobrazení
Standardní zobrazení vypočítá data při každém použití zobrazení. Na disku nejsou uložená žádná data. Lidé obvykle používají standardní zobrazení jako nástroj, který pomáhá organizovat logické objekty a dotazy v databázi. Chcete-li použít standardní zobrazení, dotaz musí na něj vytvořit přímý odkaz. Další informace najdete v dokumentaci k [vytváření zobrazení](/sql/t-sql/statements/create-view-transact-sql) .

Zobrazení v SQL Data Warehouse jsou ukládána pouze jako metadata. V důsledku toho nejsou k dispozici následující možnosti:
* Neexistuje možnost vazby schématu.
* Základní tabulky nelze aktualizovat prostřednictvím zobrazení.
* Zobrazení nelze vytvořit přes dočasné tabulky.
* Neexistuje žádná podpora pro pomocné parametry pro rozbalení a rozbalení.
* V SQL Data Warehouse nejsou žádná indexovaná zobrazení.

Standardní zobrazení je možné využít k vykonání spojení optimalizovaného pro výkon mezi tabulkami. Zobrazení může například zahrnovat redundantní distribuční klíč jako součást kritérií spojování pro minimalizaci přesunu dat. Další výhodou pro zobrazení může být vynucení konkrétního dotazu nebo spojení s nápovědou. Pomocí zobrazení tímto způsobem zaručujete, že se spojení vždy provádějí optimálním způsobem, aby si uživatelé nemuseli pamatovat správnou konstrukci pro jejich spojení.

## <a name="materialized-view"></a>Materializované zobrazení
Materializované zobrazení předběžně počítá, ukládá a udržuje data v Azure SQL Data Warehouse stejně jako tabulka. Při použití materializované zobrazení není nutná žádná recompute. Jak jsou data načtena do základních tabulek, Azure SQL Data Warehouse synchronně aktualizuje materializovaná zobrazení.  Optimalizátor dotazů automaticky používá nasazená materializovaná zobrazení ke zlepšení výkonu dotazů i v případě, že se v dotazu neodkazují na zobrazení.  Dotazy, které využívají nejvíc z materializovaná zobrazení, jsou složité dotazy (obvykle dotazy s spojeními a agregacemi) ve velkých tabulkách, které vytváří malou sadu výsledků.  

Podrobnosti o syntaxi materializované zobrazení a dalších požadavcích najdete v tématu věnovaném [Vytvoření materializované zobrazení jako příkazu SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  

Pokyny k ladění dotazů najdete [v ladění výkonu pomocí materializovaná zobrazení](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views). 

## <a name="example"></a>Příklad:
Běžným vzorem aplikace je opětovné vytvoření tabulek pomocí CREATE TABLE jako SELECT (CTAS) následovaných vzorem přejmenování objektu při načítání dat.  Následující příklad přidá nové záznamy data do dimenze data. Všimněte si, že se nejprve vytvoří nová tabulka, DimDate_New a pak se přejmenuje, aby se původní verze tabulky nahradila.

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
Tento přístup ale může mít za následek, že se tabulky zobrazují a zmizí ze zobrazení uživatele a také "tabulka neexistuje". Zobrazení lze použít k poskytnutí konzistentní prezentační vrstvy, zatímco jsou příslušné objekty přejmenovány. Poskytnutím přístupu k datům prostřednictvím zobrazení uživatelé nepotřebují mít přehled o podkladových tabulkách. Tato vrstva poskytuje konzistentní uživatelské prostředí a současně zajišťuje, aby návrháři datového skladu mohli vyvíjet datový model. Možnost vyvíjet podkladové tabulky znamená, že návrháři můžou pomocí CTAS maximalizovat výkon během procesu načítání dat.   

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v části [Přehled vývoje SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


