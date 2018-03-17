---
title: "Vytváření oddílů tabulky v SQL Data Warehouse | Microsoft Docs"
description: "Začínáme s vytváření oddílů tabulky v Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: f94bc3770fbd7e707194032cb99c67b09f8a0618
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Vytváření oddílů tabulky v SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Přehled][Overview]
> * [Datové typy][Data Types]
> * [Distribuce][Distribute]
> * [Index][Index]
> * [Oddíl][Partition]
> * [Statistiky][Statistics]
> * [Dočasné][Temporary]
> 
> 

Vytváření oddílů je podporován u všech typů tabulek SQL Data Warehouse; včetně clusterových columnstore, clusterovaný index a haldy.  Vytváření oddílů je podporováno také na všechny typy distribučních, včetně hodnoty hash nebo kruhové dotazování distribuován.  Vytváření oddílů umožňuje vám data rozdělíte do menší skupiny dat a ve většině případů dělení se provádí na sloupci, datum.

## <a name="benefits-of-partitioning"></a>Výhody dělení
Vytváření oddílů využívat data výkonu údržby a dotazů.  Jestli výhody oba, nebo pouze jeden, je závislá na tom, jak načíst data a zda na stejný sloupec lze použít pro obě účely, protože vytváření oddílů je možné provést pouze na jeden sloupec.

### <a name="benefits-to-loads"></a>Výhody zatížení
Hlavní výhoda vytváření oddílů v SQL Data Warehouse je ke zlepšení efektivity a výkon načítání dat pomocí odstranění oddílu, přepínání a slučování.  Ve většině případů jsou data rozdělena na sloupec data, která úzce souvisí pořadí, ve kterém je načíst data do databáze.  Jednou z výhod použití oddíly pro zachování dat největší ho předcházení protokolování transakcí.  Když jednoduše vkládání, aktualizaci nebo odstranění dat může být nejjednodušší způsob, s malým množstvím myšlenku a úsilí, pomocí dělení během procesu vaše zatížení může podstatně zlepšit výkon.

Přepnutí oddílu umožňuje rychle odeberte nebo nahraďte oddíl tabulky.  Tabulka faktů prodeje například může obsahovat jen data po dobu posledních 36 měsíců.  Na konci každého měsíce se odstraní nejstarší měsíc prodejních dat z tabulky.  Tato data může odstranit pomocí příkazu delete k odstranění dat nejstarší měsíc.  Ale odstraňování velké množství dat řádek po řádku příkazem delete může trvat příliš dlouho, stejně jako vytvořit riziko velké transakcí, které trvat dlouhou dobu vrátit zpět, pokud dojde k chybě.  Více optimální metodu je vyřadit nejstarší oddílu data.  Kde odstranění jednotlivých řádků může trvat hodiny, odstraňování celý oddíl může trvat sekund.

### <a name="benefits-to-queries"></a>Výhody pro dotazy
Vytváření oddílů můžete použít také pro zlepšení výkonu dotazů.  Dotaz, který použije filtr na oddílů data můžete omezit prohledávání pouze opravňující oddíly. Tato metoda filtrování můžete vyhnout prohledání úplnou tabulky a kontrolovat pouze podmnožinu dat menší. Se zavedením Clusterované indexy columnstore jsou méně výhodné predikátem odstranění výkonnostních výhod, ale v některých případech může být výhoda dotazy.  Například pokud tabulka faktů prodeje jsou rozděleny do 36 měsíců pomocí pole Datum prodeje a potom se dotazuje tento filtr na datum prodej můžete přeskočit hledání v oddíly, které neodpovídají filtru.

## <a name="partition-sizing-guidance"></a>Pokyny k dimenzování oddílu
Při vytváření oddílů lze použít ke zlepšení výkonu některých scénářích, vytváření tabulku s **příliš mnoho** oddíly může narušit výkonnost za určitých okolností.  Tyto problémy jsou především pro Clusterované tabulky columnstore.  Pro dělení být užitečné, je důležité pochopit, kdy použít vytváření oddílů a počet oddílů pro vytvoření.  Není pevný rychlé pravidlo, kolik oddíly jsou příliš mnoho, závisí na vaše data a oddíly, kolik je načítání současně.  Úspěšné schéma rozdělení oddílů má obvykle desítkami na stovky oddíly, není tisíců.

