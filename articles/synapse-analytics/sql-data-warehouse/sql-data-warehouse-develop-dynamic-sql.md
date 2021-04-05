---
title: Použití dynamického SQL
description: Tipy pro vývojová řešení s využitím dynamického SQL pro vyhrazené fondy SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 18dcdbf5a4840fda3c2689cc3c8c003470bf1c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679597"
---
# <a name="dynamic-sql-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Dynamický SQL pro vyhrazené fondy SQL ve službě Azure synapse Analytics

V tomto článku najdete tipy pro vývojová řešení pomocí dynamického SQL ve vyhrazených fondech SQL.

## <a name="dynamic-sql-example"></a>Příklad dynamického SQL

Při vývoji kódu aplikace pro vyhrazené fondy SQL může být nutné použít dynamický jazyk SQL k zajištění flexibilních, obecných a modulárních řešení. Vyhrazené fondy SQL momentálně v tuto chvíli nepodporují datové typy objektů BLOB.

Datové typy objektů BLOB nepodporují velikost řetězců, protože datové typy objektů BLOB zahrnují typy varchar (max) a nvarchar (max).

Pokud jste tyto typy v kódu aplikace použili k sestavení velkých řetězců, je nutné rozdělit kód na bloky dat a místo toho použít příkaz EXEC.

Jednoduchý příklad:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Pokud je řetězec krátký, můžete použít [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) jako normální.

> [!NOTE]
> Příkazy spouštěné jako dynamické SQL budou nadále platit pro všechna ověřovací pravidla T-SQL.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).
