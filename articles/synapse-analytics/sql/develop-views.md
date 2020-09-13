---
title: Zobrazení T-SQL s využitím synapse SQL
description: Tipy pro použití zobrazení T-SQL a vývoj řešení pomocí synapse SQL
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: fafa0c2e1b02cc49bfb852ed7770b0927b0e9334
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032720"
---
# <a name="t-sql-views-using-synapse-sql"></a>Zobrazení T-SQL s využitím synapse SQL
V tomto článku najdete tipy pro použití zobrazení T-SQL a vývoj řešení pomocí synapse SQL. 

## <a name="why-use-views"></a>Proč používat zobrazení

Zobrazení lze použít mnoha různými způsoby ke zlepšení kvality řešení.  Tento článek popisuje několik příkladů, jak rozšířit vaše řešení pomocí zobrazení a obsahuje omezení, která je potřeba vzít v úvahu.

### <a name="sql-pool---create-view"></a>Fond SQL – vytvoření zobrazení

> [!NOTE]
> **Fond SQL**: syntaxe pro zobrazení pro vytváření není popsána v tomto článku. Další informace najdete v dokumentaci k [vytváření zobrazení](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

### <a name="sql-on-demand-preview---create-view"></a>SQL na vyžádání (Preview) – vytvořit zobrazení

> [!NOTE]
> **SQL na vyžádání**: syntaxe pro zobrazení pro vytváření není popsána v tomto článku. Další informace najdete v dokumentaci k [vytváření zobrazení](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="architectural-abstraction"></a>Abstrakce architektury

Běžným vzorem aplikace je znovu vytvořit tabulky pomocí [Create Table jako SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), za nímž následuje vzor přejmenování objektu při načítání dat.

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
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Mějte na paměti, že tento přístup může mít za následek, že se tabulky zobrazují a zmizí ze zobrazení uživatele a zobrazí se výzva "tabulka neexistuje". Zobrazení lze použít k poskytnutí konzistentní prezentační vrstvy, zatímco jsou přejmenovány podkladové objekty.

Poskytnutím přístupu k datům prostřednictvím zobrazení uživatelé nepotřebují mít přehled o podkladových tabulkách. Kromě konzistentního uživatelského prostředí tato vrstva zajišťuje, aby návrháři analýz mohli vyvíjet datový model. Možnost vyvíjet základní tabulky znamená, že návrháři můžou pomocí CTAS maximalizovat výkon během procesu načítání dat.

## <a name="performance-optimization"></a>Optimalizace výkonu

Zobrazení lze také použít k vykonání spojení optimalizované pro výkon mezi tabulkami. Zobrazení může například zahrnovat redundantní distribuční klíč jako součást kritérií spojování pro minimalizaci přesunu dat.

Vynucení konkrétního dotazu nebo spojení s nápovědou je další výhodou použití zobrazení T-SQL. Díky tomu je schopnost zobrazení zajistit, aby se spojení vždy prováděla optimálním způsobem. Nemusíte uživatelům pamatovat správnou konstrukci pro jejich spojení.

## <a name="limitations"></a>Omezení

Zobrazení v synapse SQL se ukládají jenom jako metadata. V důsledku toho nejsou k dispozici následující možnosti:

* Neexistuje možnost vazby schématu.
* Základní tabulky nejde aktualizovat prostřednictvím zobrazení.
* Zobrazení nejde vytvořit přes dočasné tabulky.
* Neexistuje žádná podpora pro pomocné parametry rozbalení a rozbalení.
* V synapse SQL nejsou žádná indexovaná zobrazení.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje synapse SQL](develop-overview.md).