Při vytváření oddílů v **Clusterové columnstore** tabulky, je důležité zvážit, kolik řádků patří každý oddíl.  Pro optimální komprese a výkon Clusterované tabulky columnstore je potřeba minimálně 1 milionu řádků na distribuce a oddíl.  Před vytvořením oddíly, SQL Data Warehouse každá tabulka již rozdělí na 60 distribuované databáze.  Všechny oddíly přidat do tabulky je kromě distribuce vytvořen na pozadí.  V tomto příkladu, pokud tabulka faktů prodeje obsažené 36 měsíční oddíly a vzhledem k tomu, že SQL Data Warehouse je 60 distribuce, pak tabulky faktů prodeje by měl obsahovat 60 milionu řádků měsíčně nebo 2.1 miliardy řádků při zaplnění všechny měsíce.  Pokud tabulka obsahuje méně než minimální doporučený počet řádků na jeden oddíl, zvažte použití méně oddíly chcete-li zvýšit počet řádků na jeden oddíl.  Viz také [indexování] [ Index] článek, který obsahuje dotazy, které lze spustit v SQL Data Warehouse k vyhodnocení kvality indexy columnstore clusteru.

## <a name="syntax-difference-from-sql-server"></a>Syntaxe rozdíl oproti systému SQL Server
SQL Data Warehouse zavádí způsob, jak definovat oddíly, které je jednodušší než SQL Server.  Vytváření oddílů funkce a schémata nejsou použity v SQL Data Warehouse, jako jsou v systému SQL Server.  Místo toho, které musíte udělat je identifikaci oddílů sloupce a body hranic.  Syntaxe vytváření oddílů se mírně liší v systému SQL Server, se základními koncepty jsou stejné.  SQL Server a SQL Data Warehouse podporují jeden sloupec oddílu na jednu tabulku, která může být pohyboval oddílu.  Další informace o oddílech najdete v tématu [rozdělena na oddíly tabulky a indexy][Partitioned Tables and Indexes].

Následující příklad SQL Data Warehouse rozdělena na oddíly [CREATE TABLE] [ CREATE TABLE] příkaz oddíly tabulka FactInternetSales na sloupci OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrace, vytváření oddílů v systému SQL Server
Pokud chcete migrovat definice oddíl systému SQL Server do SQL Data Warehouse jednoduše:

* Odstranění serveru SQL Server [schéma oddílu][partition scheme].
* Přidat [oddílu funkce] [ partition function] definici tak, aby vaše CREATE TABLE.

Pokud migrujete dělenou tabulku z instance systému SQL Server, následující příkaz SQL můžete zjistit počet řádků, že v každém oddílu.  Uvědomte si, že pokud se v SQL Data Warehouse používá stejnou členitost rozdělení, počet řádků na jeden oddíl faktor 60 snižuje.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Správa zatížení
Jeden aspekt poslední díl okolnosti rozhodnutí oddílu tabulky je [úlohy správy][workload management].  Úlohy správy v SQL Data Warehouse je primárně správy paměti a souběžnosti.  V SQL Data Warehouse je maximální paměť přidělená pro každý distribuční během provádění dotazu se řídí třídy prostředků.  V ideálním případě jsou s ohledem na dalších faktorech, jako je potřebnou velikost paměti pro vytváření Clusterované indexy columnstore dimenzované oddílů.  Clusterovaný benefit indexy columnstore výrazně při jejich přidělení více paměti.  Tedy chcete zajistit, že nové vytvoření oddílu indexu není nedostatek paměti. Při přechodu z výchozí role, smallrc, jeden z jiných rolí, například largerc lze dosáhnout zvýšení množství paměti k dispozici pro dotaz.

