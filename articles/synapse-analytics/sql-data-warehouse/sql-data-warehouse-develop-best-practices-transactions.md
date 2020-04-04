---
title: Optimalizace transakcí
description: Naučte se optimalizovat výkon transakčníkód v Synapse SQL při minimalizaci rizika pro dlouhé vrácení zpět.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0139c581e6660622f1ab6db9f407725816377a6d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633567"
---
# <a name="optimizing-transactions-in-synapse-sql"></a>Optimalizace transakcí v synapse SQL

Naučte se optimalizovat výkon transakčníkód v Synapse SQL při minimalizaci rizika pro dlouhé vrácení zpět.

## <a name="transactions-and-logging"></a>Transakce a protokolování

Transakce jsou důležitou součástí relační databázový stroj. Transakce se používají při úpravě dat. Tyto transakce mohou být explicitní nebo implicitní. Single INSERT, UPDATE a DELETE příkazy jsou všechny příklady implicitní transakce. Explicitní transakce používají BEGIN TRAN, COMMIT TRAN nebo ROLLBACK TRAN. Explicitní transakce se obvykle používají, když více příkazů změny je třeba svázat v jedné atomické jednotky.

Změny v databázi jsou sledovány pomocí protokolů transakcí. Každá distribuce má svůj vlastní transakční protokol. Zápisy transakčního protokolu jsou automatické. Není vyžadována žádná konfigurace. Však zatímco tento proces zaručuje zápis zavést režii v systému. Tento dopad můžete minimalizovat zápisem transakčního kódu. Transakční efektivní kód obecně spadá do dvou kategorií.

* Kdykoli je to možné, používejte minimální konstrukce protokolování
* Zpracování dat pomocí dávky s vymezenou srozsahem, aby se zabránilo jednotného spoje dlouho běžící transakce
* Přijmout vzor přepínání oddílů pro velké změny daného oddílu

## <a name="minimal-vs-full-logging"></a>Minimální vs. úplné protokolování

Na rozdíl od plně protokolovaných operací, které používají transakční protokol ke sledování každé změny řádku, minimálně protokolované operace sledovat přidělení rozsahu a pouze změny metadat dat. Minimální protokolování tedy zahrnuje protokolování pouze informace, které je nutné vrátit zpět transakce po selhání nebo pro explicitní požadavek (ROLLBACK TRAN). Jako mnohem méně informací je sledována v transakčníprotokol, minimálně protokolované operace provádí lépe než podobně velké plně protokolované operace. Navíc protože méně zápisů přejít transakční protokol, je generováno mnohem menší množství dat protokolu a tak je efektivnější více vstupně-v.I/O.

Bezpečnostní limity transakcí se vztahují pouze na plně protokolované operace.

> [!NOTE]
> Minimálně protokolované operace se mohou účastnit explicitních transakcí. Jako všechny změny v struktury přidělení jsou sledovány, je možné vrátit zpět minimálně protokolované operace.

## <a name="minimally-logged-operations"></a>Minimálně zaznamenané operace

Následující operace mohou být minimálně zaznamenány:

* VYTVOŘIT TABULKU JAKO VÝBĚR[(CTAS)](sql-data-warehouse-develop-ctas.md)
* Vložit.. Vyberte
* CREATE INDEX
* ZMĚNIT OBNOVENÍ INDEXU
* DROP INDEX
* Zkrátit tabulku
* DROP TABULKA
* ZMĚNIT ODDÍL PŘEPÍNAČE TABULKY

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Operace interního přesunu dat (například BROADCAST a SHUFFLE) nejsou ovlivněny limitem bezpečnosti transakce.

## <a name="minimal-logging-with-bulk-load"></a>Minimální protokolování s hromadným načtením

CTAS a VLOŽIT... SELECT jsou obě operace hromadného zatížení. Obě jsou však ovlivněny definicí cílové tabulky a závisí na scénáři zatížení. Následující tabulka vysvětluje, kdy jsou hromadné operace plně nebo minimálně zaznamenány:  

| Primární index | Scénář načítání | Režim protokolování |
| --- | --- | --- |
| Halda |Všechny |**Minimální** |
| Seskupený index |Prázdná cílová tabulka |**Minimální** |
| Seskupený index |Načtené řádky se nepřekrývají s existujícími stránkami v cíli |**Minimální** |
| Seskupený index |Načtené řádky se překrývají s existujícími stránkami v cíli |Do bloku |
| Index clusterovaného úložiště sloupců |Velikost dávky >= 102 400 na rozdělení zarovnané do oddílu |**Minimální** |
| Index clusterovaného úložiště sloupců |Velikost dávky < 102 400 na rozdělení zarovnané do oddílu |Do bloku |

Stojí za zmínku, že všechny zápisy aktualizovat sekundární nebo neseskupené indexy budou vždy plně protokolované operace.

