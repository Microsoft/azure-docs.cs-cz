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
ms.openlocfilehash: ba6ceec3064c5c876ca899ab58881e23913b9701
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81429015"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Rozhodnutí o návrhu a techniky kódování pro funkce synapse SQL ve službě Azure synapse Analytics
V tomto článku najdete seznam prostředků pro funkce SQL Pool a SQL na vyžádání (Preview) služby synapse SQL. Doporučené články jsou rozdělené do dvou částí: klíčová rozhodnutí pro návrh a techniky vývoje a kódování.

Cílem těchto článků je pomáhat při vývoji optimálního technického přístupu pro komponenty synapse SQL v rámci synapse Analytics.

## <a name="key-design-decisions"></a>Klíčová rozhodnutí pro návrh
Články níže zvýrazňují koncepty a rozhodnutí o návrhu pro synapse vývoj SQL:

|                                                          |   Fond SQL   | SQL na vyžádání |
| -----------------------------------------------------    | ---- | ---- |
| [Připojení](connect-overview.md)                    | Ano | Ano |
| [Třídy prostředků a souběžnost](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ano    | Ne |
| [Transakce](develop-transactions.md)              | Ano | Ne |
| [Schémata definovaná uživatelem](develop-user-defined-schemas.md) | Ano | Ano |
| [Distribuce tabulky](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Ano | Ne |
| [Indexy tabulky](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Ano | Ne |
| [Oddíly tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Ano | Ne |
| [Statistika](develop-tables-statistics.md)            | Ano | Ano |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Ano | Ne |
| [Externí tabulky](develop-tables-external-tables.md) | Ano | Ano |
| [CETAS](develop-tables-cetas.md)                     | Ano | Ano |


## <a name="recommendations"></a>Doporučení

Níže najdete základní články, které zdůrazňují konkrétní techniky kódování, tipy a doporučení pro vývoj:

|                                            | Fond SQL | SQL na vyžádání |
| ------------------------------------------ | ------------------ | ----------------------- |
| [Uložené procedury](develop-stored-procedures.md)  | Ano                | Ne                      |
| [Popisky](develop-label.md)                           | Ano                | Ne                      |
| [Zobrazení](develop-views.md)                             | Ano                | Ano                     |
| [Dočasné tabulky](develop-tables-temporary.md)       | Ano                | Ano                     |
| [Dynamické SQL](develop-dynamic-sql.md)                 | Ano                | Ano                     |
| [Cyklické opakování](develop-loops.md)                         | Ano                | Ano                     |
| [Možnosti pro seskupení](develop-group-by-options.md)       | Ano                | Ne                      |
| [Přiřazení proměnné](develop-variable-assignment.md) | Ano                | Ano                     |

## <a name="next-steps"></a>Další kroky
Další referenční informace najdete v tématu [SQL Pool T-SQL Statements](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