Informace o přidělení paměti na jeden distribuční je k dispozici pomocí dotazu na zobrazení dynamické správy Správce zdrojů. Ve skutečnosti vaší přidělení paměti je menší než výsledky následující dotaz. Tento dotaz však poskytuje úroveň pokyny, které můžete použít, když vaše oddíly pro operace správy dat pro definování velikosti.  Pokuste se vyhnout, změna velikosti vašeho oddíly nad rámec poskytovaný třída velmi velké prostředků přidělení paměti. Pokud vaše oddíly růst nad rámec tohoto obrázku, spuštěním riziko přetížení paměti, což pak vede k menší optimální komprese.

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Přepnutí oddílu
SQL Data Warehouse podporuje oddílu rozdělení, sloučení a přepínání. Každá z těchto funkcí je spouštěných pomocí [příkaz ALTER TABLE] [ ALTER TABLE] příkaz.

Přepnout oddíly mezi dvěma tabulkami, musíte zajistit, že oddíly zarovnat na jejich odpovídající hranice a jestli se shodují definice tabulky. Jako zkontrolujte, že omezení nejsou k dispozici pro vynucení rozsahu hodnot v tabulce, zdrojová tabulka musí obsahovat stejné hranice oddílů jako cílová tabulka. Pokud hranice oddílů pak nejsou stejné, pak přepínače oddílu selžou, protože metadata oddílu nebudou synchronizovány.

### <a name="how-to-split-a-partition-that-contains-data"></a>Jak rozdělit oddíl, který obsahuje data
Nejúčinnější metodou rozdělit oddíl, který už obsahuje data je použití `CTAS` příkaz. Pokud je tabulka oddílů Clusterové columnstore, pak oddíl tabulky musí být prázdný předtím, než je možné rozdělit.

Následující příklad vytvoří tabulku oddílů columnstore. Vloží jeden řádek na každý oddíl:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> Vytvořením objektu statistiky je přesnější metadat tabulky. Pokud vynecháte statistiky, SQL Data Warehouse použije výchozí hodnoty. Podrobnosti o statistiky, přečtěte si [statistiky][statistics].
> 
> 

Následující dotaz najde počet řádků pomocí `sys.partitions` katalogu zobrazení:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Toto rozdělení příkaz obdrží chybovou zprávu:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, úroveň 15, State 1, řádek 44 ROZDĚLIT klauzule příkazu ALTER partition se nezdařila, protože oddíl není prázdný.  V lze rozdělit jen prázdné oddíly, když existuje columnstore index v tabulce. Zvažte zakázání index columnstore před spuštěním příkazu ALTER PARTITION příkazu a pak znovu sestavit columnstore index po dokončení příkazu ALTER PARTITION.

Můžete však použít `CTAS` vytvořit novou tabulku pro všechna data.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Jako hranice oddílů jsou v souladu, přepínač je povolená. Zdrojová tabulka to bude nechte prázdný oddílu, který může následně rozdělit.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Vše, co je ponechán je zarovnání dat na nové hranice oddílů pomocí `CTAS`a potom přepnout data zpět do hlavní tabulka.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Po dokončení přesunu dat, je vhodné aktualizovat statistiku v cílové tabulce. Aktualizuje statistické údaje zajistí, že statistiku přesně odrážel novou rozdělení dat v jejich příslušné oddíly.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Tabulka dělení zdrojového kódu
Aby se zabránilo vaše definice tabulky z **koroze** v systému správy zdrojů, je vhodné vzít v úvahu následující postup:

1. Vytvořit tabulku jako tabulku oddílů, ale žádné hodnoty pro oddíl

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

1. `SPLIT` v tabulce v rámci procesu nasazení:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

S tímto přístupem zůstane statické kód ve správě zdrojového kódu a dělení hodnoty hranic mohou být dynamické; vyvíjející se k skladu v čase.

## <a name="next-steps"></a>Další postup
Další informace najdete v článcích na [tabulky přehled][Overview], [tabulky datové typy][Data Types], [distribuci tabulku] [ Distribute], [Indexování tabulku][Index], [zachování statistiky tabulky] [ Statistics] a [Dočasných tabulek][Temporary].  Další informace o osvědčených postupech najdete v tématu [SQL Data Warehouse osvědčené postupy][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[workload management]: ./resource-classes-for-workload-management.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