> [!IMPORTANT]
> Synapse SQL pool databáze má 60 distribucí. Proto za předpokladu, že všechny řádky jsou rovnoměrně distribuovány a přistání v jednom oddílu, vaše dávka bude muset obsahovat 6,144,000 řádky nebo větší, aby byly minimálně zaznamenány při zápisu do indexu clusterované columnstore. Pokud je tabulka rozdělena na oddíly a řádky, které jsou vloženy hranice oddílu s rozpětím, budete potřebovat 6,144,000 řádků na hranici oddílu za předpokladu, že i rozdělení dat. Každý oddíl v každé distribuci musí nezávisle překročit prahovou hodnotu řádku 102 400, aby byla vložení minimálně přihlášena do distribuce.

Načítání dat do neprázdné tabulky s clusterovaným indexem může často obsahovat směs plně protokolovaných a minimálně protokolovaných řádků. Seskupený index je vyvážený strom (b-strom) stránek. Pokud stránka zapisovaná do již obsahuje řádky z jiné transakce, budou tyto zápisy plně zaznamenány. Pokud je však stránka prázdná, bude zápis na tuto stránku zaznamenán minimálně.

## <a name="optimizing-deletes"></a>Optimalizace odstranění

DELETE je plně protokolovaná operace.  Pokud potřebujete odstranit velké množství dat v tabulce nebo oddílu, často `SELECT` dává větší smysl pro data, která chcete zachovat, která lze spustit jako minimálně protokolovnou operaci.  Chcete-li vybrat data, vytvořte novou tabulku s [CTAS](sql-data-warehouse-develop-ctas.md).  Po vytvoření použijte [přejmenování](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) k prohození staré tabulky s nově vytvořenou tabulkou.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Optimalizace aktualizací

UPDATE je plně protokolovaná operace.  Pokud potřebujete aktualizovat velký počet řádků v tabulce nebo oddílu, může být často mnohem efektivnější použít minimálně protokolované operace, jako je [ctas](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) k tomu.

V příkladu níže byla úplná aktualizace tabulky převedena na CTAS, takže je možné minimální protokolování.

V tomto případě zpětně přidáváme částku slevy k prodeji v tabulce:

```sql
--Step 01. Create a new table containing the "Update".
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Opětovné vytváření velkých tabulek může využívat funkce správy úloh fondu SQL Synapse. Další informace naleznete v [tématu Třídy prostředků pro správu pracovního vytížení](resource-classes-for-workload-management.md).

## <a name="optimizing-with-partition-switching"></a>Optimalizace pomocí přepínání oddílů

Pokud čelí rozsáhlé úpravy uvnitř [oddílu tabulky](sql-data-warehouse-tables-partition.md), pak oddíl přepínání vzor dává smysl. Pokud je změna dat významná a zahrnuje více oddílů, pak iterace přes oddíly dosáhne stejného výsledku.

Postup provedení přepínače oddílů je následující:

1. Vytvoření prázdného oddílu
2. Proveďte "aktualizaci" jako CTAS
3. Přepnutí existujících dat do out tabulky
4. Přepnutí nových dat
5. Vyčištění dat

Chcete-li však pomoci identifikovat oddíly přepnout, vytvořte následující pomocný postup.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id]
                                                AND i.[index_id]        = p.[index_id]
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id]
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Tento postup maximalizuje opětovné použití kódu a udržuje příklad přepínání oddílů kompaktnější.

Následující kód ukazuje kroky uvedené výše k dosažení úplné rutiny přepínání oddílů.

```sql
--Create a partitioned aligned empty table to switch out the data
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimalizace protokolování s malými dávkami

Pro operace s velkými změnami dat může mít smysl rozdělit operaci na bloky nebo dávky pro rozsah jednotky práce.

Následující kód je funkční příklad. Velikost dávky byla nastavena na triviální číslo pro zvýraznění techniky. Ve skutečnosti by velikost dávky byla výrazně větší.

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Pokyny pro pozastavení a změnu měřítka

Synapse SQL umožňuje [pozastavit, obnovit a škálovat](sql-data-warehouse-manage-compute-overview.md) fond SQL na vyžádání. Při pozastavení nebo škálování fondu SQL, je důležité si uvědomit, že všechny transakce za letu jsou ukončeny okamžitě; způsobit vrácení všech otevřených transakcí zpět. Pokud vaše úloha vydala dlouho běžící a neúplné změny dat před pozastavení nebo operace škálování, pak tato práce bude nutné vrátit zpět. Toto zrušení může mít vliv na čas potřebný k pozastavení nebo škálování fondu SQL.

> [!IMPORTANT]
> Oba `UPDATE` `DELETE` a jsou plně protokolované operace, a tak tyto operace vrátit a znovu může trvat podstatně déle než ekvivalentní minimálně protokolované operace.

Nejlepším scénářem je nechat v transakcích změny dat letu dokončit před pozastavení nebo škálování fondu SQL. Tento scénář však nemusí být vždy praktické. Chcete-li zmírnit riziko dlouhého vrácení zpět, zvažte jednu z následujících možností:

* Přepsat dlouhotrvající operace pomocí [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* Rozdělit operaci na bloky; pracovat na podmnožině řádků

## <a name="next-steps"></a>Další kroky

Další informace o úrovních izolace a transakčních limitech najdete v článku [Transakce v synapse SQL.](sql-data-warehouse-develop-transactions.md)  Přehled dalších doporučených postupů naleznete v tématu [SQL Data Warehouse Best Practices](sql-data-warehouse-best-practices.md).
