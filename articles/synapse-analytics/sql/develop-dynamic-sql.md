---
title: Použití dynamického SQL v Synapse SQL
description: Tipy pro použití dynamickéSQL v Synapse SQL.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429600"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Dynamické SQL v Synapse SQL
V tomto článku najdete tipy pro použití dynamické SQL a vývoj řešení pomocí Synapse SQL.

## <a name="dynamic-sql-example"></a>Dynamický příklad SQL

Při vývoji kódu aplikace může být nutné použít dynamické SQL, které vám pomohou poskytovat flexibilní, obecná a modulární řešení.

> [!NOTE]
> Fond SQL v tuto chvíli nepodporuje datové typy objektů blob. Nepodporující datové typy objektů blob může omezit velikost řetězce, protože datové typy objektů blob zahrnují oba typy varchar(max) a nvarchar(max). Pokud jste použili tyto typy v kódu aplikace k vytvoření velkých řetězců, je třeba rozdělit kód na bloky a místo toho použít příkaz EXEC.

Jednoduchý příklad:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Pokud je řetězec krátký, můžete použít [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) jako normální.

> [!NOTE]
> Příkazy provedené jako dynamické SQL budou stále podléhat všem ověřovacím pravidlům T-SQL.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v [tématu přehled vývoje](develop-overview.md).
