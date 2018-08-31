---
title: Definování typů dat – Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Doporučení pro definování typy tabulkových dat ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: eb469e6a654414b0411f8c45b73658f99a383751
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306579"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Typy tabulkových dat ve službě Azure SQL Data Warehouse
Doporučení pro definování typy tabulkových dat ve službě Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Jaké jsou typy dat?

SQL Data Warehouse podporuje nejběžněji používané datové typy. Seznam podporované datové typy najdete v tématu [datové typy](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) v příkazu CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimalizovat délka řádku
Minimalizovat velikost datových typů zkrátí délka řádku, což vede k lepší výkon dotazů. Pomocí nejmenšího datového typu, který pracuje pro vaše data. 

- Vyhněte se definování sloupců znakových hodnot s výchozí délku. Například pokud má nejdelší hodnota je 25 znaků, nadefinujte typ sloupce jako VARCHAR(25). 
- Vyhněte se použití [NVARCHAR] [NVARCHAR] Pokud potřebujete jenom VARCHAR.
- Pokud je to možné, použijte namísto se NVARCHAR(MAX) nebo VARCHAR(MAX) NVARCHAR(4000) nebo VARCHAR(8000).

Pokud používáte externí tabulky PolyBase k načtení tabulky, definovaná délka řádku tabulky nesmí překročit 1 MB. Když řádek s proměnnou délkou dat větší než 1 MB, můžete načíst řádek pomocí BCP, ale ne pomocí PolyBase.

## <a name="identify-unsupported-data-types"></a>Určení nepodporované datové typy
Pokud migrujete z jiné databáze SQL vaší databáze, můžete se setkat datové typy, které nejsou podporované ve službě SQL Data Warehouse. Pomocí tohoto dotazu ke zjištění nepodporované datové typy ve stávající schématu SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Alternativní řešení pro nepodporované datové typy

Následující seznam obsahuje datové typy, že SQL Data Warehouse nepodporuje a poskytuje alternativy, které můžete použít namísto nepodporované datové typy.

| Nepodporovaný datový typ. | Alternativní řešení |
| --- | --- |
| [Geometrie](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Zeměpisné oblasti](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [Bitové kopie](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [SQL_VARIANT](/sql/t-sql/data-types/sql-variant-transact-sql) |Rozdělit sloupec do několika sloupců silného typu. |
| [Tabulka](/sql/t-sql/data-types/table-transact-sql) |Převeďte na dočasné tabulky. |
| [Časové razítko](/sql/t-sql/data-types/date-and-time-types) |Přepracujte kód, který použijete [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) a [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) funkce. Jsou podporovány pouze konstanty jako výchozí, proto current_timestamp nelze definovat jako výchozí omezení. Pokud je potřeba migrovat hodnoty verze řádků z zadaný sloupec časového razítka, použijte [binární](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) nebo [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) pro není NULL nebo hodnota NULL, řádek hodnoty verze. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [uživatelem definovaný typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Převeďte zpět do nativního datového typu, pokud je to možné. |
| Výchozí hodnoty | Výchozí hodnoty podporují literály a konstanty pouze. |


## <a name="next-steps"></a>Další postup
Další informace o vývoji tabulky, najdete v části [Přehled tabulek](sql-data-warehouse-tables-overview.md).
