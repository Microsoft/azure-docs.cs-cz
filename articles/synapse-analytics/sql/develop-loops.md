---
title: Použití smyček T-SQL
description: Tipy pro použití t-SQL smyčky, nahrazení kurzory a vývoj souvisejících řešení s fondem SQL v Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: baff2806b1a8c3c99546365c2496238c24b2b243
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429951"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>Použití t-SQL smyček v Synapse SQL
Tento článek obsahuje základní tipy pro použití t-SQL smyčky, nahrazení kurzory a vývoj souvisejících řešení s fondem SQL v Synapse SQL.

## <a name="purpose-of-while-loops"></a>Účel smyčků WHILE

Synapse SQL podporuje smyčku [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) pro opakované provádění bloků příkazů. Tato smyčka WHILE pokračuje tak dlouho, dokud jsou zadané podmínky pravdivé nebo dokud kód konkrétně neukončí smyčku pomocí klíčového slova BREAK. 

Smyčky ve fondu SQL jsou užitečné pro nahrazení kurzory definované v kódu SQL. Naštěstí téměř všechny kurzory, které jsou napsány v kódu SQL jsou rychlé vpřed, jen pro čtení odrůdy. Takže smyčky [WHILE] jsou skvělou alternativou pro nahrazení kurzorů.

## <a name="replacing-cursors-in-sql-pool"></a>Nahrazení kurzorů ve fondu SQL

Před potápěním v, je třeba zvážit následující otázku: "Mohl by tento kurzor přepsat použít set-založené operace?" V mnoha případech je odpověď ano a je často nejlepším přístupem. Operace založená na sadě často provádí rychleji než iterativní, řádek po řádku přístup.

Kurzory rychlého převíjení jen pro čtení lze snadno nahradit smyčkou. Následující kód je jednoduchý příklad. Tento příklad kódu aktualizuje statistiky pro každou tabulku v databázi. Iterace přes tabulky ve smyčce, každý příkaz provede v pořadí.

Nejprve vytvořte dočasnou tabulku obsahující jedinečné číslo řádku, které slouží k identifikaci jednotlivých příkazů:

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

Za druhé, inicializovat proměnné potřebné k provedení smyčky:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Nyní smyčku přes příkazy jejich provádění jeden po druhém:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Nakonec přetáhněte dočasnou tabulku vytvořenou v prvním kroku

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v [tématu přehled vývoje](develop-overview.md).
