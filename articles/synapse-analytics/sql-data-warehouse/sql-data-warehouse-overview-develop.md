---
title: Prostředky pro vývoj vyhrazeného fondu SQL (dříve SQL DW) ve službě Azure synapse Analytics
description: Koncepce vývoje, rozhodnutí o návrhu, doporučení a techniky kódování pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 6b34c70b453c26fe27a51e1aa802564864640cb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96453690"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Rozhodnutí o návrhu a techniky kódování pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics 

 V tomto článku najdete další materiály, které vám pomůžou lépe pochopit klíčová rozhodnutí, doporučení a techniky kódování pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse.

## <a name="key-design-decisions"></a>Klíčová rozhodnutí pro návrh

Následující články zvýrazňují koncepty a rozhodnutí o návrhu pro vývoj distribuovaného datového skladu pomocí vyhrazené funkce fondu SQL (dříve SQL DW) v Azure synapse:

* [připojení](sql-data-warehouse-connect-overview.md)
* [Concurrency](resource-classes-for-workload-management.md)
* [převody](sql-data-warehouse-develop-transactions.md)
* [uživatelsky definovaná schémata](sql-data-warehouse-develop-user-defined-schemas.md)
* [distribuce tabulky](sql-data-warehouse-tables-distribute.md)
* [indexy tabulek](sql-data-warehouse-tables-index.md)
* [oddíly tabulky](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [týkají](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Doporučení pro vývoj a techniky kódování

Následující články obsahují techniky, tipy a doporučení pro konkrétní kódování pro vývoj vyhrazeného fondu SQL (dříve SQL DW):

* [uložené procedury](sql-data-warehouse-develop-stored-procedures.md)
* [popisky](sql-data-warehouse-develop-label.md)
* [Náhled](performance-tuning-materialized-views.md)
* [dočasné tabulky](sql-data-warehouse-tables-temporary.md)
* [dynamické SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [opakování](sql-data-warehouse-develop-loops.md)
* [možnosti pro seskupení](sql-data-warehouse-develop-group-by-options.md)
* [přiřazení proměnné](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Další kroky

Další referenční informace najdete v tématu [příkazy jazyka T-SQL](sql-data-warehouse-reference-tsql-statements.md).
