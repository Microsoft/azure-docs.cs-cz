---
title: Použití smyček T-SQL
description: Tipy pro použití t-SQL smyčky a nahrazení kurzory v Azure SQL Data Warehouse pro vývoj řešení.
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
ms.openlocfilehash: afb2160cb9b4e34d3d17db86bac9cd3be79886d0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351597"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Použití smyček T-SQL v datovém skladu SQL
Tipy pro použití t-SQL smyčky a nahrazení kurzory v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="purpose-of-while-loops"></a>Účel smyčků WHILE

SQL Data Warehouse podporuje smyčku [WHILE](/sql/t-sql/language-elements/while-transact-sql) pro opakované provádění bloků příkazů. Tato smyčka WHILE pokračuje tak dlouho, dokud jsou zadané podmínky pravdivé nebo dokud kód konkrétně neukončí smyčku pomocí klíčového slova BREAK. Smyčky jsou užitečné pro nahrazení kurzory definované v kódu SQL. Naštěstí téměř všechny kurzory, které jsou napsány v kódu SQL jsou rychlé vpřed, jen pro čtení odrůdy. Proto [WHILE] smyčky jsou skvělou alternativou pro nahrazení kurzory.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Nahrazení kurzorů v datovém skladu SQL
Nicméně, před potápěním v hlavě první byste si měli položit následující otázku: "Mohl by tento kurzor být přepsán, aby používal operace založené na sadě?." V mnoha případech je odpověď ano a je často nejlepším přístupem. Operace založená na sadě často provádí rychleji než iterativní, řádek po řádku přístup.

Rychlé převíjení kurzorů jen pro čtení lze snadno nahradit sopakování konstrukce. Následuje jednoduchý příklad. Tento příklad kódu aktualizuje statistiky pro každou tabulku v databázi. Iterace přes tabulky ve smyčce, každý příkaz provede v pořadí.

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

