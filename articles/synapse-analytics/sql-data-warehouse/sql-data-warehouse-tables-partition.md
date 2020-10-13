---
title: Dělení tabulek
description: Doporučení a příklady použití oddílů tabulky v synapse fondu SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ed5c0a140c69e9042fc9b85589719a54b65e985e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88763129"
---
# <a name="partitioning-tables-in-synapse-sql-pool"></a>Dělení tabulek v synapse fondu SQL

Doporučení a příklady použití oddílů tabulky v synapse fondu SQL

## <a name="what-are-table-partitions"></a>Co jsou oddíly tabulky?

Oddíly tabulky umožňují rozdělit data do menších skupin dat. Ve většině případů se oddíly tabulek vytvářejí ve sloupci kalendářních dat. Vytváření oddílů je podporováno u všech typů tabulek Synapsech fondů SQL. včetně clusterovaného columnstore, clusterovaného indexu a haldy. Vytváření oddílů je podporováno také pro všechny typy distribuce, včetně hodnoty hash nebo kruhového dotazování.  

Dělení může přinést údržbu dat a výkon dotazů. Bez ohledu na to, jestli je výhoda nebo jenom jedna, závisí na způsobu načítání dat a na tom, jestli je možné použít pro oba účely stejný sloupec, protože rozdělení na oddíly jde udělat jenom v jednom sloupci.

### <a name="benefits-to-loads"></a>Výhody načtení

Hlavní výhodou dělení v synapse fondu SQL je zlepšení efektivity a výkonu načítání dat pomocí odstranění oddílu, přepínání a sloučení. Ve většině případů jsou data rozdělená na sloupec data, který je úzce svázaný s pořadím, ve kterém jsou data načtena do databáze. Jednou z největších výhod používání oddílů pro zachování dat je zamezení protokolování transakcí. I když stačí vkládat, aktualizovat nebo odstraňovat data, může být nejjednodušším přístupem, přičemž při vytváření oddílů během procesu načítání může podstatně dojít k výraznému zlepšení výkonu.

Přepínání oddílů lze použít pro rychlé odebrání nebo nahrazení oddílu tabulky.  Například tabulka faktů prodeje může obsahovat jenom data za posledních 36 měsíců. Na konci každého měsíce se z tabulky odstraní nejstarší měsíc prodejních dat.  Tato data je možné odstranit pomocí příkazu DELETE a odstranit data po nejstarší měsíc. 

Odstranění velkého množství dat řádek po řádku s příkazem Delete může trvat příliš mnoho času a také může vytvořit riziko velkých transakcí, u kterých dojde k chybnému vrácení zpět, pokud se něco nepovede. Lepším přístupem je vyřazení nejstaršího oddílu dat. V případě, že odstranění jednotlivých řádků může trvat hodiny, může odstranění celého oddílu trvat několik sekund.

### <a name="benefits-to-queries"></a>Výhody pro dotazy

K vylepšení výkonu dotazů je také možné použít dělení na oddíly. Dotaz, který aplikuje filtr na dělená data, může omezit kontrolu jenom na opravňující oddíly. Tato metoda filtrování se může vyhnout úplné kontrole tabulky a kontroluje jenom menší podmnožinu dat. Díky zavedení clusterovaných indexů columnstore jsou výhody eliminace predikátu méně užitečné, ale v některých případech může existovat výhoda pro dotazy. 

Například pokud je tabulka faktů prodeje rozdělena do 36 měsíců pomocí pole Datum prodeje, pak dotazy, které filtrují datum prodeje, mohou přeskočit hledání v oddílech, které se neshodují s filtrem.

## <a name="sizing-partitions"></a>Změna velikosti oddílů

Zatímco rozdělení do oddílů lze využít ke zlepšení výkonu některých scénářů, vytvoření tabulky s **příliš mnoha** oddíly může za určitých okolností snížit výkon.  Tyto otázky jsou obzvláště pravdivé pro clusterované tabulky columnstore. 

Aby bylo vytváření oddílů užitečné, je důležité pochopit, kdy použít dělení a počet oddílů, které se mají vytvořit. Neexistuje žádné pevné rychlé pravidlo, které by bylo příliš mnoho oddílů, záleží na vašich datech a na počtu oddílů, které načítáte současně. Úspěšné schéma rozdělení na oddíly má obvykle desítky na stovky oddílů, nikoli tisíce.

Při vytváření oddílů v **clusterovaných tabulkách columnstore** je důležité vzít v úvahu, kolik řádků patří do jednotlivých oddílů. Pro zajištění optimální komprese a výkonu clusterovaných tabulek columnstore je potřeba minimálně 1 000 000 řádků na distribuci a oddíl. Před vytvořením oddílů už synapse fond SQL každou tabulku rozdělí do 60 distribuovaných databází. 

Všechny oddíly přidané do tabulky jsou navíc k distribucím vytvořeným na pozadí. Pokud v tomto příkladu tabulka faktů prodeje obsahuje 36 měsíčních oddílů a vzhledem k tomu, že synapse fond SQL má 60 distribucí, tabulka faktů prodeje by měla obsahovat 60 000 000 řádků za měsíc nebo 2 100 000 000 řádky, když jsou vyplněny všechny měsíce. Pokud tabulka obsahuje méně než Doporučený minimální počet řádků na oddíl, zvažte použití méně oddílů, aby se zvýšil počet řádků na oddíl. 

Další informace najdete v článku věnovaném [indexování](sql-data-warehouse-tables-index.md) , který obsahuje dotazy, které mohou vyhodnocovat kvalitu indexů columnstore clusteru.

