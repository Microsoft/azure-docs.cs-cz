---
title: VYTVOŘIT TABULKU JAKO VÝBĚR (CTAS)
description: Vysvětlení a příklady příkazu CREATE TABLE AS SELECT (CTAS) v SQL Analytics pro vývoj řešení.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: bb9ff52bd7d2e4cfd1a1df4d780a4c369380284f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350606"
---
# <a name="create-table-as-select-ctas-in-sql-analytics"></a>VYTVOŘIT TABULKU JAKO SELECT (CTAS) v SQL Analytics

Tento článek vysvětluje příkaz T-SQL CREATE TABLE AS SELECT (CTAS) v SQL Analytics pro vývoj řešení. Článek také obsahuje příklady kódu.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

Příkaz [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) je jednou z nejdůležitějších dostupných funkcí T-SQL. CTAS je paralelní operace, která vytvoří novou tabulku na základě výstupu příkazu SELECT. CTAS je nejjednodušší a nejrychlejší způsob, jak vytvořit a vložit data do tabulky pomocí jediného příkazu.

## <a name="selectinto-vs-ctas"></a>Vyberte... INTO vs. CTAS

CTAS je více přizpůsobitelné verze [SELECT ... DO](/sql/t-sql/queries/select-into-clause-transact-sql) prohlášení.

Následuje příklad jednoduchého SELECT... Do:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Vyberte... INTO neumožňuje změnit metodu distribuce nebo typ indexu jako součást operace. Můžete `[dbo].[FactInternetSales_new]` vytvořit pomocí výchozí ho typu distribuce ROUND_ROBIN a výchozí strukturu tabulky clustered COLUMNSTORE INDEX.

S CTAS, na druhé straně můžete určit jak rozdělení dat tabulky, stejně jako typ struktury tabulky. Převod předchozího příkladu na CTAS:

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
> Pokud se pokoušíte změnit index pouze v operaci CTAS a zdrojová tabulka je distribuována hash, udržujte stejný distribuční sloupec a typ dat. Tím se zabrání pohybu dat mezi distribucemi během operace, což je efektivnější.

## <a name="use-ctas-to-copy-a-table"></a>Kopírování tabulky pomocí CTAS

Pravděpodobně jedním z nejběžnějších použití CTAS je vytvoření kopie tabulky za účelem změny DDL. Řekněme, že jste tabulku původně vytvořili jako `ROUND_ROBIN`a nyní ji chcete změnit na tabulku distribuovanou ve sloupci. CTAS je způsob, jakým byste změnili sloupec distribuce. Ctas můžete také změnit dělení, indexování nebo typy sloupců.

Řekněme, že jste tuto tabulku vytvořili `ROUND_ROBIN`pomocí výchozího typu distribuce `CREATE TABLE`, který neurčuje sloupec distribuce v .

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

Nyní chcete vytvořit novou kopii této tabulky, s `Clustered Columnstore Index`, takže můžete využít výkon clusterovaných columnstore tabulek. Chcete také distribuovat tuto `ProductKey`tabulku na , protože očekáváte spojení v tomto sloupci a chcete `ProductKey`se vyhnout přesunu dat během spojení na . Nakonec také chcete přidat dělení na `OrderDateKey`, takže můžete rychle odstranit stará data uvolněním staré oddíly. Zde je prohlášení CTAS, které zkopíruje starou tabulku do nové tabulky.

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

Nakonec můžete přejmenovat tabulky, vyměnit v nové tabulce a pak přetáhnout starý stůl.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Použití ctas k obejít nepodporované funkce

Ctas můžete také použít k řešení řady nepodporovaných funkcí uvedených níže. Tato metoda může často být užitečné, protože nejen že bude váš kód kompatibilní, ale často poběží rychleji na SQL Analytics. Tento výkon je výsledkem jeho plně paralelizované konstrukce. Scénáře zahrnují:

* ANSI SPOJENÍ na UPDATEs
* ANSI JOINs na DELETEs
* Příkaz MERGE

> [!TIP]
> Zkus si nejdřív myslet "CTAS.". Řešení problému pomocí CTAS je obecně dobrý přístup, i když v důsledku toho píšete více dat.

## <a name="ansi-join-replacement-for-update-statements"></a>Ansi připojit nahrazení pro příkazy aktualizace

Možná zjistíte, že máte komplexní aktualizaci. Aktualizace spojuje více než dvě tabulky dohromady pomocí syntaxe spojení ANSI k provedení UPDATE nebo DELETE.

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

Původní dotaz mohl vypadat podobně jako v tomto příkladu:

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

SQL Analytics nepodporuje připojení ANSI `FROM` v klauzuli příkazu, `UPDATE` takže nelze použít předchozí příklad bez jeho úpravy.

