---
title: Definování datových typů
description: Doporučení pro definování datových typů tabulek v Azure SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 020b6fc08dad0dacb51215eca6f7baf127a9dba6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351320"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Datové typy tabulek v Datovém skladu Azure SQL
Doporučení pro definování datových typů tabulek v Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Jaké jsou datové typy?

SQL Data Warehouse podporuje nejčastěji používané datové typy. Seznam podporovaných datových typů naleznete v příkazu CREATE TABLE v [tématu datové typy.](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) 

## <a name="minimize-row-length"></a>Minimalizace délky řádku
Minimalizace velikosti datových typů zkracuje délku řádku, což vede k lepšímu výkonu dotazu. Použijte nejmenší datový typ, který funguje pro vaše data. 

- Vyhněte se definování sloupců znaků s velkou výchozí délkou. Pokud je například nejdelší hodnota 25 znaků, definujte sloupec jako VARCHAR(25). 
- Nepoužívejte [NVARCHAR][NVARCHAR], když potřebujete pouze VARCHAR.
- Pokud je to možné, použijte NVARCHAR(4000) nebo VARCHAR(8000) místo NVARCHAR(MAX) nebo VARCHAR(MAX).

Pokud k načtení tabulek používáte externí tabulky PolyBase, definovaná délka řádku tabulky nesmí překročit 1 MB. Pokud řádek s daty proměnné délky překročí 1 MB, můžete načíst řádek s BCP, ale ne s PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifikace nepodporovaných datových typů
Pokud migrujete databázi z jiné databáze SQL, může dojít k datovým typům, které nejsou podporovány v datovém skladu SQL. Tento dotaz slouží ke zjištění nepodporovaných datových typů v existujícím schématu SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Zástupná řešení pro nepodporované datové typy

V následujícím seznamu jsou uvedeny datové typy, které SQL Data Warehouse nepodporuje, a poskytuje alternativy, které můžete použít místo nepodporovaných datových typů.

| Nepodporovaný datový typ | Alternativní řešení |
| --- | --- |
| [Geometrie](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Geografie](/sql/t-sql/spatial-geography/spatial-types-geography) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [Obrázek](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [Ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [Sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Rozdělte sloupec do několika sloupců silného typu. |
| [Tabulka](/sql/t-sql/data-types/table-transact-sql) |Převést na dočasné tabulky. |
| [Časové razítko](/sql/t-sql/data-types/date-and-time-types) |Přepracovat kód pro použití [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) a [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) funkce. Jako výchozí jsou podporovány pouze konstanty, proto current_timestamp nelze definovat jako výchozí omezení. Pokud potřebujete migrovat hodnoty verze řádku ze sloupce zadaného časovým razítkem, použijte [binární](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) nebo [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) pro hodnoty verze řádku NOT NULL nebo NULL. |
| [Xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [uživatelem definovaný typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Pokud je to možné, převeďte zpět na nativní datový typ. |
| výchozí hodnoty | Výchozí hodnoty podporují pouze literály a konstanty. |


## <a name="next-steps"></a>Další kroky
Další informace o vývoji tabulek naleznete v [tématu Přehled tabulky](sql-data-warehouse-tables-overview.md).
