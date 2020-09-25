---
title: Použití smyček T-SQL
description: Tipy pro použití smyček T-SQL, výměna kurzorů a vývoj souvisejících řešení s fondem SQL ve synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 33e1ebc2269ef1db6bb0646f845b09be1a01c724
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289051"
---
# <a name="use-t-sql-loops-in-synapse-sql"></a>Použití smyček T-SQL v synapse SQL
Tento článek vám poskytne základní tipy pro používání smyček T-SQL, nahrazení kurzorů a vývoj souvisejících řešení s fondem SQL ve synapse SQL.

## <a name="purpose-of-while-loops"></a>Účel smyčky WHILe

Synapse SQL podporuje smyčku [while](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15&preserve-view=true) pro opakované provádění bloků příkazů. Tato smyčka WHILe pokračuje, dokud jsou zadané podmínky pravdivé nebo dokud kód konkrétně neukončí smyčku pomocí klíčového slova BREAK. 

Smyčky ve fondu SQL jsou užitečné pro nahrazování kurzorů definovaných v kódu SQL. Naštěstí jsou téměř všechny kurzory, které jsou napsány v kódu SQL, určeny pro rychlý posun, jen pro čtení. Takže zatímco smyčky jsou skvělou alternativou pro nahrazování kurzorů.

## <a name="replace-cursors-in-sql-pool"></a>Nahrazení kurzorů ve fondu SQL

Před začnete v je třeba zvážit následující otázku: "Chcete-li tento kurzor přepsat, aby používal operace založené na nastavení?" V mnoha případech je odpověď ano a často se jedná o nejlepší přístup. Operace založená na sadě se často provádí rychleji než iterativní, řádek po řádku.

Rychlé dopředné kurzory, které jsou jen pro čtení, se snadno nahrazují konstrukcí smyček. Následující kód je jednoduchý příklad. Tento příklad kódu aktualizuje statistiku pro každou tabulku v databázi. Pomocí iterace v tabulkách ve smyčce se každý příkaz provede v pořadí.

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

Za druhé inicializujte proměnné potřebné ke spuštění smyčky:

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

Další tipy pro vývoj najdete v tématu [Přehled vývoje](develop-overview.md).
