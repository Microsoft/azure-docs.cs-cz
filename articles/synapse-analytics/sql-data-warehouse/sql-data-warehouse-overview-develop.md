---
title: Prostředky pro vývoj synapse fondu SQL ve službě Azure synapse Analytics
description: Koncepce vývoje, rozhodování o návrhu, doporučení a techniky kódování pro SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 12f91633834ee98582eaad886b48eb8619378265
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81411677"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Rozhodnutí o návrhu a techniky kódování pro synapse fond SQL ve službě Azure synapse Analytics 
 V tomto článku najdete další materiály, které vám pomůžou lépe pochopit klíčová rozhodnutí pro návrh, doporučení a techniky kódování pro fond SQL ve službě Azure synapse.

## <a name="key-design-decisions"></a>Klíčová rozhodnutí pro návrh
Následující články zvýrazňují koncepty a rozhodnutí o návrhu pro vývoj distribuovaného datového skladu pomocí funkce fondu SQL ve službě Azure synapse:

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
Následující články obsahují konkrétní techniky kódování, tipy a doporučení pro vývoj fondu SQL:

* [uložené procedury](sql-data-warehouse-develop-stored-procedures.md)
* [popisky](sql-data-warehouse-develop-label.md)
* [Náhled](sql-data-warehouse-develop-views.md)
* [dočasné tabulky](sql-data-warehouse-tables-temporary.md)
* [dynamické SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [opakování](sql-data-warehouse-develop-loops.md)
* [možnosti pro seskupení](sql-data-warehouse-develop-group-by-options.md)
* [přiřazení proměnné](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Další kroky
Další referenční informace najdete v tématu [příkazy jazyka T-SQL](sql-data-warehouse-reference-tsql-statements.md).
