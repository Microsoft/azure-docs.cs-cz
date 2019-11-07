---
title: Definování datových typů
description: Doporučení pro definování typů tabulkových dat v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a1d88d8fabd5ff32cc1ca5cca4e2a57a86c8dcb3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692481"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Typy tabulkových dat v Azure SQL Data Warehouse
Doporučení pro definování typů tabulkových dat v Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Jaké jsou datové typy?

SQL Data Warehouse podporuje nejčastěji používané datové typy. Seznam podporovaných datových typů najdete v tématu [datové typy](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) v příkazu CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimalizovat délku řádku
Minimalizace velikosti datových typů zkrátí délku řádku, což vede k lepšímu výkonu dotazů. Použijte nejmenší datový typ, který je vhodný pro vaše data. 

- Vyhněte se definování sloupců znaků s velkou výchozí délkou. Například pokud má nejdelší hodnota 25 znaků, pak sloupec definujte jako VARCHAR (25). 
- Nepoužívejte [NVARCHAR] [NVARCHAR] pouze v případě, že potřebujete VARCHAR.
- Pokud je to možné, použijte NVARCHAR (4000) nebo VARCHAR (8000) místo typu NVARCHAR (MAX) nebo VARCHAR (MAX).

Pokud k načtení tabulek používáte základní externí tabulky, může definovaná délka řádku tabulky překročit 1 MB. Pokud řádek s daty s proměnlivou délkou přesáhne 1 MB, můžete řádek načíst pomocí BCP, ale ne u základny.

## <a name="identify-unsupported-data-types"></a>Identifikace nepodporovaných datových typů
Pokud migrujete databázi z jiné databáze SQL, může dojít k datovým typům, které nejsou podporovány v SQL Data Warehouse. Tento dotaz slouží ke zjištění nepodporovaných datových typů ve vašem existujícím schématu SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Alternativní řešení pro nepodporované datové typy

Následující seznam obsahuje datové typy, které SQL Data Warehouse nepodporuje, a nabízí alternativy, které můžete použít místo nepodporovaných datových typů.

| Nepodporovaný datový typ | Alternativní řešení |
| --- | --- |
| [geometrie](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geografické](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [obrazu](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [textové](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Rozdělí sloupec do několika sloupců se silným typem. |
| [stolní](/sql/t-sql/data-types/table-transact-sql) |Převod na dočasné tabulky. |
| [časové razítko](/sql/t-sql/data-types/date-and-time-types) |Reworking Code pro použití [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) a funkce [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) . Jako výchozí se podporují jenom konstanty, proto CURRENT_TIMESTAMP nejde definovat jako výchozí omezení. Pokud potřebujete migrovat hodnoty verze řádku ze sloupce zadaného časového razítka, použijte [binární](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) nebo [varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) pro hodnoty nenulového řádku a verze null. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [uživatelem definovaný typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Pokud je to možné, převeďte zpátky na nativní datový typ. |
| výchozí hodnoty | Výchozí hodnoty podporují pouze literály a konstanty. |


## <a name="next-steps"></a>Další kroky
Další informace o vývoji tabulek najdete v tématu [Přehled tabulek](sql-data-warehouse-tables-overview.md).
