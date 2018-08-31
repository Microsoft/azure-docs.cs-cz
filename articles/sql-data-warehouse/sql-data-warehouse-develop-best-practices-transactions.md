---
title: Optimalizace transakcí pro službu Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Zjistěte, jak optimalizovat výkon transakční kódu ve službě Azure SQL Data Warehouse při minimalizovat rizika pro dlouhé vrácení zpět.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/19/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: eb9c66c23440d2fd55c62de02b6c0070245c6d67
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247915"
---
# <a name="optimizing-transactions-in-azure-sql-data-warehouse"></a>Optimalizace transakcí ve službě Azure SQL Data Warehouse
Zjistěte, jak optimalizovat výkon transakční kódu ve službě Azure SQL Data Warehouse při minimalizovat rizika pro dlouhé vrácení zpět.

## <a name="transactions-and-logging"></a>Transakce a protokolování
Transakce jsou důležitou součástí relačním databázovém stroji. SQL Data Warehouse používá během úpravu dat transakcí. Tyto transakce může být explicitní nebo implicitní. Příkazy jeden INSERT, UPDATE a DELETE jsou všechny příklady implicitní transakce. Explicitní transakce pomocí BEGIN TRAN, TRAN potvrzení nebo vrácení zpět TRAN. Explicitní transakce jsou obvykle používány při víc úpravy příkazy potřeba je svázat dohromady v jednu atomickou jednotku. 

Azure SQL Data Warehouse potvrdí změny do databáze pomocí protokolů transakcí. Každá distribuce má svůj vlastní transakční protokol. Zápisy protokolu transakce jsou automatické. Není nutná žádná konfigurace. Během tohoto procesu zaručuje zápis ji v systému zavést další režií. Minimalizovat tohoto dopadu napsáním kódu transakčně efektivní. Transakčně efektivní kód široce spadá do dvou kategorií.

* Minimální protokolování použití vytvoří vždy, když je to možné
* Zpracování dat pomocí dávky s vymezeným oborem, aby singulární dlouhotrvající transakce
* Přijmout oddíl přepínání vzor pro velké změny do daného oddílu

## <a name="minimal-vs-full-logging"></a>Minimální vs. úplné protokolování
Na rozdíl od plně protokolované operace, které pomocí transakčního protokolu udržovat přehled o každé změně řádku, minimálně protokolované operace sledovat, přidělení rozsahu a pouze změny metadat. Proto zahrnuje minimální protokolování protokolování pouze informace, které je potřeba vrátit zpět transakce po selhání, nebo pro explicitní žádost (TRAN vrácení zpět). Jak mnohem méně informace jsou sledovány v protokolu transakcí, minimálně protokolovaných operací vrací lepší výsledky než podobné velikosti plně protokolovaných operací. Navíc vzhledem k tomu, že menší počet zápisů přejít transakčního protokolu, je vygenerována mnohem menší množství dat protokolu a další vstupně-výstupních operací je efektivní.

Transakce bezpečnostní omezení platí jenom pro plně protokolované operace.

> [!NOTE]
> Minimálně protokolované operace mohou účastnit explicitní transakce. Jak všechny změny v přidělování struktury jsou sledovány, je možné vrátit zpět minimálně protokolované operace. 
> 
> 

## <a name="minimally-logged-operations"></a>Minimálně operací
Tyto operace podporují minimálně přihlašováno:

* VYTVOŘENÍ TABLE AS SELECT ([CTAS](sql-data-warehouse-develop-ctas.md))
* VLOŽIT... VYBERTE
* VYTVOŘENÍ INDEXU
* PŘÍKAZ ALTER INDEX ZNOVU SESTAVIT
* ODSTRANIT INDEX
* TRUNCATE TABLE
* ODSTRANIT TABULKU
* PŘÍKAZ ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Operace přesunu dat vnitřní (jako je například vysílání a SHUFFLE) nejsou ovlivněny transakce bezpečnostní omezení.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Minimální protokolování pomocí hromadné načtení
Funkce CTAS a vložit... Vybrat jsou obě operace hromadného načtení. Však obě jsou ovlivněny definici cílové tabulky a závisí na scénáři zatížení. Následující tabulka vysvětluje, při hromadné operace jsou plně nebo minimálně protokolování:  

| Primární Index | Scénář zatížení | Režim protokolování |
| --- | --- | --- |
| Halda |Všechny |**Minimální** |
| Clusterovaný Index |Prázdná cílová tabulka |**Minimální** |
| Clusterovaný Index |Načtené řádky nepřekrývají s existující stránky v cíli |**Minimální** |
| Clusterovaný Index |Načtené řádky se překrývá s existující stránky v cíli |Úplná |
| Clusterovaný Index Columnstore |Velikost dávky > = 102,400 na distribuci zarovnání oddílu |**Minimální** |
| Clusterovaný Index Columnstore |Batch velikost < 102,400 na distribuci zarovnání oddílu |Úplná |

Je vhodné poznamenat, že jakékoli zápisy aktualizovat sekundární či neclusterované indexy budou vždy plně protokolované operace.

