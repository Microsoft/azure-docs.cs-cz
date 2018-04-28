---
title: Pomocí smyčky T-SQL v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro pomocí smyčky T-SQL a nahrazení kurzory v Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8d51c8f18d7c00d21fcc057efcda73e2a6b46cc7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Pomocí smyčky T-SQL v SQL Data Warehouse
Tipy pro pomocí smyčky T-SQL a nahrazení kurzory v Azure SQL Data Warehouse na vývoj řešení.

## <a name="purpose-of-while-loops"></a>Účelem při smyčky

Podporuje SQL Data Warehouse [při](/sql/t-sql/language-elements/while-transact-sql) smyčky pro opakovaně provádění blok příkazu. Tuto CHVÍLI smyčky pokračuje pro tak dlouho, dokud k zadaným podmínkám mají hodnotu true, nebo dokud se kód ukončí smyčky pomocí klíčového slova přerušení. Smyčky jsou užitečné pro nahrazení kurzory definované v kódu SQL. Naštěstí jsou téměř všechny kurzory, které jsou zapsány v kód SQL z různých rychloposuv vpřed, jen pro čtení. Proto [] smyčky jsou SICE skvělou alternativou k nahrazení kurzory.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Nahrazení kurzory v SQL Data Warehouse
Ale, než začnete v head nejdřív měli byste požádat sami následující otázku: "by mohla používat na základě sady operace být přepsána tohoto kurzoru?." V mnoha případech odpovědi je Ano a je často nejlepším přístupem. Operace na základě sady často provede rychleji než přístup iterativní, po řádcích.

Rychloposuv vpřed kurzory jen pro čtení můžete snadno nahradit opakování konstrukce. Zde je jednoduchý příklad. Tento příklad kódu aktualizuje statistiku pro každou tabulku v databázi. Podle iterování přes tabulky v smyčky, každý příkaz spustí v pořadí.

Nejprve vytvořte dočasnou tabulku obsahující řádek jedinečné číslo, které používají k identifikaci jednotlivých příkazy:

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

Druhý inicializace proměnné potřebná k provedení smyčka:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Nyní smyčku příkazy provádění jeden současně:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Nakonec vyřaďte dočasné tabulky vytvořené v prvním kroku

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj, najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

