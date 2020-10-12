---
title: Použití smyček T-SQL
description: Tipy pro vývoj řešení pomocí smyček T-SQL a nahrazení kurzorů ve fondu synapse SQL
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
ms.openlocfilehash: 25dad01a54b6ffe08656379340f58e0fe70ec666
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213410"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Použití smyček T-SQL ve fondu synapse SQL

V tomto článku jsou uvedené tipy pro vývoj řešení fondů SQL pomocí smyček T-SQL a nahrazování kurzorů.

## <a name="purpose-of-while-loops"></a>Účel smyčky WHILe

Synapse fond SQL podporuje smyčku [while](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pro opakované provádění bloků příkazů. Tato smyčka WHILe pokračuje, dokud jsou zadané podmínky pravdivé nebo dokud kód konkrétně neukončí smyčku pomocí klíčového slova BREAK.

Smyčky jsou užitečné pro nahrazování kurzorů definovaných v kódu SQL. Naštěstí jsou téměř všechny kurzory, které jsou napsány v kódu SQL, určeny pro rychlý posun, jen pro čtení. Takže zatímco smyčky jsou skvělou alternativou pro nahrazování kurzorů.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Výměna kurzorů ve fondu SQL synapse

Předtím, než začnete v hlavě, byste si ale měli zeptat na následující otázku: "chcete, aby se tento kurzor přepsal pro použití operací založených na nastavení?"

V mnoha případech je odpověď ano a často se jedná o nejlepší přístup. Operace založená na sadě často provádí rychlejší zpracování více než iterativního přístupu řádku.

Rychlé dopředné kurzory, které jsou jen pro čtení, se dají snadno nahradit konstrukcí smyčky. Následující příklad je jednoduchý. Tento příklad kódu aktualizuje statistiku pro každou tabulku v databázi. Pomocí iterace v tabulkách ve smyčce se každý příkaz provede v pořadí.

Nejdřív vytvořte dočasnou tabulku obsahující číslo jedinečného řádku, které slouží k identifikaci jednotlivých příkazů:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Za druhé inicializujte proměnné potřebné k provedení smyčky:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Nyní Projděte přes příkazy, které je spouštějí po jednom v čase:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Nakonec vyřaďte dočasnou tabulku vytvořenou v prvním kroku.

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).
