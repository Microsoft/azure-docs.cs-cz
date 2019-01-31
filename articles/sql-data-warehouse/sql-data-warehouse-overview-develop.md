---
title: Zdroje informací pro vývoj datový sklad v Azure | Dokumentace Microsoftu
description: Koncepty vývoje, rozhodnutí o návrhu, doporučení a technikám kódování pro SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 08/29/2018
ms.author: kevinvngo
ms.reviewer: igorstan
ms.openlocfilehash: 8c04e0409faa3b63a8a2957284ac7aa96740ae03
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468372"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Rozhodnutí o návrhu a technikám kódování pro SQL Data Warehouse
Podívejte se přes tyto články o vývoji pro lepší pochopení klíčových rozhodnutí, doporučení a technikám kódování pro SQL Data Warehouse.

## <a name="key-design-decisions"></a>Rozhodnutí o návrhu klíče
V následujících článcích zvýraznění koncepty a rozhodnutí o návrhu pro vývoj distribuovaných data warehouse s využitím SQL Data Warehouse:

* [Připojení][connections]
* [concurrency][concurrency]
* [Transakce][transactions]
* [schémata definovaná uživatelem][user-defined schemas]
* [distribuce tabulky][table distribution]
* [indexy tabulek][table indexes]
* [Oddíly tabulky][table partitions]
* [CTAS][CTAS]
* [statistiky][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Doporučení pro vývoj a technikám kódování
Tyto články zvýraznit konkrétní technikám kódování, tipy a doporučení pro vývoj služby SQL Data Warehouse:

* [Uložené procedury][stored procedures]
* [popisky][labels]
* [Zobrazení][views]
* [Dočasné tabulky][temporary tables]
* [dynamic SQL][dynamic SQL]
* [opakování ve smyčce][looping]
* [Seskupit podle možnosti][group by options]
* [přiřazení proměnné][variable assignment]

## <a name="next-steps"></a>Další postup
Další informace najdete v části [příkazy SQL Data Warehouse T-SQL](sql-data-warehouse-reference-tsql-statements.md).

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
