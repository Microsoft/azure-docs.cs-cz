---
title: Optimalizace transakcí
description: Přečtěte si, jak optimalizovat výkon transakčního kódu ve vyhrazeném fondu SQL a zároveň minimalizovat rizika pro dlouhá vrácení zpět.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3f7d6f8ca285fdc024db9ba952af9f7d169e7188
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678470"
---
# <a name="optimizing-transactions-in-dedicated-sql-pool-in-azure-synapse-analytics"></a>Optimalizace transakcí ve vyhrazeném fondu SQL ve službě Azure synapse Analytics

Přečtěte si, jak optimalizovat výkon transakčního kódu ve vyhrazeném fondu SQL a zároveň minimalizovat rizika pro dlouhá vrácení zpět.

## <a name="transactions-and-logging"></a>Transakce a protokolování

Transakce jsou důležitou součástí modulu relačních fondů SQL. Transakce se používají při úpravě dat. Tyto transakce můžou být explicitní nebo implicitní. Jednoduché příkazy INSERT, UPDATE a DELETE jsou všechny příklady implicitních transakcí. Explicitní transakce používají příkaz BEGIN TRAN, COMMIT TRAN nebo ROLLBACK TRAN. Explicitní transakce se obvykle používají, pokud je potřeba spojit více příkazů úprav v jedné atomické jednotce.

Změny ve fondu SQL jsou sledovány pomocí protokolů transakcí. Každá distribuce má svůj vlastní transakční protokol. Zápisy do protokolu transakcí jsou automatické. Není nutná žádná konfigurace. Nicméně i když tento proces zaručuje, že při zápisu se v systému zavádí režie. Tento dopad můžete minimalizovat zapsáním transakčního efektivního kódu. Velmi účinný efektivní kód spadá do dvou kategorií.

* Pokud je to možné, používejte minimální konstrukce protokolování
* Zpracování dat pomocí vymezených dávek, aby se předešlo transakcím v čísle Long spuštěných
* Provedení velkých úprav v daném oddílu pomocí vzoru přepínání oddílů

## <a name="minimal-vs-full-logging"></a>Minimální a úplné protokolování

Na rozdíl od plně protokolovaných operací, které používají transakční protokol k udržení přehledu o každé změně řádku, se s minimálním množstvím protokolovaných operací uchovává pouze přidělení rozsahu a změny meta dat. Minimální protokolování proto zahrnuje protokolování pouze informací potřebných k vrácení transakce po selhání nebo pro explicitní požadavek (vrácení zpět TRAN). V transakčním protokolu jsou sledovány mnohem méně informací, což znamená, že při minimálním zaprotokolovaných operacích dochází k lepšímu množství operací, které mají stejnou velikost. Vzhledem k tomu, že menší počet zápisů přechází do transakčního protokolu, je vygenerováno mnohem menší množství dat protokolu, což je vyšší efektivita vstupně-výstupních operací.

Limity bezpečnosti transakce platí pouze pro plně protokolované operace.

> [!NOTE]
> Minimální protokolované operace se mohou účastnit explicitních transakcí. Vzhledem k tomu, že jsou všechny změny ve strukturách přidělení sledovány, je možné vrátit se k minimálnímu množství protokolovaných operací.

## <a name="minimally-logged-operations"></a>Minimální protokolované operace

Následující operace jsou schopné provádět minimální protokolování:

* CREATE TABLE jako SELECT ([CTAS](sql-data-warehouse-develop-ctas.md))
* Vložit.. VYBRALI
* CREATE INDEX
* ZMĚNIT INDEX OPĚTOVNÉHO SESTAVENÍ
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ZMĚNIT ODDÍL PŘEPÍNÁNÍ TABULKY

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Operace přesunu vnitřních dat (například VŠESMĚROVé vysílání a náhodné) nejsou ovlivněny bezpečnostním limitem transakcí.

## <a name="minimal-logging-with-bulk-load"></a>Minimální protokolování s hromadnou zátěží

CTAS a vložit... VYBRAT jsou operace hromadného načtení. Obě jsou však ovlivněny definicí cílové tabulky a závisí na scénáři zatížení. Následující tabulka vysvětluje, kdy jsou hromadné operace úplné nebo s minimálním protokolem:  

| Primární index | Scénář načtení | Režim protokolování |
| --- | --- | --- |
| Halda |Všechny |**Minimální** |
| Clusterovaný index |Prázdná cílová tabulka |**Minimální** |
| Clusterovaný index |Načtené řádky se nepřesahují s existujícími stránkami v cíli. |**Minimální** |
| Clusterovaný index |Načtené řádky se překrývají s existujícími stránkami v cíli. |Do bloku |
| Clusterovaný index columnstore |Velikost dávky >= 102 400 na distribuci zarovnaná na oddíl |**Minimální** |
| Clusterovaný index columnstore |Velikost dávky < 102 400 na distribuci zarovnaná na oddíl |Do bloku |

Je třeba poznamenat, že jakékoli zápisy k aktualizaci sekundárních nebo neclusterovaných indexů budou vždy plně protokolované.

