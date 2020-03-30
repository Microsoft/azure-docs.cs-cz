---
title: Prostředky pro vývoj datového skladu v Azure Synapse Analytics
description: Vývojové koncepty, rozhodnutí o návrhu, doporučení a techniky kódování pro SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: aa0f5fd631dfa3e4deca4853c27a667fcf312fec
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350284"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Návrh rozhodnutí a techniky kódování pro datový sklad v Azure Synapse Analytics 
 V tomto článku najdete další prostředky, které vám pomohou lépe porozumět klíčovým rozhodnutím návrhu, doporučením a technikám kódování pro datový sklad v Azure Synapse.

## <a name="key-design-decisions"></a>Klíčová rozhodnutí o návrhu
Následující články zvýrazňují koncepty a rozhodnutí o návrhu pro vývoj distribuovaného datového skladu pomocí funkce SQL Analytics v Azure Synapse:

* [Připojení](sql-data-warehouse-connect-overview.md)
* [Souběžnost](resource-classes-for-workload-management.md)
* [Transakce](sql-data-warehouse-develop-transactions.md)
* [uživatelem definovaná schémata](sql-data-warehouse-develop-user-defined-schemas.md)
* [rozložení tabulky](sql-data-warehouse-tables-distribute.md)
* [indexy tabulek](sql-data-warehouse-tables-index.md)
* [oddíly tabulky](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [statistiky](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Doporučení pro vývoj a kódovací techniky
Následující články obsahují specifické techniky kódování, tipy a doporučení pro vývoj datového skladu pomocí služby SQL Analytics:

* [uložené procedury](sql-data-warehouse-develop-stored-procedures.md)
* [Popisky](sql-data-warehouse-develop-label.md)
* [Zobrazení](sql-data-warehouse-develop-views.md)
* [dočasné tabulky](sql-data-warehouse-tables-temporary.md)
* [dynamický SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [Opakování](sql-data-warehouse-develop-loops.md)
* [seskupit podle možností](sql-data-warehouse-develop-group-by-options.md)
* [přiřazení proměnných](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Další kroky
Další referenční informace naleznete v tématu [T-SQL statements](sql-data-warehouse-reference-tsql-statements.md).
