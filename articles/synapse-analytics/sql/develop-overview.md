---
title: Zdroje informací pro vývoj funkcí Synapse SQL
description: Vývojové koncepty, rozhodnutí o návrhu, doporučení a techniky kódování pro Synapse SQL.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429015"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Návrh rozhodnutí a techniky kódování pro funkce Synapse SQL v Azure Synapse Analytics
V tomto článku najdete seznam prostředků pro sql fondu a SQL na vyžádání (preview) funkce Synapse SQL. Doporučené články jsou rozděleny do dvou částí: Klíčová rozhodnutí o návrhu a vývojové a kódovací techniky.

Cílem těchto článků je pomoci vám vyvinout optimální technický přístup k komponentám Synapse SQL v rámci Synapse Analytics.

## <a name="key-design-decisions"></a>Klíčová rozhodnutí o návrhu
Níže uvedené články zdůrazňují koncepty a rozhodnutí o návrhu pro vývoj Synapse SQL:

|                                                          |   Fond SQL   | SQL na vyžádání |
| -----------------------------------------------------    | ---- | ---- |
| [Připojení](connect-overview.md)                    | Ano | Ano |
| [Třídy zdrojů a souběžnost](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ano    | Ne |
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

Níže najdete základní články, které zdůrazňují specifické techniky kódování, tipy a doporučení pro vývoj:

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
Další referenční informace naleznete v [tématu sql pool T-SQL příkazy](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

