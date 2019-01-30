---
title: Dělení tabulky ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Doporučení a příklady použití oddíly tabulky ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 60f475afd8e9d599d3771b875f15a29e8a082fb7
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245884"
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Dělení tabulek v SQL Data Warehouse
Doporučení a příklady použití oddíly tabulky ve službě Azure SQL Data Warehouse.

## <a name="what-are-table-partitions"></a>Jaké jsou oddíly tabulky?
Oddíly tabulky umožňují rozdělit do menších skupin dat vaše data. Ve většině případů jsou oddíly tabulky vytvořit u sloupce datum. Vytváření oddílů se podporuje na všech typech tabulka SQL Data Warehouse; jako Clusterované columnstore, clusterovaný index a haldy. Dělení je také podporována na všech typech distribuce, včetně hodnot hash nebo distribuované metodou kruhového.  

Dělení můžete využívat data údržbu a výkonu dotazování umožňujícímu. Určuje, zda jeho výhody plynou oba, nebo pouze jedna je závislé na tom, jak načíst data a zda stejný sloupec lze použít pro oba účely, protože dělení lze provést pouze na jeden sloupec.

### <a name="benefits-to-loads"></a>Výhody pro načtení
Hlavní výhodou dělení ve službě SQL Data Warehouse je zvýšení efektivity a výkon při načítání dat pomocí odstranění oddílů, přepínání a sloučení. Ve většině případů se data rozdělená na sloupec data, která je úzce vázané na pořadí, ve kterém je načtení dat do databáze. Jednou z výhod používání oddílů zachovat data největší ho předcházení protokolování transakcí. Jednoduše vkládání, aktualizaci nebo odstranění dat mohou být nejjednodušší, s trochou myšlenek a úsilí, použití dělení během procesu načtení může výrazně zlepšit výkon.

Přepínání oddílů je možné rychle odeberte nebo nahraďte oddíl tabulky.  Například tabulku faktů sales může obsahovat jen data po dobu posledních 36 měsíců. Na konci každého měsíce se odstraní nejstarší měsíc prodejních dat z tabulky.  Tato data můžou odstranit pomocí příkazu delete odstraníte data po nejstarší měsíc. Ale odstranění velký objem dat řádek po řádku pomocí příkazu delete může trvat příliš dlouho, jakož i riziko možného velké transakce, které trvat dlouhou dobu vrátit zpět, pokud se něco nepovede. Více optimální přístup je odstranit nejstarší oddíl data. Pokud odstranění jednotlivých řádků může trvat hodiny, odstraňuje se celý oddíl může trvat sekund.

### <a name="benefits-to-queries"></a>Výhody pro dotazy
Dělení můžete použít také ke zlepšení výkonu dotazů. Dotaz, který aplikuje filtr dělená data můžete omezit kontrola pouze oprávněným oddíly. Tato metoda filtrování se můžete vyhnout ke skenování celé tabulky a prohledávat pouze podmnožinu dat menší. Se zavedením Clusterované indexy columnstore přinese zlepšení výkonu odstranění predikátu jsou méně užitečné, ale v některých případech může být výhoda pro dotazy. Například pokud tabulce faktů sales rozdělená na 36 měsíců pomocí pole prodejní data, dotazuje filtr na datu, prodej přeskočit hledání v oddílech, které neodpovídají filtr.

## <a name="sizing-partitions"></a>Nastavení velikosti oddíly
I když je dělení můžete použít ke zlepšení výkonu některých scénářích, vytvoření tabulky s **příliš mnoho** oddílů může snížit výkon za určitých okolností.  Tyto problémy jsou především clusterovaných tabulek columnstore. Pro rozdělení do oddílů, bude vhodné, je důležité pochopit, kdy použít dělení a počet oddílů, chcete-li vytvořit. Neexistuje žádné pevné pravidlo rychlé, kolik oddíly jsou příliš mnoho, závisí na vaše data a oddíly, kolik je načítání současně. Úspěšné schéma rozdělení oddílů má obvykle desítky ke stovkám oddíly, ne tisíců.

