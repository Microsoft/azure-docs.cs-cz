---
title: Zdroje informací pro vývoj datového skladu v Azure
description: Koncepce vývoje, rozhodování o návrhu, doporučení a techniky kódování pro SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a6cb7937bdd5dea9eb1a48b2b350db9077431fe0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645622"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Rozhodnutí o návrhu a techniky kódování pro SQL Data Warehouse
Projděte si tyto články o vývoji, abyste lépe pochopili důležité aspekty rozhodování o návrhu, doporučení a kódovací techniky pro SQL Data Warehouse.

## <a name="key-design-decisions"></a>Klíčová rozhodnutí pro návrh
Následující články zvýrazňují koncepty a rozhodnutí o návrhu pro vývoj distribuovaného datového skladu pomocí SQL Data Warehouse:

* [připojení][connections]
* [Concurrency][concurrency]
* [převody][transactions]
* [uživatelsky definovaná schémata][user-defined schemas]
* [distribuce tabulky][table distribution]
* [indexy tabulek][table indexes]
* [oddíly tabulky][table partitions]
* [CTAS][CTAS]
* [statistiky][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Doporučení pro vývoj a techniky kódování
Tyto články zvýrazňují konkrétní techniky kódování, tipy a doporučení pro vývoj SQL Data Warehouse:

* [uložené procedury][stored procedures]
* [popisky][labels]
* [Náhled][views]
* [dočasné tabulky][temporary tables]
* [dynamické SQL][dynamic SQL]
* [opakování][looping]
* [možnosti pro seskupení][group by options]
* [přiřazení proměnné][variable assignment]

## <a name="next-steps"></a>Další kroky
Další referenční informace najdete v tématu [SQL Data Warehouse příkazů T-SQL](sql-data-warehouse-reference-tsql-statements.md).

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
