---
title: Rozdělení tabulek
description: Doporučení a příklady pro použití oddílů tabulky ve fondu SYNAPse SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 368276f75128c80b8df326a26acf26c841e9f68a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742691"
---
# <a name="partitioning-tables-in-synapse-sql-pool"></a>Dělení tabulek ve fondu Synapse SQL

Doporučení a příklady pro použití oddílů tabulky v fondu SYNApse SQL.

## <a name="what-are-table-partitions"></a>Co jsou oddíly tabulky?

Oddíly tabulky umožňují rozdělit data do menších skupin dat. Ve většině případů jsou oddíly tabulky vytvořeny ve sloupci data. Dělení je podporováno u všech typů tabulek fondu SYNAPse SQL; včetně clusterované columnstore, seskupený index a haldy. Dělení je také podporováno ve všech typech distribuce, včetně hash nebo kruhového dotazování distribuované.  

Dělení může být přínosem pro údržbu dat a výkon dotazu. Zda je výhodné pro oba nebo jen jeden je závislá na tom, jak jsou data načtena a zda stejný sloupec lze použít pro oba účely, protože dělení lze provést pouze na jeden sloupec.

### <a name="benefits-to-loads"></a>Výhody pro zatížení

Primární výhodou dělení v synapse SQL fondu je zlepšit efektivitu a výkon načítání dat pomocí odstranění oddílu, přepínání a slučování. Ve většině případů jsou data rozdělena na sloupec data, který je úzce spjat s pořadím, ve kterém jsou data načtena do databáze. Jednou z největších výhod použití oddílů k udržování dat je zamezení protokolování transakcí. Zatímco jednoduše vkládání, aktualizace nebo odstranění dat může být nejjednodušší přístup, s trochou myšlení a úsilí, pomocí dělení během procesu načítání může podstatně zlepšit výkon.

Přepínání oddílů lze použít k rychlému odebrání nebo nahrazení části tabulky.  Tabulka prodejních faktů může například obsahovat pouze data za posledních 36 měsíců. Na konci každého měsíce je z tabulky odstraněn nejstarší měsíc dat o prodeji.  Tato data mohou být odstraněna pomocí příkazu delete k odstranění dat za nejstarší měsíc. Odstranění velkého množství dat řádek po řádku s příkazem delete však může trvat příliš mnoho času a také vytvořit riziko velkých transakcí, které trvá dlouhou dobu vrácení zpět, pokud se něco pokazí. Optiopnějším přístupem je vyřadit nejstarší oddíl dat. Kde odstranění jednotlivých řádků může trvat hodiny, odstranění celého oddílu může trvat několik sekund.

### <a name="benefits-to-queries"></a>Výhody pro dotazy

Dělení lze také ke zlepšení výkonu dotazu. Dotaz, který použije filtr na dělená data, může omezit prohledávání pouze na kvalifikační oddíly. Tato metoda filtrování se může vyhnout úplnému prohledávání tabulky a pouze skenovat menší podmnožinu dat. Se zavedením indexy clusterované columnstore predikátu odstranění výkon výhody jsou méně prospěšné, ale v některých případech může být přínosem pro dotazy. Pokud je například tabulka prodejních faktů rozdělena do 36 měsíců pomocí pole data prodeje, mohou dotazy filtrované v datu prodeje přeskočit vyhledávání v oddílech, které neodpovídají filtru.

## <a name="sizing-partitions"></a>Velikost oddílů

Zatímco dělení lze použít ke zlepšení výkonu některé scénáře, vytvoření tabulky s **příliš mnoho** oddílů může poškodit výkon za určitých okolností.  Tyto obavy platí zejména pro clusterované columnstore tabulky. Pro dělení být užitečné, je důležité pochopit, kdy použít dělení a počet oddílů k vytvoření. Neexistuje žádné pevné rychlé pravidlo o tom, kolik oddílů je příliš mnoho, záleží na vašich datech a na tom, kolik oddílů načítáte současně. Úspěšné schéma dělení má obvykle desítky až stovky oddílů, ne tisíce.

Při vytváření oddílů v **clusterovaných columnstore** tabulky, je důležité zvážit, kolik řádků patří do každého oddílu. Pro optimální kompresi a výkon clusterovaných tabulek columnstore je potřeba minimálně 1 milion řádků na distribuci a oddíl. Před vytvořením oddílů fond synapse SQL již rozdělí každou tabulku do 60 distribuovaných databází. Všechny dělení přidané do tabulky je navíc k distribuce vytvořené na pozadí. V tomto příkladu pokud tabulka prodejních faktů obsahovala 36 měsíčních oddílů a vzhledem k tomu, že fond SYNAPse SQL má 60 rozdělení, měla by tabulka prodejních faktů obsahovat 60 milionů řádků za měsíc nebo 2,1 miliardy řádků při každém zalidnění všech měsíců. Pokud tabulka obsahuje méně než doporučený minimální počet řádků na oddíl, zvažte použití méně oddílů, aby se zvýšil počet řádků na oddíl. Další informace naleznete v článku [Indexování,](sql-data-warehouse-tables-index.md) který obsahuje dotazy, které můžete posoudit kvalitu indexů clustercolumnstore.

## <a name="syntax-differences-from-sql-server"></a>Rozdíly syntaxe od serveru SQL Server

