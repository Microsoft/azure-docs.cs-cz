---
title: Datové typy tabulek v synapse SQL
description: Doporučení pro definování datových typů tabulek v synapse SQL
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 789976949f23467801589343483ebcad11fa5c56
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032890"
---
# <a name="table-data-types-in-synapse-sql"></a>Datové typy tabulek v synapse SQL

Doporučení pro definování datových typů tabulek v synapse SQL 

## <a name="data-types"></a>Typy dat

Synapse SQL podporuje nejčastěji používané datové typy. Seznam podporovaných datových typů najdete v tématu [datové typy](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) v příkazu CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimalizovat délku řádku

Minimalizace velikosti datových typů zkrátí délku řádku, což vede k lepšímu výkonu dotazů. Použijte nejmenší datový typ, který je vhodný pro vaše data.

- Vyhněte se definování sloupců znaků s velkou výchozí délkou. Například pokud má nejdelší hodnota 25 znaků, pak sloupec definujte jako VARCHAR (25).
- Nepoužívejte [NVARCHAR] [NVARCHAR] pouze v případě, že potřebujete VARCHAR.
- Pokud je to možné, použijte NVARCHAR (4000) nebo VARCHAR (8000) místo typu NVARCHAR (MAX) nebo VARCHAR (MAX).

> [!NOTE]
> Pokud používáte základní externí tabulky k načtení tabulek fondu SQL, definovaná délka řádku tabulky nesmí překročit 1 MB. Pokud řádek s daty s proměnlivou délkou přesáhne 1 MB, můžete řádek načíst pomocí BCP, ale ne u základny.

## <a name="identify-unsupported-data-types"></a>Identifikace nepodporovaných datových typů

Pokud migrujete databázi z jiné databáze SQL, může dojít k datovým typům, které nejsou podporovány v synapse SQL. Tento dotaz slouží ke zjištění nepodporovaných datových typů ve vašem existujícím schématu SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Alternativní řešení pro nepodporované datové typy

Následující seznam obsahuje datové typy, které synapse SQL nepodporuje, a nabízí alternativy, které můžete použít místo nepodporovaných datových typů.

| Nepodporovaný datový typ | Alternativní řešení |
| --- | --- |
| [geometrie](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [geografické](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [obrazu](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [textové](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Rozdělí sloupec do několika sloupců se silným typem. |
| [stolní](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Pokud používáte fond SQL, můžete převést na dočasné tabulky. Pokud používáte SQL (Preview), můžete zvážit ukládání dat do úložiště pomocí [CETAS](../sql/develop-tables-cetas.md). |
| [časové razítko](/sql/t-sql/data-types/date-and-time-types) |Reworking Code pro použití [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a funkce [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . Jako výchozí se podporují jenom konstanty, proto current_timestamp nejde definovat jako výchozí omezení. Pokud potřebujete migrovat hodnoty verze řádku ze sloupce zadaného časového razítka, použijte [binární](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) nebo [varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) pro hodnoty nenulového řádku a verze null. |
| [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [uživatelem definovaný typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Pokud je to možné, převeďte zpátky na nativní datový typ. |
| výchozí hodnoty | Výchozí hodnoty podporují pouze literály a konstanty. |

## <a name="next-steps"></a>Další kroky

Další informace o vývoji tabulek najdete v tématu [Přehled tabulek](develop-overview.md).
