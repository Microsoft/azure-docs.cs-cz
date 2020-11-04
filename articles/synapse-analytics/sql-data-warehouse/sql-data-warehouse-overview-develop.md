---
title: Prostředky pro vývoj vyhrazeného fondu SQL ve službě Azure synapse Analytics
description: Koncepce vývoje, rozhodnutí o návrhu, doporučení a techniky kódování pro vyhrazený fond SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f158655cd7aeba647480325966c7f0cfcf65b13c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322144"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Rozhodnutí o návrhu a techniky kódování pro vyhrazený fond SQL ve službě Azure synapse Analytics 

 V tomto článku najdete další materiály, které vám pomůžou lépe pochopit klíčová rozhodnutí pro návrh, doporučení a techniky kódování pro vyhrazený fond SQL v Azure synapse.

## <a name="key-design-decisions"></a>Klíčová rozhodnutí pro návrh

Následující články zvýrazňují koncepty a rozhodnutí o návrhu pro vývoj distribuovaného datového skladu pomocí vyhrazené funkce fondu SQL ve službě Azure synapse:

* [připojení](../sql/connect-overview.md)
* [Concurrency](resource-classes-for-workload-management.md)
* [převody](sql-data-warehouse-develop-transactions.md)
* [uživatelsky definovaná schémata](sql-data-warehouse-develop-user-defined-schemas.md)
* [distribuce tabulky](sql-data-warehouse-tables-distribute.md)
* [indexy tabulek](sql-data-warehouse-tables-index.md)
* [oddíly tabulky](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [týkají](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Doporučení pro vývoj a techniky kódování

Následující články obsahují konkrétní techniky kódování, tipy a doporučení pro vývoj vyhrazeného fondu SQL:

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
