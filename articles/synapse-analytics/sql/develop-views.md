---
title: Zobrazení T-SQL pomocí synapse SQL
description: Tipy pro používání zobrazení T-SQL a vývoj řešení s Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428651"
---
# <a name="t-sql-views-using-synapse-sql"></a>Zobrazení T-SQL pomocí synapse SQL
V tomto článku najdete tipy pro použití zobrazení T-SQL a vývoj řešení s Synapse SQL. 

## <a name="why-use-views"></a>Proč používat zobrazení?

Zobrazení lze použít v mnoha různými způsoby ke zlepšení kvality vašeho řešení.  Tento článek upozorňuje na několik příkladů, jak obohatit řešení o zobrazení a obsahuje omezení, která je třeba vzít v úvahu.

### <a name="sql-pool---create-view"></a>Fond SQL – vytvoření zobrazení

> [!NOTE]
> **Fond SQL**: Syntaxe pro vytvoření zobrazení není popsána v tomto článku. Další informace naleznete v dokumentaci [create view.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="sql-on-demand-preview---create-view"></a>SQL na vyžádání (náhled) - vytvořit zobrazení

> [!NOTE]
> **SQL na vyžádání**: Syntaxe pro CREATE VIEW není popsána v tomto článku. Další informace naleznete v dokumentaci [create view.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="architectural-abstraction"></a>Architektonická abstrakce

Běžným vzorem aplikace je opětovné vytvoření tabulek pomocí [příkazu CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), po kterém následuje vzor přejmenování objektu při načítání dat.

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
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Mějte na paměti, že tento přístup může mít za následek tabulky objevují a mizí ze zobrazení uživatele a výzvy "tabulka neexistuje" chybové zprávy. Zobrazení lze použít k poskytnutí konzistentní vrstvy prezentace uživatelům, zatímco podkladové objekty jsou přejmenovány.

Tím, že uživatelé poskytují přístup k datům prostřednictvím zobrazení, nepotřebují viditelnost podkladových tabulek. Kromě konzistentního uživatelského prostředí tato vrstva zajišťuje, že návrháři analýz mohou vyvíjet datový model. Schopnost vyvíjet základní tabulky znamená, že návrháři mohou používat CTAS k maximalizaci výkonu během procesu načítání dat.

## <a name="performance-optimization"></a>Optimalizace výkonu

Zobrazení lze také použít k vynucení výkonu optimalizované spojení mezi tabulkami. Zobrazení může například zahrnovat redundantní distribuční klíč jako součást kritérií spojení pro minimalizaci pohybu dat.

Vynucení konkrétnídotaz nebo spojení nápovědy je další výhodou použití zobrazení T-SQL. Jako takové zobrazení schopnost zajišťuje, že spojení jsou vždy prováděny optimálním způsobem. Vyhnete se nutnosti, aby si uživatelé pamatovali správnou konstrukci pro jejich spojení.

## <a name="limitations"></a>Omezení

Zobrazení v Synapse SQL jsou uložena pouze jako metadata. V důsledku toho nejsou k dispozici následující možnosti:

* Neexistuje možnost vazby schématu.
* Základní tabulky nelze aktualizovat v zobrazení.
* Zobrazení nelze vytvořit přes dočasné tabulky
* Neexistuje žádná podpora pro expand / NOEXPAND rady
* V synapse SQL nejsou žádná indexovaná zobrazení.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete [v tématu Přehled vývoje synapse SQL](develop-overview.md).



