---
title: Použití dynamického SQL
description: Tipy pro použití dynamickésql v Azure SQL Data Warehouse pro vývoj řešení.
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
ms.openlocfilehash: a44bec72029a50c2ef348bcdda497803e35f586d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350559"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamické SQL v datovém skladu SQL
Tipy pro použití dynamickésql v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="dynamic-sql-example"></a>Dynamický příklad SQL

Při vývoji kódu aplikace pro SQL Data Warehouse možná budete muset použít dynamické SQL, které vám pomohou poskytovat flexibilní, obecná a modulární řešení. SQL Data Warehouse v tuto chvíli nepodporuje datové typy objektů blob. Nepodporující datové typy objektů blob může omezit velikost řetězce, protože datové typy objektů blob zahrnují oba typy varchar(max) a nvarchar(max). Pokud jste použili tyto typy v kódu aplikace k vytvoření velkých řetězců, je třeba rozdělit kód na bloky a místo toho použít příkaz EXEC.

Jednoduchý příklad:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Pokud je řetězec krátký, můžete použít [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) jako normální.

> [!NOTE]
> Příkazy provedené jako dynamické SQL budou i nadále podléhat všem ověřovacím pravidlům TSQL.
> 
> 

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v [tématu přehled vývoje](sql-data-warehouse-overview-develop.md).

