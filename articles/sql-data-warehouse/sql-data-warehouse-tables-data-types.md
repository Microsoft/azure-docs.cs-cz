---
title: Definování typů dat – Azure SQL Data Warehouse | Microsoft Docs
description: Doporučení pro definování typů tabulek dat v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 4d8a222a6d4cfa4138fe833fb4e9cc895dbc5f65
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523502"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tabulka datových typů v Azure SQL Data Warehouse
Doporučení pro definování typů tabulek dat v Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Jaké jsou typy dat?

SQL Data Warehouse podporuje nejčastěji používané datové typy. Seznam podporované datové typy, naleznete v části [datové typy](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) v příkazu CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimalizovat délka řádku
Minimalizace velikost datových typů zkracuje délku řádku, což vede k lepší výkon dotazů. Použijte nejmenší datový typ, který funguje pro vaše data. 

- Nedefinujte znak sloupce s délkou velké výchozí. Například pokud nejdelší hodnota je 25 znaků, pak definujte vaše sloupec jako VARCHAR(25). 
- Nepoužívejte [NVARCHAR] [NVARCHAR], když potřebujete VARCHAR.
- Pokud je to možné, použijte místo NVARCHAR(MAX) nebo VARCHAR(MAX) NVARCHAR(4000) nebo VARCHAR(8000).

Pokud používáte externí tabulky PolyBase načíst vaše tabulky, nesmí překročit definované délka řádku tabulky 1 MB. Pokud řádek s proměnnou délkou dat překročí 1 MB, můžete načíst řádek pomocí BCP, ale ne pomocí funkce PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifikovat nepodporované datové typy
Pokud migrujete z jiné databáze SQL databáze, můžete se setkat datové typy, které nejsou podporované v SQL Data Warehouse. Pomocí tohoto dotazu ke zjištění nepodporované datové typy ve vaší stávající schématu SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Alternativní řešení nepodporované datové typy

Následující seznam obsahuje typy dat, že SQL Data Warehouse nepodporuje a alternativy, které můžete použít místo nepodporované datové typy.

| Nepodporovaný datový typ. | Alternativní řešení |
| --- | --- |
| [Geometrie](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [ID hierarchie](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [Bitové kopie](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [SQL_VARIANT](/sql/t-sql/data-types/sql-variant-transact-sql) |Rozdělí sloupec do několika sloupců silného typu. |
| [Tabulka](/sql/t-sql/data-types/table-transact-sql) |Převeďte na dočasné tabulky. |
| [časové razítko](/sql/t-sql/data-types/date-and-time-types) |Přepracování kódu pro použití [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) a [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) funkce. Jsou podporovány pouze konstanty jako výchozí, proto current_timestamp nelze definovat jako výchozí omezení. Pokud potřebujete migrovat hodnoty verze řádků z typu sloupec časového razítka, použijte [binární](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) nebo [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) pro není NULL nebo hodnota NULL, řádek hodnoty verze. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [uživatelsky definovaný typ.](/sql/relational-databases/native-client/features/using-user-defined-types) |Převeďte zpátky na nativní datový typ, pokud je to možné. |
| výchozí hodnoty | Výchozí hodnoty podporují literály a pouze konstanty. |


## <a name="next-steps"></a>Další postup
Další informace o vývoji tabulky najdete v tématu [tabulky přehled](sql-data-warehouse-tables-overview.md).
