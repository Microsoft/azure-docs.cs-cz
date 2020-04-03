---
title: Použití smyček T-SQL
description: Tipy pro vývoj řešení pomocí T-SQL smyček a nahrazení kurzorů v fondu Synapse SQL.
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
ms.openlocfilehash: 4cec4801f2a15ebf858f50377c9718fdacac4e29
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619001"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Použití t-SQL smyček v fondu Synapse SQL
Součástí tohoto článku jsou tipy pro vývoj řešení fondu SQL pomocí T-SQL smyčky a nahrazení kurzory.

## <a name="purpose-of-while-loops"></a>Účel smyčků WHILE

Synapse SQL fond podporuje [while](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) smyčky pro opakovaně provádění příkazů bloků. Tato smyčka WHILE pokračuje tak dlouho, dokud jsou zadané podmínky pravdivé nebo dokud kód konkrétně neukončí smyčku pomocí klíčového slova BREAK. 

Smyčky jsou užitečné pro nahrazení kurzory definované v kódu SQL. Naštěstí téměř všechny kurzory, které jsou napsány v kódu SQL jsou rychlé vpřed, jen pro čtení odrůdy. Takže, WHILE smyčky jsou skvělou alternativou pro nahrazení kurzory.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Nahrazení kurzorů ve fondu SYNAPSE SQL
Nicméně, před potápěním v hlavě první byste si měli položit následující otázku: "Mohl by tento kurzor být přepsán, aby používal operace založené na sadě?" 

V mnoha případech je odpověď ano a je často nejlepším přístupem. Operace založená na sadě často provádí rychleji než iterativní, řádek po řádku přístup.

Rychlé převíjení kurzorů jen pro čtení lze snadno nahradit sopakování konstrukce. Následující příklad je jednoduchý. Tento příklad kódu aktualizuje statistiky pro každou tabulku v databázi. Iterace přes tabulky ve smyčce, každý příkaz provede v pořadí.

Nejprve vytvořte dočasnou tabulku obsahující jedinečné číslo řádku, které slouží k identifikaci jednotlivých příkazů:

```
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

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Nyní smyčku přes příkazy jejich provádění jeden po druhém:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Nakonec přetáhněte dočasnou tabulku vytvořenou v prvním kroku

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v [tématu přehled vývoje](sql-data-warehouse-overview-develop.md).