> [!IMPORTANT]
> SQL Data Warehouse má 60 distribucí. Proto se za předpokladu, že všechny řádky jsou rovnoměrně rozloženy a úvodní do jednoho oddílu, služby batch musí obsahovat 6,144,000 řádků nebo větší, aby minimálně zaznamená při zápisu do Clusterovaného indexu Columnstore. Pokud v tabulce je rozdělit na oddíly a řádcích vloženého span hranice oddílů, musíte 6,144,000 řádky za hranice oddílu za předpokladu, že i distribuci dat. Každý oddíl v každé distribuci musí nezávisle na sobě by překračovaly prahovou hodnotu 102 400 řádků pro vložení být minimálně přihlášení distribuce.
> 
> 

Načítání dat do prázdné tabulky pomocí clusterovaného indexu můžete často obsahovat kombinaci plně zaznamenané a minimálně zaznamenané řádků. Clusterovaný index je vyvážené stromu (b stromu) stránek. Pokud na stránce zapisuje do již obsahuje řádky z jiné transakce, pak tyto zápisy, budou plně zaznamenány. Ale pokud na stránce je prázdný pak zápisu pro danou stránku budou minimálně zaznamenány.

## <a name="optimizing-deletes"></a>Optimalizace odstranění
ODSTRANĚNÍ je plně protokolovaných operací.  Pokud je potřeba odstranit velké množství dat v tabulce nebo oddíl, je často vhodnější pro `SELECT` data chcete zachovat, která může běžet jako minimálně protokolovaných operací.  Pokud chcete vybrat data, umožňuje vytvořit novou tabulku s [CTAS](sql-data-warehouse-develop-ctas.md).  Po vytvoření pomocí [přejmenovat](/sql/t-sql/statements/rename-transact-sql) vyměnit vaše staré tabulky s nově vytvořenou databázi.

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
AKTUALIZACE je plně protokolovaných operací.  Pokud je potřeba aktualizovat velký počet řádků v tabulce nebo oddíl, může často být mnohem efektivnější použít minimálně protokolovaných operací, jako [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) Uděláte to tak.

V příkladu níže celou tabulku aktualizace se převedlo na provedení příkazu CTAS tak, aby byla minimální protokolování je to možné.

V takovém případě zpětně přidáváme částky slevy na prodeje v tabulce:

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
> Opětovné vytvoření velké tabulky můžete s výhodou využít funkce správy úloh datového skladu SQL. Další informace najdete v tématu [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).
> 
> 

## <a name="optimizing-with-partition-switching"></a>Optimalizace přepínání oddílů
Pokud potýkají s rozsáhlé změny uvnitř [tabulky oddílu](sql-data-warehouse-tables-partition.md), pak oddíl přepínání vzor dává smysl. Pokud úpravu dat je důležité, zahrnuje několik oddílů napříč oddíly potom iterace dosáhne stejného výsledku.

Postup provedení oddíl přepínače jsou následující:

1. Vytvořte prázdnou na oddíl
2. Provedení této aktualizace jako příkazu CTAS
3. Přepnout existujících dat do výstupní tabulky
4. Přepnout v nových dat.
5. Vyčistit data

Ale identifikovat oddíly přepnout, vytvořte následující proceduru pomocné rutiny.  

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

Tento postup maximalizuje opakované využívání kódu a udržuje přepnutí příklad kompaktnějším oddílu.

Následující kód ukazuje kroky k dosažení úplné oddíl přepínání rutina již bylo zmíněno dříve.

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

## <a name="minimize-logging-with-small-batches"></a>Minimalizovat protokolování pomocí malých dávkách
Pro operace úprav velkých objemů dat má smysl rozdělit operace do bloků dat nebo dávky k určení rozsahu jednotku práce.

Následující kód je funkční příklad. Velikost dávky je nastavená na hodnotu triviální zvýrazněte technika. Ve skutečnosti by podstatně větší velikost dávky. 

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

## <a name="pause-and-scaling-guidance"></a>Pozastavení a škálování pokyny
Azure SQL Data Warehouse umožňuje [pozastavení, obnovení a škálování](sql-data-warehouse-manage-compute-overview.md) váš datový sklad na vyžádání. Při pozastavení nebo škálování služby SQL Data Warehouse, je důležité pochopit, že jakékoli vydávaných za pochodu transakce budou ukončeny okamžitě; Příčinou všechny otevřené transakce zpět. Pokud vaše úloha vystavila dlouho běží a je neúplný úpravu dat před operaci pozastavení nebo škálování, tuto práci bude muset vrátit zpět. Toto zrušení může mít vliv na čas potřebný k pozastavení nebo škálování databáze Azure SQL Data Warehouse. 

> [!IMPORTANT]
> Obě `UPDATE` a `DELETE` jsou plně operací a tak tyto zpět/znovu operace může trvat výrazně déle, než ekvivalentní minimálně protokoluje operace. 
> 
> 

Nejlepší možností je nechat v letu data změny transakce dokončena před pozastavením nebo Škálováním SQL Data Warehouse. Ale v tomto scénáři nemusí být vždy praktické. Pro zmírnění rizika dlouhé odvolávání, zvažte jednu z následujících možností:

* Přepište dlouho běžící operace pomocí [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)
* Operace rozdělení do bloků dat; práce na podmnožinu řádků

## <a name="next-steps"></a>Další postup
Zobrazit [transakcí ve službě SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) získat další informace o úrovních izolace a transakční omezení.  Přehled dalších osvědčených postupů, naleznete v tématu [osvědčené postupy SQL Data Warehouse](sql-data-warehouse-best-practices.md).

