---
title: Použití smyček T-SQL
description: Tipy pro použití smyček T-SQL, výměna kurzorů a vývoj souvisejících řešení pomocí synapse SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0d83e1305a851bf6bafb6c4c79f5caf73f8e44b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98120882"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>Použití smyček T-SQL s synapse SQL ve službě Azure synapse Analytics

Tento článek poskytuje základní tipy pro používání smyček T-SQL, nahrazování kurzorů a vývoj souvisejících řešení pomocí synapse SQL.

## <a name="purpose-of-while-loops"></a>Účel smyčky WHILe

Synapse SQL podporuje smyčku [while](/sql/t-sql/language-elements/while-transact-sql?preserve-view=true&view=sql-server-ver15) pro opakované provádění bloků příkazů. Tato smyčka WHILe pokračuje, dokud jsou zadané podmínky pravdivé nebo dokud kód konkrétně neukončí smyčku pomocí klíčového slova BREAK. 

Smyčky v synapse SQL jsou užitečné pro nahrazování kurzorů definovaných v kódu SQL. Naštěstí jsou téměř všechny kurzory, které jsou napsány v kódu SQL, určeny pro rychlý posun, jen pro čtení. Takže zatímco smyčky jsou skvělou alternativou pro nahrazování kurzorů.

## <a name="replace-cursors-in-synapse-sql"></a>Nahrazení kurzorů v synapse SQL

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