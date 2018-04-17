---
title: Použití dynamické SQL v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro používání dynamických SQL v Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 260c8b69cbe783c2cf18e40669fe742867ab133d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamické SQL v SQL Data Warehouse
Tipy pro používání dynamických SQL v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="dynamic-sql-example"></a>Příklad dynamické SQL

Při vývoji aplikace kód pro SQL Data Warehouse, možná budete muset použít dynamické sql které nám pomáhají doručovat flexibilní, obecné a modulární řešení. SQL Data Warehouse nepodporuje datové typy objektů blob v tuto chvíli. Nejsou podporovány datové typy objektů blob může být omezení velikosti vašeho řetězce vzhledem k tomu, že typy dat objektu blob obsahují typy varchar(max) a nvarchar(max). Pokud používáte tyto typy vytvářet velké řetězců v kódu aplikace, budete muset rozdělit bloky kódu a místo toho použít příkaz EXEC.

Jednoduchý příklad:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Pokud je krátký řetězec, můžete použít [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) jako normální.

> [!NOTE]
> Příkazy provést, protože dynamické SQL budou platit stále všech ověřovacích pravidel TSQL.
> 
> 

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj, najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

