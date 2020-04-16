---
title: Definování datových typů
description: Doporučení pro definování datových typů tabulek v synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 312c58a7df09ebe3e0bdf0749f902199723a331b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429002"
---
# <a name="table-data-types-in-synapse-sql"></a>Datové typy tabulek v synapse SQL

Doporučení pro definování datových typů tabulek v synapse SQL. 

## <a name="what-are-the-data-types"></a>Jaké jsou datové typy?

Synapse SQL podporuje nejčastěji používané datové typy. Seznam podporovaných datových typů naleznete v příkazu CREATE TABLE v [tématu datové typy.](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) 

## <a name="minimize-row-length"></a>Minimalizace délky řádku

Minimalizace velikosti datových typů zkracuje délku řádku, což vede k lepšímu výkonu dotazu. Použijte nejmenší datový typ, který funguje pro vaše data.

- Vyhněte se definování sloupců znaků s velkou výchozí délkou. Pokud je například nejdelší hodnota 25 znaků, definujte sloupec jako VARCHAR(25).
- Nepoužívejte [NVARCHAR][NVARCHAR], když potřebujete pouze VARCHAR.
- Pokud je to možné, použijte NVARCHAR(4000) nebo VARCHAR(8000) místo NVARCHAR(MAX) nebo VARCHAR(MAX).

> [!NOTE]
> Pokud k načtení tabulkových stolů SQL používáte externí tabulky PolyBase, definovaná délka řádku tabulky nesmí překročit 1 MB. Pokud řádek s daty proměnné délky překročí 1 MB, můžete načíst řádek s BCP, ale ne s PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifikace nepodporovaných datových typů

Pokud migrujete databázi z jiné databáze SQL, může dojít k datovým typům, které nejsou podporovány v synapse SQL. Tento dotaz slouží ke zjištění nepodporovaných datových typů v existujícím schématu SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Zástupná řešení pro nepodporované datové typy

V následujícím seznamu jsou uvedeny datové typy, které synapse SQL nepodporuje, a poskytuje alternativy, které můžete použít namísto nepodporovaných datových typů.

| Nepodporovaný datový typ | Alternativní řešení |
| --- | --- |
| [Geometrie](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Geografie](/sql/t-sql/spatial-geography/spatial-types-geography) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [Obrázek](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[Varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Rozdělte sloupec do několika sloupců silného typu. |
| [Tabulka](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Pokud používáte fond SQL, můžete převést na dočasné tabulky. Pokud používáte SQL (preview), můžete zvážit ukládání dat do úložiště pomocí [CETAS](../sql/develop-tables-cetas.md). |
| [Časové razítko](/sql/t-sql/data-types/date-and-time-types) |Přepracovat kód pro použití [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funkce. Jako výchozí jsou podporovány pouze konstanty, proto current_timestamp nelze definovat jako výchozí omezení. Pokud potřebujete migrovat hodnoty verze řádku ze sloupce zadaného časovým razítkem, použijte [binární](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) nebo [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) pro hodnoty verze řádku NOT NULL nebo NULL. |
| [Xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [uživatelem definovaný typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Pokud je to možné, převeďte zpět na nativní datový typ. |
| výchozí hodnoty | Výchozí hodnoty podporují pouze literály a konstanty. |

## <a name="next-steps"></a>Další kroky

Další informace o vývoji tabulek naleznete v [tématu Přehled tabulky](develop-overview.md).
