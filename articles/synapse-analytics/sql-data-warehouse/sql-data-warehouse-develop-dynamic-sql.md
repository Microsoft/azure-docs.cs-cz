---
title: Použití dynamického SQL
description: Tipy pro vývojová řešení využívající dynamické SQL v fondu Synapse SQL.
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
ms.openlocfilehash: a9280bb8153204f86096cf8249ff053bee3f71cc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633531"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Dynamické SQL v fondu Synapse SQL

Součástí tohoto článku jsou tipy pro vývojová řešení pomocí dynamické SQL ve fondu SQL.

## <a name="dynamic-sql-example"></a>Dynamický příklad SQL

Při vývoji kódu aplikace pro fond SQL může být nutné použít dynamické SQL, které vám pomohou poskytovat flexibilní, obecná a modulární řešení. Fond SQL v tuto chvíli nepodporuje datové typy objektů blob.

Nepodporující datové typy objektů blob může omezit velikost řetězce, protože datové typy objektů blob zahrnují oba typy varchar(max) a nvarchar(max).

Pokud jste použili tyto typy v kódu aplikace k vytvoření velkých řetězců, je třeba rozdělit kód na bloky a místo toho použít příkaz EXEC.

Jednoduchý příklad:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Pokud je řetězec krátký, můžete použít [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) jako normální.

> [!NOTE]
> Příkazy provedené jako dynamické SQL budou stále podléhat všem ověřovacím pravidlům T-SQL.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v [tématu přehled vývoje](sql-data-warehouse-overview-develop.md).
