---
title: Prostředky pro vývoj funkcí SQL synapse
description: Koncepce vývoje, rozhodování o návrhu, doporučení a techniky kódování pro synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 2a24a61e26bdfaba0619e3965e95b1828380a1c8
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322099"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Rozhodnutí o návrhu a techniky kódování pro funkce synapse SQL ve službě Azure synapse Analytics
V tomto článku najdete seznam prostředků pro vyhrazený fond SQL a funkce bez serveru SQL (Preview) synapse SQL. Doporučené články jsou rozdělené do dvou částí: klíčová rozhodnutí pro návrh a techniky vývoje a kódování.

Cílem těchto článků je pomáhat při vývoji optimálního technického přístupu pro komponenty synapse SQL v rámci synapse Analytics.

## <a name="key-design-decisions"></a>Klíčová rozhodnutí pro návrh
Články níže zvýrazňují koncepty a rozhodnutí o návrhu pro synapse vývoj SQL:

| Článek | vyhrazený fond SQL | fond SQL bez serveru |
| ------- | -------- | ------------- |
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

| Článek | vyhrazený fond SQL | fond SQL bez serveru |
| ------- | -------- | ------------- |
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

