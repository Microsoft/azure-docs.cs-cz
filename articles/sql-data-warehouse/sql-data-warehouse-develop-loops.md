---
title: Pomocí smyčky T-SQL v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro pomocí smyčky T-SQL a nahrazení kurzory v Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: ''
ms.assetid: f3384b81-b943-431b-bc73-90e47e4c195f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 2e49a0de0e4a6aba6639f7f3100f41c8db254220
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
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

