---
title: Datové typy pokyny - Azure SQL Data Warehouse | Microsoft Docs
description: Doporučení k definování typů dat, které jsou kompatibilní s SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: ''
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 03/17/2018
ms.author: barbkess
ms.openlocfilehash: dcdcb6eddf35fe3ec4754353452c68cd3e24f907
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="guidance-for-defining-data-types-for-tables-in-sql-data-warehouse"></a>Pokyny pro definování typů dat pro tabulky v SQL Data Warehouse
Tato doporučení použijte k definování typů tabulek dat, které jsou kompatibilní s datovým skladem SQL. Kromě kompatibility minimalizovat velikost datových typů zlepšuje výkon dotazů.

SQL Data Warehouse podporuje nejčastěji používané datové typy. Seznam podporované datové typy, naleznete v části [datové typy](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) v příkazu CREATE TABLE. 


## <a name="minimize-row-length"></a>Minimalizovat délka řádku
Minimalizace velikost datových typů zkracuje délku řádku, což vede k lepší výkon dotazů. Použijte nejmenší datový typ, který funguje pro vaše data. 

- Nedefinujte znak sloupce s délkou velké výchozí. Například pokud nejdelší hodnota je 25 znaků, pak definujte vaše sloupec jako VARCHAR(25). 
- Nepoužívejte [NVARCHAR] [ NVARCHAR] když potřebujete jenom VARCHAR.
- Pokud je to možné, použijte místo NVARCHAR(MAX) nebo VARCHAR(MAX) NVARCHAR(4000) nebo VARCHAR(8000).

Pokud používáte Polybase načíst vaše tabulky, nesmí překročit definované délka řádku tabulky 1 MB. Pokud řádek s proměnnou délkou dat překročí 1 MB, můžete načíst řádek pomocí BCP, ale ne pomocí funkce PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifikovat nepodporované datové typy
Pokud migrujete z jiné databáze SQL databáze, můžete se setkat datové typy, které nejsou podporované v SQL Data Warehouse. Pomocí tohoto dotazu ke zjištění nepodporované datové typy ve vaší stávající schématu SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Alternativní řešení pomocí nepodporované datové typy

Následující seznam obsahuje typy dat, že SQL Data Warehouse nepodporuje a alternativy, které můžete použít místo nepodporované datové typy.

| Nepodporovaný datový typ. | Alternativní řešení |
| --- | --- |
| [Geometrie][geometry] |[varbinary][varbinary] |
| [Geography][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [image][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |Rozdělí sloupec do několika sloupců silného typu. |
| [Tabulka][table] |Převeďte na dočasné tabulky. |
| [časové razítko][timestamp] |Přepracování kódu pro použití [datetime2] [ datetime2] a `CURRENT_TIMESTAMP` funkce.  Jsou podporovány pouze konstanty jako výchozí, proto current_timestamp nelze definovat jako výchozí omezení. Pokud potřebujete migrovat hodnoty verze řádků z typu sloupec časového razítka, použijte [binární][BINARY](8) nebo [VARBINARY][BINARY](8) pro NOT NULL nebo Řádek verze hodnoty NULL. |
| [xml][xml] |[varchar][varchar] |
| [uživatelsky definovaný typ.][user defined types] |Převeďte zpátky na nativní datový typ, pokud je to možné. |
| výchozí hodnoty | Výchozí hodnoty podporují literály a pouze konstanty.  Nedeterministické výrazy nebo funkce, jako například `GETDATE()` nebo `CURRENT_TIMESTAMP`, nejsou podporovány. |


## <a name="next-steps"></a>Další postup
Další informace naleznete v tématu:

- [SQL Data Warehouse osvědčené postupy][SQL Data Warehouse Best Practices]
- [Tabulka – přehled][Overview]
- [Distribuce tabulky][Distribute]
- [Indexování tabulky][Index]
- [Vytváření oddílů tabulky][Partition]
- [Zachování statistiky tabulky][Statistics]
- [Dočasné tabulky][Temporary]

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[user defined types]: https://msdn.microsoft.com/library/ms131694.aspx