## <a name="syntax-differences-from-sql-server"></a>Rozdíly v syntaxi od SQL Server

Synapse fond SQL zavádí způsob, jak definovat oddíly, které jsou jednodušší než SQL Server. Funkce dělení a schémata se v Synapsem fondu SQL nepoužívají, protože jsou v SQL Server. Místo toho je třeba určit dělený sloupcový a hraniční body. 

I když se syntaxe dělení může mírně lišit od SQL Server, základní koncepty jsou stejné. SQL Server a synapse fond SQL podporují jeden sloupec oddílu na tabulku, což může být oddíl s rozsahem. Další informace o dělení najdete v tématu [dělené tabulky a indexy](/sql/relational-databases/partitions/partitioned-tables-and-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Následující příklad používá příkaz [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) k rozdělení tabulky FactInternetSales do sloupce OrderDateKey:

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

## <a name="migrating-partitioning-from-sql-server"></a>Migrace dělení z SQL Server

Postup migrace SQL Server definice oddílů na synapse fond SQL jednoduše:

- Eliminujte [schéma oddílu](/sql/t-sql/statements/create-partition-scheme-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)SQL Server.
- Přidejte do svého CREATE TABLE definici [funkce oddílu](/sql/t-sql/statements/create-partition-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

Pokud migrujete dělenou tabulku z SQL Server instance, následující SQL vám může pomáhat zjistit počet řádků v jednotlivých oddílech. Mějte na paměti, že pokud se ve synapse fondu SQL používá stejné členitosti oddílů, počet řádků na oddíl se zkrátí o faktor 60.  

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

Synapse fond SQL podporuje rozdělení, slučování a přepínání oddílů. Každá z těchto funkcí je spuštěna pomocí příkazu [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

Chcete-li přepnout oddíly mezi dvěma tabulkami, je nutné zajistit, aby oddíly byly zarovnány na příslušných hranicích a aby definice tabulek odpovídaly. Jelikož omezení CHECK nejsou k dispozici pro vynutit rozsah hodnot v tabulce, zdrojová tabulka musí obsahovat stejné hranice oddílu jako cílová tabulka. Pokud hranice oddílu nejsou stejné, pak přepínač oddílu selže, protože metadata oddílu nebudou synchronizována.

### <a name="how-to-split-a-partition-that-contains-data"></a>Rozdělení oddílu obsahujícího data

Nejúčinnější Metoda rozdělení oddílu, který již obsahuje data, je použití `CTAS` příkazu. Pokud je dělená tabulka clusterovaným objektem columnstore, musí být oddíl tabulky prázdný, aby bylo možné ho rozdělit.

Následující příklad vytvoří dělenou tabulku columnstore. Vloží do každého oddílu jeden řádek:

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

Následující dotaz vyhledá počet řádků pomocí `sys.partitions` zobrazení katalogu:

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

Následující rozdělený příkaz obdrží chybovou zprávu:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Klauzule MSG 35346, Level 15, state 1, line 44 příkaz ALTER PARTITION se nezdařila, protože oddíl není prázdný. V případě, že index columnstore v tabulce existuje, lze rozdělit pouze prázdné oddíly. Před vyvoláním příkazu ALTER PARTITION zvažte možnost zakázat index columnstore a pak znovu sestavit index columnstore po dokončení příkazu ALTER PARTITION.

Můžete ale použít `CTAS` k vytvoření nové tabulky pro uložení dat.

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

Když jsou hranice oddílu zarovnané, je povolen přepínač. Tím se ponechá zdrojová tabulka s prázdným oddílem, který můžete následně rozdělit.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Vše, co je ponecháno, je zarovnání dat na nové hranice oddílu pomocí `CTAS` a poté přepnout data zpět do hlavní tabulky.

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

Po dokončení přesunu dat je vhodné aktualizovat statistiku v cílové tabulce. Aktualizace statistiky zajistí, že statistiky přesně odrážejí novou distribuci dat v příslušných oddílech.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Načtení nových dat do oddílů, které obsahují data v jednom kroku

Načítání dat do oddílů pomocí přepínání oddílů je pohodlný způsob, jak připravit nová data v tabulce, která není viditelná pro uživatele.  Může to být náročné na zaneprázdněné systémy, aby se mohla zabývat kolize uzamčení spojeného s přepínáním oddílů.  

Chcete-li vymazat stávající data v oddílu, je k dismailu `ALTER TABLE` nutné použít k přepnutí dat.  `ALTER TABLE`V nových datech se pak vyžadoval jiný.  

V synapse fondu SQL `TRUNCATE_TARGET` je možnost podporována v `ALTER TABLE` příkazu.  Pomocí `TRUNCATE_TARGET` `ALTER TABLE` příkazu přepíše existující data v oddílu novými daty.  Níže je uveden příklad, který používá `CTAS` k vytvoření nové tabulky s existujícími daty, vkládání nových dat a následné přepínání všech dat zpět do cílové tabulky, která Přepisuje stávající data.

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

### <a name="table-partitioning-source-control"></a>Správa zdrojového kódu pro dělení tabulek

Chcete-li se vyhnout definici tabulky z **rusting** v systému správy zdrojového kódu, je vhodné zvážit následující postup:

1. Vytvoří tabulku jako dělenou tabulku, ale bez hodnot oddílů.

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

1. `SPLIT` tabulka v rámci procesu nasazení:

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

V rámci tohoto přístupu kód ve správě zdrojového kódu zůstává statický a hodnoty hranic pro oddíly můžou být dynamické; vývoj databáze v průběhu času.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji tabulek naleznete v článcích v tématu [Přehled tabulek](sql-data-warehouse-tables-overview.md).
