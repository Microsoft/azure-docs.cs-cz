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
ms.openlocfilehash: 8a770e66120e69271744942899186ece39b2a3c3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479525"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Zobrazení v Azure SQL Data Warehouse
Tipy pro použití zobrazení T-SQL v Azure SQL Data Warehouse pro vývoj řešení. 

## <a name="why-use-views"></a>Proč používat zobrazení?
Zobrazení lze použít mnoha různými způsoby ke zlepšení kvality řešení.  Tento článek popisuje několik příkladů, jak rozšířit vaše řešení pomocí zobrazení a také omezení, která je potřeba vzít v úvahu.


> [!IMPORTANT]
> Podívejte se na novou vyhodnocenou syntaxi zobrazení v části [vytvořit materializovaná zobrazení jako vybrat](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  Další informace najdete v poznámkách k [verzi](/azure/sql-data-warehouse/release-notes-10-0-10106-0).
>


> [!NOTE]
> V tomto článku není popsána syntaxe pro zobrazení pro vytváření. Další informace najdete v dokumentaci k [vytváření zobrazení](/sql/t-sql/statements/create-view-transact-sql) .
> 

## <a name="architectural-abstraction"></a>Abstrakce architektury

Běžným vzorem aplikace je opětovné vytvoření tabulek pomocí CREATE TABLE jako SELECT (CTAS) následovaných vzorem přejmenování objektu při načítání dat.

Následující příklad přidá nové záznamy data do dimenze data. Všimněte si, že se nejprve vytvoří nová tabulka, DimDate_New a pak se přejmenuje, aby se původní verze tabulky nahradila.

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

Tento přístup ale může mít za následek, že se tabulky zobrazují a zmizí ze zobrazení uživatele a také "tabulka neexistuje". Zobrazení lze použít k poskytnutí konzistentní prezentační vrstvy, zatímco jsou příslušné objekty přejmenovány. Poskytnutím přístupu k datům prostřednictvím zobrazení uživatelé nepotřebují mít přehled o podkladových tabulkách. Tato vrstva poskytuje konzistentní uživatelské prostředí a současně zajišťuje, aby návrháři datového skladu mohli vyvíjet datový model. Možnost vyvíjet podkladové tabulky znamená, že návrháři můžou pomocí CTAS maximalizovat výkon během procesu načítání dat.   

## <a name="performance-optimization"></a>Optimalizace výkonu
Zobrazení lze také využít k vymáhání spojení optimalizovaného pro výkon mezi tabulkami. Zobrazení může například zahrnovat redundantní distribuční klíč jako součást kritérií spojování pro minimalizaci přesunu dat. Další výhodou pro zobrazení může být vynucení konkrétního dotazu nebo spojení s nápovědou. Pomocí zobrazení tímto způsobem zaručujete, že se spojení vždy provádějí optimálním způsobem, aby si uživatelé nemuseli pamatovat správnou konstrukci pro jejich spojení.

## <a name="limitations"></a>Omezení
Zobrazení v SQL Data Warehouse jsou ukládána pouze jako metadata. V důsledku toho nejsou k dispozici následující možnosti:

* Neexistuje možnost vazby schématu.
* Základní tabulky nelze aktualizovat prostřednictvím zobrazení.
* Zobrazení nelze vytvořit přes dočasné tabulky.
* Neexistuje žádná podpora pro pomocné parametry pro rozbalení a rozbalení.
* V SQL Data Warehouse nejsou žádná indexovaná zobrazení.

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v části [Přehled vývoje SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