> [!IMPORTANT]
> Vyhrazený fond SQL má 60 distribucí. Proto za předpokladu, že všechny řádky jsou rovnoměrně distribuovány a vydávány v jednom oddílu, bude muset dávka obsahovat 6 144 000 řádků nebo větší, aby bylo při zápisu do clusterovaného indexu columnstore minimální protokolované. Pokud je tabulka rozdělená na oddíly a řádky, které jsou vloženy na hranice oddílu, budete potřebovat 6 144 000 řádků na hranici oddílu za předpokladu, že bude i distribuce dat. Každý oddíl v každé distribuci musí nezávisle překročit prahovou hodnotu 102 400 řádku, aby bylo možné vložit na minimum přihlášené k distribuci.

Načtení dat do neprázdné tabulky s clusterovaným indexem může často obsahovat kombinaci plně protokolovaných a minimálních protokolovaných řádků. Clusterovaný index je vyrovnaný strom (b-Tree) stránek. Pokud stránka, na kterou se zapisuje, již obsahuje řádky z jiné transakce, pak budou tyto zápisy plně protokolovány. Pokud je ale stránka prázdná, pak se zápis na tuto stránku bude považovat za minimální protokol.

## <a name="optimizing-deletes"></a>Optimalizace odstranění

ODSTRANĚNÍ je plně zaprotokolovaná operace.  Pokud potřebujete v tabulce nebo oddílu odstranit velké množství dat, často to znamená, že `SELECT` data, která chcete zachovat, je vhodnější, což je možné spustit jako podobuně zaznamenanou operaci.  Pokud chcete data vybrat, vytvořte novou tabulku pomocí [CTAS](sql-data-warehouse-develop-ctas.md).  Po vytvoření pomocí [Přejmenovat](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) zahodíte starou tabulku s nově vytvořenou tabulkou.

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

AKTUALIZACE je plně zaprotokolovaná operace.  Pokud potřebujete aktualizovat velký počet řádků v tabulce nebo oddílu, může být často efektivnější použít k tomu přihlášené operace, například [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

V následujícím příkladu je celá aktualizace tabulky převedena na CTAS, aby bylo možné minimální protokolování.

V tomto případě jsme do prodeje v tabulce přidávali částku slevy:

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
> Opětovné vytváření velkých tabulek může mít za úkol používání vyhrazených funkcí správy úloh fondů SQL. Další informace najdete v tématu [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).

## <a name="optimizing-with-partition-switching"></a>Optimalizace pomocí přepínání oddílů

Pokud se v [oddílu tabulky](sql-data-warehouse-tables-partition.md)setkávají změny ve velkém měřítku, dává vzor přepínání oddílů smysl. Pokud je změna dat významná a zahrnuje několik oddílů, pak iterace na těchto oddílech dosáhne stejného výsledku.

Přepínač oddílu má následující kroky:

1. Vytvoření prázdného oddílu out
2. Provést aktualizaci jako CTAS
3. Přepínat existující data na výstupní tabulku
4. Přepnout na nová data
5. Vyčištění dat

Chcete-li však identifikovat oddíly, které mají být přepnuty, vytvořte následující pomocnou proceduru.  

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

Tento postup maximalizuje opakované použití kódu a udržuje příklad přepínání oddílů na více kompaktních.

Následující kód demonstruje výše zmíněné kroky k dosažení úplné rutiny přepínání oddílů.

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

## <a name="minimize-logging-with-small-batches"></a>Minimalizace protokolování u malých dávek

Pro operace změny velkých objemů dat může smysl rozdělit operaci na bloky dat nebo dávky, aby bylo možné určit rozsah práce.

Následující kód je pracovní příklad. Velikost dávky byla nastavena na triviální číslo pro zvýraznění techniky. Ve skutečnosti by byla velikost dávky výrazně větší.

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

## <a name="pause-and-scaling-guidance"></a>Pokyny pro pozastavení a škálování

Vyhrazený fond SQL vám umožňuje [pozastavit, obnovit a škálovat](sql-data-warehouse-manage-compute-overview.md) vyhrazený fond SQL na vyžádání. Při pozastavení nebo škálování vyhrazeného fondu SQL je důležité pochopit, že jakékoliv transakce v letadle jsou okamžitě ukončeny. způsob vrácení všech otevřených transakcí zpět. Pokud vaše úloha vystavila dlouho probíhající a nedokončená úprava dat před operací pozastavit nebo škálování, bude potřeba tuto práci vrátit zpátky. To může mít vliv na dobu potřebnou k pozastavení nebo škálování vyhrazeného fondu SQL.

> [!IMPORTANT]
> `UPDATE`A `DELETE` jsou plně protokolované operace, takže tyto operace vrácení zpět/opětovného zpracování mohou trvat výrazně déle než ekvivalentní minimální zaznamenání operací.

Nejlepším řešením je umožnit, aby transakce změny letových dat byly dokončeny před pozastavením nebo škálováním vyhrazeného fondu SQL. Tento scénář ale nemusí být vždycky praktický. Chcete-li zmírnit riziko dlouhého vrácení zpět, vezměte v úvahu jednu z následujících možností:

* Přepisování dlouhotrvajících operací s použitím [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* Rozdělit operaci na bloky dat; provozování v podmnožině řádků

## <a name="next-steps"></a>Další kroky

Další informace o úrovních izolace a omezeních [transakcí najdete v tématu transakce ve vyhrazeném fondu SQL](sql-data-warehouse-develop-transactions.md) .  Přehled dalších osvědčených postupů najdete v tématu [věnovaném osvědčeným postupům pro fond SQL](sql-data-warehouse-best-practices.md).
