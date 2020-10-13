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
ms.openlocfilehash: 5a831d79d315cb71712515eed45d9a679d307e06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760916"
---
# <a name="table-data-types-in-synapse-sql"></a>Datové typy tabulek v synapse SQL

V tomto článku najdete doporučení pro definování datových typů tabulek v synapse SQL. 

## <a name="data-types"></a>Typy dat

Synapse SQL podporuje nejčastěji používané datové typy. Seznam podporovaných datových typů najdete v tématu [datové typy](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes&preserve-view=true) v příkazu CREATE TABLE. 

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
| [geometrie](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [geografické](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Rozdělí sloupec do několika sloupců se silným typem. |
| [table](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Pokud používáte fond SQL, můžete převést na dočasné tabulky. Pokud používáte SQL (Preview), můžete zvážit ukládání dat do úložiště pomocí [CETAS](../sql/develop-tables-cetas.md). |
| [časové razítko](/sql/t-sql/data-types/date-and-time-types) |Reworking Code pro použití [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a funkce [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) . Jako výchozí se podporují jenom konstanty, proto current_timestamp nejde definovat jako výchozí omezení. Pokud potřebujete migrovat hodnoty verze řádku ze sloupce zadaného časového razítka, použijte [binární](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) nebo [varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) pro hodnoty nenulového řádku a verze null. |
| [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [uživatelem definovaný typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Pokud je to možné, převeďte zpátky na nativní datový typ. |
| výchozí hodnoty | Výchozí hodnoty podporují pouze literály a konstanty. |

## <a name="next-steps"></a>Další kroky

Další informace o vývoji tabulek najdete v tématu [Přehled tabulek](develop-overview.md).
