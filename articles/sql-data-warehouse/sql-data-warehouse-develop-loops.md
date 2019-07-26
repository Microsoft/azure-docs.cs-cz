---
title: Použití smyček T-SQL v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro použití smyček T-SQL a nahrazování kurzorů v Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e27edcc1383a235fbdb9513066e69e2f680ea2f9
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479625"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Použití smyček T-SQL v SQL Data Warehouse
Tipy pro použití smyček T-SQL a nahrazování kurzorů v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="purpose-of-while-loops"></a>Účel smyčky WHILe

SQL Data Warehouse podporuje smyčku [while](/sql/t-sql/language-elements/while-transact-sql) pro opakované provádění bloků příkazů. Tato smyčka WHILe pokračuje, dokud jsou zadané podmínky pravdivé nebo dokud kód konkrétně neukončí smyčku pomocí klíčového slova BREAK. Smyčky jsou užitečné pro nahrazování kurzorů definovaných v kódu SQL. Naštěstí jsou téměř všechny kurzory, které jsou napsány v kódu SQL, určeny pro rychlý posun, jen pro čtení. Proto jsou smyčky [WHILe] skvělou alternativou pro nahrazování kurzorů.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Nahrazování kurzorů v SQL Data Warehouse
Před začnete nejprve byste však měli klást následující otázky: "Je možné, že tento kurzor bude přepsán pro použití operací založených na sadě?." V mnoha případech je odpověď ano a často se jedná o nejlepší přístup. Operace založená na sadě často provádí rychlejší zpracování více než iterativního přístupu řádku.

Rychlé dopředné kurzory, které jsou jen pro čtení, se dají snadno nahradit konstrukcí smyčky. Následuje jednoduchý příklad. Tento příklad kódu aktualizuje statistiku pro každou tabulku v databázi. Pomocí iterace v tabulkách ve smyčce se každý příkaz provede v pořadí.

Nejdřív vytvořte dočasnou tabulku obsahující číslo jedinečného řádku, které slouží k identifikaci jednotlivých příkazů:

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

Za druhé inicializujte proměnné potřebné k provedení smyčky:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Nyní Projděte přes příkazy, které je spouštějí po jednom v čase:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Nakonec vyřaďte dočasnou tabulku vytvořenou v prvním kroku.

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).

