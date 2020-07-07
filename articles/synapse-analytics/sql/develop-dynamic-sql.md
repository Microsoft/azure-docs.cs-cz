---
title: Použití dynamického SQL v synapse SQL
description: Tipy pro použití dynamického SQL v synapse SQL
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: b546e6ba6967edcf41e2830a639e69d436827c40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81429600"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Dynamické SQL v synapse SQL
V tomto článku najdete tipy pro použití dynamického SQL a vývoj řešení pomocí synapse SQL.

## <a name="dynamic-sql-example"></a>Příklad dynamického SQL

Při vývoji kódu aplikace může být nutné použít dynamický jazyk SQL k zajištění flexibilních, obecných a modulárních řešení.

> [!NOTE]
> Fond SQL v tuto chvíli nepodporuje datové typy objektů BLOB. Datové typy objektů BLOB nepodporují velikost řetězců, protože datové typy objektů BLOB zahrnují typy varchar (max) a nvarchar (max). Pokud jste tyto typy v kódu aplikace použili k sestavení velkých řetězců, je nutné rozdělit kód na bloky dat a místo toho použít příkaz EXEC.

Jednoduchý příklad:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Pokud je řetězec krátký, můžete použít [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) jako normální.

> [!NOTE]
> Příkazy spouštěné jako dynamické SQL budou nadále platit pro všechna ověřovací pravidla T-SQL.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje](develop-overview.md).
