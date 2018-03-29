---
title: Prostředky pro vývoj datového skladu v Azure | Microsoft Docs
description: Vývoj koncepty, rozhodnutí o návrhu, doporučení a kódování techniky pro SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: develop
ms.date: 03/22/2018
ms.author: jrj;barbkess
ms.openlocfilehash: c81a6f9af556a86f10d2482333fdc98f9cfb1454
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Rozhodnutí o návrhu a kódování techniky pro SQL Data Warehouse
Prohlédněte si prostřednictvím tyto články vývoj lépe pochopit klíčových rozhodnutí, doporučení a kódování techniky pro SQL Data Warehouse.

## <a name="key-design-decisions"></a>Rozhodnutí o návrhu klíče
V následujících článcích zvýrazněte koncepty a rozhodnutí o návrhu pro vývoj distribuované datový sklad SQL Data Warehouse pomocí:

* [Připojení][connections]
* [Souběžnosti][concurrency]
* [Transakce][transactions]
* [Uživatelem definované schémata][user-defined schemas]
* [distribuce tabulky][table distribution]
* [indexy tabulek][table indexes]
* [Tabulka oddílů][table partitions]
* [CTAS][CTAS]
* [Statistiky][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Doporučení pro vývoj a techniky kódování
Tyto články zvýraznit konkrétní kódování techniky, tipy a doporučení pro vývoj SQL Data Warehouse:

* [Uložené procedury][stored procedures]
* [Popisky][labels]
* [zobrazení][views]
* [dočasné tabulky][temporary tables]
* [dynamic SQL][dynamic SQL]
* [ve smyčce][looping]
* [Seskupit podle možnosti][group by options]
* [Přiřazení proměnné][variable assignment]

## <a name="next-steps"></a>Další postup
Další informace najdete v části [příkazů SQL Data Warehouse T-SQL](sql-data-warehouse-reference-tsql-statements.md).

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
