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
ms.openlocfilehash: d47b4847a12b63532e44a8a1a47101dd065f811b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96446603"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Rozhodnutí o návrhu a techniky kódování pro funkce synapse SQL ve službě Azure synapse Analytics
V tomto článku najdete seznam prostředků pro vyhrazený fond SQL a funkce fondu SQL bez serveru synapse SQL. Doporučené články jsou rozdělené do dvou částí: klíčová rozhodnutí pro návrh a techniky vývoje a kódování.

Cílem těchto článků je pomáhat při vývoji optimálního technického přístupu pro komponenty synapse SQL v rámci služby Azure synapse Analytics.

## <a name="key-design-decisions"></a>Klíčová rozhodnutí pro návrh
Články níže zvýrazňují koncepty a rozhodnutí o návrhu pro synapse vývoj SQL:

| Článek | vyhrazený fond SQL | Bezserverový fond SQL |
| ------- | -------- | ------------- |
| [Připojení](connect-overview.md)                    | Yes | Yes |
| [Třídy prostředků a souběžnost](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Yes    | No |
| [Transakce](develop-transactions.md)              | Yes | No |
| [Schémata definovaná uživatelem](develop-user-defined-schemas.md) | Yes | Yes |
| [Distribuce tabulky](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Yes | No |
| [Indexy tabulky](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Yes | No |
| [Oddíly tabulky](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Yes | No |
| [Statistika](develop-tables-statistics.md)            | Yes | Yes |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Yes | No |
| [Externí tabulky](develop-tables-external-tables.md) | Yes | Yes |
| [CETAS](develop-tables-cetas.md)                     | Yes | Yes |


## <a name="recommendations"></a>Doporučení

Níže najdete základní články, které zdůrazňují konkrétní techniky kódování, tipy a doporučení pro vývoj:

| Článek | vyhrazený fond SQL | Bezserverový fond SQL |
| ------- | -------- | ------------- |
| [Uložené procedury](develop-stored-procedures.md)  | Yes                | No                      |
| [Popisky](develop-label.md)                           | Yes                | No                      |
| [Zobrazení](develop-views.md)                             | Yes                | Yes                     |
| [Dočasné tabulky](develop-tables-temporary.md)       | Yes                | Yes                     |
| [Dynamické SQL](develop-dynamic-sql.md)                 | Yes                | Yes                     |
| [Cyklické opakování](develop-loops.md)                         | Yes                | Yes                     |
| [Možnosti pro seskupení](develop-group-by-options.md)       | Yes                | No                      |
| [Přiřazení proměnné](develop-variable-assignment.md) | Yes                | Ano                     |

## <a name="next-steps"></a>Další kroky
Další referenční informace najdete v tématu [SQL Pool T-SQL Statements](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