Synapse SQL fond zavádí způsob, jak definovat oddíly, které je jednodušší než SQL Server. Dělení funkce a schémata nejsou používány v fondu SQL Synapse, jak jsou v SQL Server. Místo toho vše, co musíte udělat, je identifikovat dělený sloupec a hraniční body. Zatímco syntaxe dělení může být mírně odlišné od SQL Server, základní pojmy jsou stejné. SQL Server a Fond SQL Podporuje jeden sloupec oddílu na tabulku, která může být oddíl rozsahu. Další informace o dělení naleznete v tématu [Dělení tabulek a indexů](/sql/relational-databases/partitions/partitioned-tables-and-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Následující příklad používá příkaz [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) k rozdělení tabulky FactInternetSales ve sloupci OrderDateKey:

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

## <a name="migrating-partitioning-from-sql-server"></a>Migrace oddílů ze serveru SQL Server

Migrace definic oddílů serveru SQL Server do fondu SYNAPSE SQL jednoduše:

- Eliminujte [schéma oddílů](/sql/t-sql/statements/create-partition-scheme-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)serveru SQL Server .
- Přidejte definici [funkce oddílu](/sql/t-sql/statements/create-partition-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) do tabulky CREATE TABLE.

Pokud migrujete rozdělenou tabulku z instance serveru SQL Server, následující SQL vám může pomoci zjistit počet řádků, které v každém oddílu. Mějte na paměti, že pokud stejné dělení rozlišovací schopnost se používá v fondu SQL Synapse, počet řádků na oddíl snižuje o faktor 60.  

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

## <a name="partition-switching"></a>Přepínání oddílů

Fond SQL synapse podporuje rozdělení oddílů, slučování a přepínání. Každá z těchto funkcí je spuštěna pomocí příkazu [ALTER TABLE.](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Chcete-li přepínat oddíly mezi dvěma tabulkami, musíte zajistit, aby oddíly zarovnány na jejich příslušné hranice a že definice tabulky odpovídají. Vzhledem k tomu, že kontrolní omezení nejsou k dispozici k vynucení rozsahu hodnot v tabulce, musí zdrojová tabulka obsahovat stejné hranice oddílu jako cílová tabulka. Pokud hranice oddílu nejsou stejné, přepínač oddílu se nezdaří, protože metadata oddílu nebudou synchronizována.

### <a name="how-to-split-a-partition-that-contains-data"></a>Rozdělení oddílu, který obsahuje data

Nejúčinnější metodou rozdělení oddílu, který již obsahuje `CTAS` data, je použití příkazu. Pokud je rozdělená tabulka clusterované columnstore, oddíl tabulky musí být prázdný, aby mohl být rozdělen.

Následující příklad vytvoří tabulku dělení sloupců. Vloží jeden řádek do každého oddílu:

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
```

Následující dotaz vyhledá počet řádků `sys.partitions` pomocí zobrazení katalogu:

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

Msg 35346, Level 15, State 1, Line 44 SPLIT klauzule příkazu ALTER PARTITION se nezdařilo, protože oddíl není prázdný. Pouze prázdné oddíly lze rozdělit v případě, že index columnstore existuje v tabulce. Zvažte zakázání indexu columnstore před vydáním příkazu ALTER PARTITION a potom znovu sestavení indexu columnstore po dokončení oddílu ALTER.

Můžete však `CTAS` vytvořit novou tabulku pro uložení dat.

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

Jako hranice oddílu jsou zarovnány, přepínač je povolen. To ponechá zdrojovou tabulku s prázdným oddílem, který můžete následně rozdělit.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Vše, co zbývá, je zarovnat data `CTAS`k novým hranicím oddílu pomocí aplikace a potom je přepnout zpět do hlavní tabulky.

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

Po dokončení přesunu dat je vhodné aktualizovat statistiky v cílové tabulce. Aktualizace statistiky zajišťuje, že statistiky přesně odrážejí nové rozdělení dat v příslušných oddílech.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Načtení nových dat do oddílů, které obsahují data v jednom kroku

Načítání dat do oddílů s přepínáním oddílů je pohodlný způsob, jak fázi nová data v tabulce, která není viditelná pro uživatele přepínač v nových datech.  To může být náročné na zaneprázdněné systémy řešit zamykání tvrzení spojené s přepnutíoddílu.  Chcete-li vymazat existující data v `ALTER TABLE` oddílu, dříve nutné přepnout data.  Pak `ALTER TABLE` bylo zapotřebí další přepnout v nových datech.  V fondu SYNApse `TRUNCATE_TARGET` SQL je tato `ALTER TABLE` možnost podporována v příkazu.  Příkaz `TRUNCATE_TARGET` `ALTER TABLE` přepíše existující data v oddílu novými daty.  Níže je uveden `CTAS` příklad, který používá k vytvoření nové tabulky s existujícími daty, vloží nová data, pak přepne všechna data zpět do cílové tabulky a přepíše stávající data.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Ovládací prvek zdrojového kódu pro dělení tabulek

Chcete-li zabránit **rezivění** definice tabulky v systému správy zdrojového kódu, můžete zvážit následující přístup:

1. Vytvoření tabulky jako dělené tabulky, ale bez hodnot oddílů

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

1. `SPLIT`v rámci procesu nasazení:

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

S tímto přístupem kód v řízení zdrojového kódu zůstává statický a hodnoty hranice dělení mohou být dynamické; s databází v průběhu času.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji tabulek naleznete v článcích [přehled u tabulky](sql-data-warehouse-tables-overview.md).
