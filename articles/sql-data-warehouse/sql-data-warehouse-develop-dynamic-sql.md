---
title: Použití dynamického SQL
description: Tipy pro použití dynamického SQL v Azure SQL Data Warehouse pro vývoj řešení
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 87320128537a235e8047a2f826b0e59c08aef76b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692838"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamické SQL v SQL Data Warehouse
Tipy pro použití dynamického SQL v Azure SQL Data Warehouse pro vývoj řešení

## <a name="dynamic-sql-example"></a>Příklad dynamického SQL

Při vývoji kódu aplikace pro SQL Data Warehouse může být nutné použít dynamický jazyk SQL k zajištění flexibilních, obecných a modulárních řešení. SQL Data Warehouse v tuto chvíli nepodporuje datové typy objektů BLOB. Datové typy objektů BLOB nepodporují velikost řetězců, protože datové typy objektů BLOB zahrnují typy varchar (max) a nvarchar (max). Pokud jste tyto typy v kódu aplikace použili k sestavení velkých řetězců, je nutné rozdělit kód na bloky dat a místo toho použít příkaz EXEC.

Jednoduchý příklad:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Pokud je řetězec krátký, můžete použít [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) jako normální.

> [!NOTE]
> Příkazy spouštěné jako dynamické SQL budou nadále platit pro všechna ověřovací pravidla TSQL.
> 
> 

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).