Při vytváření oddílů na **Clusterované columnstore** tabulky, je důležité vzít v úvahu, kolik řádků patří do jednotlivých oddílů. Pro optimální komprese a výkonu clusterovaných tabulek columnstore je potřeba minimálně 1 milion řádků na distribuci a oddílu. Před vytvořením oddíly, SQL Data Warehouse již rozdělí každou tabulku na 60 distribuované databáze. Žádné dělení přidá do tabulky je kromě distribuce vytvořeny na pozadí. Podle tohoto příkladu, pokud v tabulce faktů sales obsažené 36 měsíční oddíly a vzhledem k tomu, že SQL Data Warehouse má 60 distribucí, pak v tabulce faktů sales by měl obsahovat 60 milionů řádků za měsíc, nebo 2.1 miliard řádků po zaplnění všech měsíců. Pokud tabulka obsahuje méně než minimální doporučený počet řádků na oddíl, zvažte použití menší počet oddílů zvýšit počet řádků na oddíl. Další informace najdete v tématu [indexování](sql-data-warehouse-tables-index.md) článek, který obsahuje dotazy, které můžete posouzení jakosti indexů columnstore clusteru.

## <a name="syntax-differences-from-sql-server"></a>Rozdílů v syntaxi z SQL serveru
SQL Data Warehouse představuje způsob, jak definovat oddílů, který je jednodušší než SQL Server. Rozdělení funkcí a schémata nejsou použity ve službě SQL Data Warehouse, jako jsou v systému SQL Server. Všechno, co musíte udělat místo toho je identifikovat rozdělené sloupce a hranice body. Syntaxe dělení může být mírně liší od SQL serveru, základní koncepty stále platí. SQL Server a SQL Data Warehouse podporuje jeden sloupec oddílu jednu tabulku, která může být rozsah oddílu. Další informace o dělení najdete v tématu [dělené tabulky a indexy](/sql/relational-databases/partitions/partitioned-tables-and-indexes).

V následujícím příkladu [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) příkaz k rozdělení tabulky FactInternetSales na sloupci OrderDateKey:

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

## <a name="migrating-partitioning-from-sql-server"></a>Migrace dělení z SQL serveru
Definice oddílu serveru SQL Server jednoduše migrovat do služby SQL Data Warehouse:

- Odstranit SQL Server [schéma oddílu](/sql/t-sql/statements/create-partition-scheme-transact-sql).
- Přidat [oddílů](/sql/t-sql/statements/create-partition-function-transact-sql) definice do vytvoření tabulky.

Pokud migrujete dělenou tabulku ze instance systému SQL Server, následující příkaz SQL vám umožňují zjistit počet řádků, které v jednotlivých oddílech. Uvědomte si, že pokud stejnou členitost dělení se používá v SQL Data Warehouse, počet řádků na oddíl násobkem 60 snižuje.  

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

## <a name="workload-management"></a>Správa úloh
Je jedním z faktorů konečné zvážit vaše rozhodnutí oddíl tabulky [správu úloh](resource-classes-for-workload-management.md). Správa úloh ve službě SQL Data Warehouse je primárně správu paměti a souběžnosti. Ve službě SQL Data Warehouse je maximální paměť přidělená pro jednotlivé distribuce při provádění dotazu se řídí třídy prostředků. V ideálním případě jsou oddíly velikostí s ohledem na dalších faktorů, jako je vytváření Clusterované indexy columnstore potřebnou velikost paměti. Výhoda indexů columnstore clusteru výrazně při přidělování paměti. Proto budete chtít zajistit, že opětovné sestavení oddílu indexu není nedostatek paměti. Zvýšení množství paměti k dispozici pro dotaz můžete dosáhnout při přechodu z výchozí role smallrc, do jednoho z jiných rolí, jako je například largerc.