Můžete použít kombinaci CTAS a implicitní spojení nahradit předchozí příklad:

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

## <a name="ansi-join-replacement-for-delete-statements"></a>Ansi spojení nahrazení delete příkazy

Někdy je nejlepším přístupem k odstranění dat použití CTAS, zejména pro `DELETE` příkazy, které používají syntaxi spojení ANSI. Důvodem je, že SQL Analytics nepodporuje `FROM` ansi `DELETE` spojení v klauzuli příkazu. Místo odstranění dat vyberte data, která chcete zachovat.

Následuje příklad převedeného `DELETE` příkazu:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Nahradit příkazy sloučení

Příkazy sloučení můžete nahradit alespoň částečně pomocí CTAS. Můžete kombinovat `INSERT` a `UPDATE` do jednoho příkazu. Všechny odstraněné záznamy by `SELECT` měly být z příkazu omezeny, aby byly výsledky vynechány.

Následující příklad je `UPSERT`pro :

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Explicitně stav datový typ a nullability výstupu

Při migraci kódu můžete zjistit, že narazíte na tento typ způsobu kódování:

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

Možná si myslíte, že byste měli tento kód migrovat na CTAS a měli byste mít pravdu. Nicméně, je tu skrytý problém zde.

Následující kód nepřináší stejný výsledek:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Všimněte si, že sloupec "výsledek" přenáší dopředu datový typ a nullability hodnoty výrazu. Pokud nejste opatrní, může přenos datového typu vpřed vést k jemným odchylkám v hodnotách.

Zkuste tento příklad:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Hodnota uložená pro výsledek se liší. Jako trvalá hodnota ve sloupci výsledek se používá v jiných výrazech, chyba se stává ještě významnější.

![Snímek obrazovky s výsledky CTAS](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

To je důležité pro migrace dat. I když druhý dotaz je pravděpodobně přesnější, je tu problém. Data by se lišila ve srovnání se zdrojovým systémem, což vede k otázkám integrity migrace. To je jeden z těch vzácných případů, kdy "špatná" odpověď je vlastně ten správný!

Důvod, proč vidíme rozdíl mezi těmito dvěma výsledky je kvůli implicitní typ odlévání. V prvním příkladu tabulka definuje definici sloupce. Při vložení řádku dojde k implicitní převod typu. V druhém příkladu neexistuje žádný implicitní převod typu, protože výraz definuje datový typ sloupce.

Všimněte si také, že sloupec v druhém příkladu byl definován jako nullable sloupec, zatímco v prvním příkladu nemá. Při vytvoření tabulky v prvním příkladu byla explicitně definována nullability sloupce. V druhém příkladu byla ponechána na výraz a ve výchozím nastavení by mělo za následek definici NULL.

Chcete-li tyto problémy vyřešit, je nutné explicitně nastavit převod typu a nullability v select části ctas prohlášení. Tyto vlastnosti nelze nastavit v příkazu VYTVOŘIT TABULKU.
Následující příklad ukazuje, jak opravit kód:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Je třeba počítat s následujícím:

* Můžete použít CAST nebo CONVERT.
* Pomocí funkce ISNULL, nikoli COALESCE, vynutíte nullability. Viz následující poznámka.
* ISNULL je nejkrajnější funkce.
* Druhá část ISNULL je konstanta, 0.

> [!NOTE]
> Pro nullability správně nastavit, je důležité použít ISNULL a ne COALESCE. COALESCE není deterministická funkce, a proto výsledek výrazu bude vždy nullable. HODNOTA ISNULL je odlišná. Je to deterministické. Proto pokud druhá část funkce ISNULL je konstanta nebo literál, výsledná hodnota nebude null.

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

Pole částky je však vypočítaný výraz. Není součástí zdrojových dat.

Chcete-li vytvořit rozdělenou datovou sadu, můžete použít následující kód:

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

Dotaz by běžel dokonale. Problém nastává, když se pokusíte udělat přepínač oddílu. Definice tabulky se neshodují. Chcete-li, aby definice tabulky odpovídaly, `ISNULL` upravte CTAS a přidejte funkci, aby se zachoval atribut nullability sloupce.

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

Můžete vidět, že konzistence typu a udržování vlastnosti nullability na CTAS je osvědčený postup inženýrství. Pomáhá udržovat integritu ve výpočtech a také zajišťuje, že přepínání oddílů je možné.

CTAS je jedním z nejdůležitějších příkazů v SQL Analytics. Ujistěte se, že jste důkladně pochopit. Viz [dokumentace CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v [přehledu vývoje](sql-data-warehouse-overview-develop.md).

