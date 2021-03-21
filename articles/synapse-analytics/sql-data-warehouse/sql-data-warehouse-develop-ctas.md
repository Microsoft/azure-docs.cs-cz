---
title: CREATE TABLE JAKO SELECT (CTAS)
description: Vysvětlení a příklady příkazu CREATE TABLE AS SELECT (CTAS) v synapse SQL pro vývoj řešení.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 68bab754142538fc6067cf2593ae6244a03a48d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98734810"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE JAKO SELECT (CTAS)

Tento článek vysvětluje CREATE TABLE jako příkaz SELECT (CTAS) T-SQL v synapse SQL pro vývoj řešení. Článek také poskytuje příklady kódu.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

Příkaz [CREATE TABLE AS Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (CTAS) je jednou z nejdůležitějších funkcí T-SQL, které jsou k dispozici. CTAS je paralelní operace, která vytvoří novou tabulku na základě výstupu příkazu SELECT. CTAS je nejjednodušší a nejrychlejší způsob, jak vytvořit a vložit data do tabulky jediným příkazem.

## <a name="selectinto-vs-ctas"></a>VYBRAT... DO vs. CTAS

CTAS je přizpůsobitelná verze [výběru... DO](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) příkazu.

Následuje příklad jednoduchého výběru... USKLADNĚN

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

VYBRAT... DO není možné v rámci operace změnit buď metodu distribuce, nebo typ indexu. Vytvoříte `[dbo].[FactInternetSales_new]` pomocí výchozího distribučního typu ROUND_ROBIN a výchozí struktury tabulky CLUSTEROVANÉHO indexu COLUMNSTORE.

S CTAS můžete na druhé straně určit rozdělení dat tabulky i typ struktury tabulky. Chcete-li převést předchozí příklad na CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Pokud se pokoušíte změnit index v operaci CTAS a zdrojová tabulka je distribuována jako hodnota hash, udržujte stejný distribuční sloupec a datový typ. Tím se zabrání přesunu mezi distribučními daty během operace, což je efektivnější.

## <a name="use-ctas-to-copy-a-table"></a>Kopírování tabulky pomocí CTAS

Jedním z nejběžnějších použití CTAS je vytvoření kopie tabulky, aby bylo možné změnit DDL. Řekněme, že jste původně vytvořili tabulku jako `ROUND_ROBIN` a teď ji chcete změnit na tabulku distribuovanou na sloupci. CTAS je způsob, jakým byste změnili distribuční sloupec. CTAS můžete použít také ke změně dělení, indexování nebo typů sloupců.

Řekněme, že jste tuto tabulku vytvořili pomocí výchozího typu distribuce `ROUND_ROBIN` , a ne zadáním distribučního sloupce v `CREATE TABLE` .

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

Nyní chcete vytvořit novou kopii této tabulky s `Clustered Columnstore Index` , aby bylo možné využít výkon clusterovaných tabulek columnstore. Tuto tabulku také chcete distribuovat `ProductKey` , protože očekáváte spojení s tímto sloupcem a chcete se vyhnout přesunu dat během spojení `ProductKey` . Nakonec také budete chtít přidat rozdělení do oddílů `OrderDateKey` , abyste mohli rychle odstranit stará data vyřazením starých oddílů. Tady je příkaz CTAS, který zkopíruje starou tabulku do nové tabulky.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Nakonec můžete přejmenovat tabulky, které se zahodí v nové tabulce, a pak vyřadit starou tabulku.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Použití CTAS k řešení nepodporovaných funkcí

Pomocí CTAS můžete také vyřešit řadu nepodporovaných funkcí uvedených níže. Tato metoda může být často užitečná, protože nejen váš kód bude kompatibilní, ale bude často běžet rychleji na synapse SQL. Tento výkon je výsledkem úplného paralelního návrhu. Mezi scénáře patří:

* SPOJENÍ ANSI při aktualizacích
* Spojení ANSI při odstraňování
* Příkaz MERGE

> [!TIP]
> Zkuste si představit slovo "CTAS First". Řešení potíží pomocí CTAS je obecně dobrým přístupem, a to i v případě, že zapisujete další data v důsledku výsledku.

## <a name="ansi-join-replacement-for-update-statements"></a>Nahrazení spojení ANSI pro příkazy Update

Může se stát, že máte složitou aktualizaci. Aktualizace spojí více než dvě tabulky dohromady pomocí syntaxe připojení ANSI k provedení aktualizace nebo odstranění.

Představte si, že jste museli aktualizovat tuto tabulku:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

Původní dotaz možná vypadal jako v tomto příkladu:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

Synapse SQL nepodporuje spojení ANSI v `FROM` klauzuli `UPDATE` příkazu, takže nemůžete použít předchozí příklad beze změny.

Pomocí kombinace CTAS a implicitního spojení můžete nahradit předchozí příklad:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-merge"></a>Nahrazení spojení ANSI pro sloučení 

Ve službě Azure synapse Analytics, [sloučení](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true) (Preview) se neshodou podle TARGETu vyžaduje, aby cílem byla distribuovaná tabulka hash.  Uživatelé mohou použít připojení ke standardu ANSI s [aktualizací Update](/sql/t-sql/queries/update-transact-sql?view=azure-sqldw-latest&preserve-view=true) nebo [Delete](/sql/t-sql/statements/delete-transact-sql?view=azure-sqldw-latest&preserve-view=true) jako alternativní řešení pro úpravu dat cílové tabulky na základě výsledku spojení s jinou tabulkou.  Zde je příklad.

```sql
CREATE TABLE dbo.Table1   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
CREATE TABLE dbo.Table2   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES(1, 10.0);  
INSERT INTO dbo.Table2 VALUES(1, 0.0);  
GO  
UPDATE dbo.Table2   
SET dbo.Table2.ColB = dbo.Table2.ColB + dbo.Table1.ColB  
FROM dbo.Table2   
    INNER JOIN dbo.Table1   
    ON (dbo.Table2.ColA = dbo.Table1.ColA);  
GO  
SELECT ColA, ColB   
FROM dbo.Table2;

```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Explicitní stavový datový typ a hodnota null výstupu

Při migraci kódu můžete být spuštěni v rámci tohoto typu vzoru kódu:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Možná si myslíte, že byste tento kód mohli migrovat na CTAS a vy budete mít správný. Zde je ale skrytý problém.

Následující kód nepřinese stejný výsledek:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Všimněte si, že sloupec "Result" přenáší datový typ a hodnoty hodnoty null výrazu. Zablokování datového typu může vést k drobným odchylkám v hodnotách, pokud nebudete opatrní.

Vyzkoušejte tento příklad:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Hodnota uložená pro výsledek je odlišná. Vzhledem k tomu, že hodnota trvalá ve sloupci výsledek se používá v jiných výrazech, bude chyba ještě důležitější.

![Snímek obrazovky s výsledky CTAS](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

To je důležité pro migrace dat. I když je druhý dotaz pravděpodobně přesnější, dojde k problému. Data by se v porovnání se zdrojovým systémem lišila a v případě potřeby vede k otázkám integrity migrace. Toto je jeden z těch vzácných případů, kdy je "nesprávná" odpověď vlastně pravá.

Důvodem, proč se u dvou výsledků zobrazuje nerovnost, je kvůli implicitnímu přetypování typu. V prvním příkladu tabulka definuje definici sloupce. Při vložení řádku dojde k převodu implicitního typu. V druhém příkladu není k dispozici žádný implicitní převod typu, protože výraz definuje datový typ sloupce.

Všimněte si také, že sloupec ve druhém příkladu byl definován jako sloupec s možnou hodnotou null, zatímco v prvním příkladu to nemá. Při vytvoření tabulky v prvním příkladu byla explicitně definována možnost null sloupce. Ve druhém příkladu byl ponechán na výraz a ve výchozím nastavení by výsledkem byla definice NULL.

Chcete-li tyto problémy vyřešit, je nutné explicitně nastavit převod typu a možnost použití hodnoty null ve vybrané části příkazu CTAS. Tyto vlastnosti nelze nastavit v ' CREATE TABLE '.
Následující příklad ukazuje, jak opravit kód:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Všimněte si, že:

* Můžete použít přetypování nebo převod.
* Pro vynucení hodnoty NULL použijte ISNULL, not COALESCE. Podívejte se na následující poznámku.
* ISNULL je nejvzdálenější funkce.
* Druhá část pole ISNULL je konstanta, 0.

> [!NOTE]
> Aby hodnota null byla správně nastavena, je důležité použít funkci ISNULL a NOT COALESCE. COALESCE není deterministické funkce, takže výsledek výrazu bude vždy NULLable. ISNULL je jiný. Je deterministický. Proto pokud je druhá část funkce ISNULL konstanta nebo literál, výsledná hodnota nebude NULL.

Zajištění integrity výpočtů je také důležité pro přepínání oddílů tabulky. Představte si, že máte tuto tabulku definovanou jako tabulku faktů:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Pole Částka je ale počítaný výraz. Není součástí zdrojových dat.

Chcete-li vytvořit dělenou datovou sadu, můžete použít následující kód:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Dotaz by měl být dokonale správný. K tomuto problému dochází při pokusu o provedení přepínače oddílu. Definice tabulek se neshodují. Chcete-li, aby definice tabulek odpovídaly, upravte CTAS a přidejte `ISNULL` funkci pro zachování atributu s hodnotou null sloupce.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Můžete vidět, že konzistence typů a udržování vlastností s hodnotou null na CTAS je osvědčeným postupem. Pomáhá zachovat integritu ve výpočtech a také zajišťuje, že je možné přepínat oddíly.

CTAS je jedním z nejdůležitějších příkazů v synapse SQL. Ujistěte se, že ji důkladně rozumíte. Další informace najdete v [dokumentaci k CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).