Informace o přidělení paměti na distribuci je k dispozici pomocí dotazu na zobrazení dynamické správy Správce zdrojů. Ve skutečnosti vaše přidělení paměti je menší než výsledky následující dotaz. Tento dotaz však poskytuje úroveň pokyny, které můžete použít při nastavování velikosti oddílů pro operace správy data. Snažte se vyhnout třídění oddílů nad rámec přidělení paměti poskytovaný třídou velmi velké skupiny prostředků. Pokud oddíly nárůst na tomto obrázku, vystavujete se riziku tlaku na paměť, což zase vede k méně optimální komprese.

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

## <a name="partition-switching"></a>Přepínání oddílů
SQL Data Warehouse podporuje rozdělení, sloučení a přepínání oddílů. Každá z těchto funkcí provádí pomocí [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) příkazu.

Pokud chcete přepnout oddílů mezi dvěma tabulkami, musíte zajistit, že v jejich příslušných hranic zarovnat oddíly a, odpovídají definici tabulky. Jako zkontrolujte omezení nejsou k dispozici k vynucení rozsahu hodnot v tabulce, zdrojová tabulka musí obsahovat stejné hranice oddílu jako cílovou tabulku. Pokud hranice oddílů pak nejsou stejné, pak přepnutí oddílu selže, protože metadata oddílu se nebudou synchronizovat.

### <a name="how-to-split-a-partition-that-contains-data"></a>Jak rozdělit oddíl, který obsahuje data
Nejefektivnější metoda rozdělit oddíl, který již obsahuje data, je použít `CTAS` příkazu. Pokud Clusterované columnstore se dělené tabulky pak tabulku oddílů musí být prázdný předtím, než je možné rozdělit.

Následující příklad vytvoří tabulku oddílů columnstore. Jeden řádek vloží do jednotlivých oddílů:

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
> Vytvořením objektu statistiky metadat tabulky je přesnější. Pokud vynecháte statistiky, SQL Data Warehouse použije výchozí hodnoty. Podrobnosti o statistiky, najdete v tématu [statistiky](sql-data-warehouse-tables-statistics.md).
> 
> 

Následující dotaz hledá počet řádků s použitím `sys.partitions` katalogu zobrazení:

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

Následující příkaz rozdělení obdrží chybovou zprávu:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg – 35346, úroveň 15, State 1, řádek ROZDĚLIT 44 klauzule příkazu ALTER PARTITION se nezdařila, protože oddíl není prázdný. V lze rozdělit jen prázdné oddíly, při v tabulce existuje columnstore index. Vezměte v úvahu zakázat index columnstore před spuštěním příkazu ALTER PARTITION a pak znovu sestavit columnstore index po dokončení příkazu ALTER PARTITION.

Můžete však použít `CTAS` vytvořit novou tabulku pro data.

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

Jak jsou zarovnány hranice oddílů, je povolený přepínač. Zdrojová tabulka to bude nechte prázdný oddíl, který můžete následně rozdělit.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Vše, co zbývá, je zarovnání dat do nové hranice oddílů pomocí `CTAS`a potom přepněte zpět do hlavní data.

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

Po dokončení přesunu dat, je vhodné aktualizace statistik v cílové tabulce. Aktualizaci statistiky zajišťuje že statistiky přesně odrážet nové rozdělení dat do jejich odpovídajících oddílů.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Tabulka dělení správy zdrojového kódu
Aby se zabránilo vaší definice tabulky z **koroze** v systému správy zdrojů, můžete chtít zvážit následující postup:

1. Vytvoření tabulky jako dělenou tabulku, ale žádné hodnoty oddílu

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
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT` v tabulce jako součást procesu nasazení:

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

S tímto přístupem zůstane statické kódu ve správě zdrojového kódu a mohou být dynamický; dělení hodnoty hranice vývoj s datového skladu v čase.

## <a name="next-steps"></a>Další postup
Další informace o vývoji tabulky, najdete v článcích na [Přehled tabulek](sql-data-warehouse-tables-overview.md).

