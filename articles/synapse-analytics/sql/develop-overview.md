---
title: Prostředky pro vývoj funkcí SQL synapse
description: Koncepce vývoje, rozhodování o návrhu, doporučení a techniky kódování pro synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 85b0137f8d89def2f38ffe82199950c9158888d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070049"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Rozhodnutí o návrhu a techniky kódování pro funkce synapse SQL ve službě Azure synapse Analytics
V tomto článku najdete seznam prostředků pro funkce SQL Pool a SQL na vyžádání (Preview) služby synapse SQL. Doporučené články jsou rozdělené do dvou částí: klíčová rozhodnutí pro návrh a techniky vývoje a kódování.

Cílem těchto článků je pomáhat při vývoji optimálního technického přístupu pro komponenty synapse SQL v rámci synapse Analytics.

## <a name="key-design-decisions"></a>Klíčová rozhodnutí pro návrh
Články níže zvýrazňují koncepty a rozhodnutí o návrhu pro synapse vývoj SQL:

| Článek | Fond SQL | SQL na vyžádání |
| ------- | -------- | ------------- |
| [Připojení](connect-overview.md)                    | Yes | Ano |
| [Třídy prostředků a souběžnost](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Yes    | No |
| [Transakce](develop-transactions.md)              | Yes | No |
| [Schémata definovaná uživatelem](develop-user-defined-schemas.md) | Yes | Ano |
| [Distribuce tabulky](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Yes | No |
| [Indexy tabulky](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Yes | No |
| [Oddíly tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Yes | No |
| [Statistika](develop-tables-statistics.md)            | Yes | Ano |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Yes | No |
| [Externí tabulky](develop-tables-external-tables.md) | Yes | Ano |
| [CETAS](develop-tables-cetas.md)                     | Yes | Ano |


## <a name="recommendations"></a>Doporučení

Níže najdete základní články, které zdůrazňují konkrétní techniky kódování, tipy a doporučení pro vývoj:

| Článek | Fond SQL | SQL na vyžádání |
| ------- | -------- | ------------- |
| [Uložené procedury](develop-stored-procedures.md)  | Yes                | No                      |
| [Štítky](develop-label.md)                           | Yes                | No                      |
| [Zobrazení](develop-views.md)                             | Yes                | Ano                     |
| [Dočasné tabulky](develop-tables-temporary.md)       | Yes                | Ano                     |
| [Dynamické SQL](develop-dynamic-sql.md)                 | Yes                | Ano                     |
| [Cyklické opakování](develop-loops.md)                         | Yes                | Ano                     |
| [Možnosti pro seskupení](develop-group-by-options.md)       | Yes                | No                      |
| [Přiřazení proměnné](develop-variable-assignment.md) | Yes                | Ano                     |

## <a name="next-steps"></a>Další kroky
Další referenční informace najdete v tématu [SQL Pool T-SQL Statements](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

