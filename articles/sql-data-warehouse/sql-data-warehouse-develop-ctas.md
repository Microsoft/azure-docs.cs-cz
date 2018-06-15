---
title: Vytvoření TABLE AS SELECT (funkce CTAS) v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro psaní kódu s použitím příkazu Vytvořit tabulku AS vyberte funkce CTAS () v Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 9bff6b1216ae826203b24a2cdf8a3d7fd0fd586f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599080"
---
# <a name="using-create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Pomocí CREATE TABLE AS SELECT (funkce CTAS) v Azure SQL Data Warehouse
Tipy pro psaní kódu s použitím příkazu Vytvořit tabulku AS vyberte funkce CTAS () T-SQL v Azure SQL Data Warehouse na vývoj řešení.

## <a name="what-is-create-table-as-select-ctas"></a>Co je vytvoření tabulky AS vyberte funkce CTAS ()?

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) nebo příkaz funkce CTAS je jedním z nejdůležitějších funkcích T-SQL, která je k dispozici. Je paralelní operace, která vytvoří novou tabulku ve výstupu příkazu SELECT. CTASD je nejjednodušší a nejrychlejší způsob, jak vytvořit kopii tabulky. 

## <a name="selectinto-vs-ctas"></a>VYBERTE... DO vs. CTAS
Můžete zvážit funkce CTAS jako extrémně účtovat verze [vyberte... DO](/sql/t-sql/queries/select-into-clause-transact-sql) příkaz.

Dole je příklad jednoduchý příkaz SELECT... DO:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

V předchozím příkladu `[dbo].[FactInternetSales_new]` je vytvořen jako ROUND_ROBIN distribuované tabulku s INDEXEM COLUMNSTORE v clusteru, protože toto jsou výchozí hodnoty tabulky v Azure SQL Data Warehouse.

VYBERTE... DO ale neumožňuje změnit metodu distribuce nebo typ indexu v rámci operace. Toto je, kde funkce CTAS odeslán.

Chcete-li převést předchozí příklad pro funkce CTAS je poměrně jednoduché:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

Pomocí funkce CTAS budete moci změnit rozdělení dat v tabulce, jakož i typu tabulky. 

> [!NOTE]
> Pokud se pouze pokoušíte změnit index v vaše `CTAS` operace a zdrojová tabulka je distribuovat algoritmu hash pak vaší `CTAS` operace provede nejlépe, pokud chcete zachovat stejné distribuční typ sloupce a data. Tím se vyhnete křížové distribuční přesun dat během operace, které je efektivnější.
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>Pomocí funkce CTAS zkopírujte tabulku
Možná jedním z většiny běžných používá `CTAS` vytváří kopie tabulku, ve kterém můžete změnit DDL. Pokud třeba jste původně vytvořili tabulku jako `ROUND_ROBIN` a teď chcete ho změnit na tabulku rozdělit na sloupci, `CTAS` je, jak by sloupec distribuční změnit. `CTAS` lze také změnit typy rozdělení do oddílů, indexování nebo sloupec.

Řekněme, že jste vytvořili tuto tabulku pomocí výchozí typ distribuce tohoto `ROUND_ROBIN` distribuované vzhledem k tomu, že žádný distribuční sloupec byl zadán v `CREATE TABLE`.

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
    CustomerPONumber nvarchar(25)
);
```

Teď chcete vytvořit novou kopii této tabulky s clusterovaný Columnstore Index, takže můžete využít výhod výkonu tabulky Columnstore v clusteru. Můžete také chcete distribuovat tuto tabulku na ProductKey vzhledem k tomu, že se očekává spojení na tomto sloupci a chcete zabránit přesunu dat během spojení na ProductKey. Nakonec chcete také přidat dělení na OrderDateKey, takže můžete rychle odstranit stará data odstranit staré oddíly. Zde je funkce CTAS příkaz, který by vaše staré tabulka zkopírujte do nové tabulky.

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

Nakonec můžete přejmenovat vaše tabulky odkládacího souboru v nové tabulce a pak vyřadit staré tabulky.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [!NOTE]
> Azure SQL Data Warehouse zatím nepodporuje automatické vytváření ani automatickou aktualizaci statistik.  Aby vám dotazy vracely co nejlepší výsledky, je důležité, aby se statistiky vytvořily pro všechny sloupce všech tabulek po prvním načtením nebo kdykoli, kdy v datech dojde k podstatným změnám.  Podrobné vysvětlení statistiky najdete v tématu [statistiky] [statistiky] ve skupině témat věnovaných vývoji.
> 
> 

## <a name="using-ctas-to-work-around-unsupported-features"></a>Pomocí funkce CTAS obejít nepodporované funkce
Funkce CTAS lze také obejít počet níže uvedené nepodporované funkce. To může být často na win/win situaci, protože pouze váš kód bude kompatibilní, ale je často spustí rychleji SQL Data Warehouse. Toto je v důsledku plně parallelized návrh. Mezi scénáře, které může být kolem pracovali funkce CTAS patří:

* ANSI spojení na aktualizace
* ANSI spojení na odstranění
* MERGE – příkaz

> [!NOTE]
> Zamyslete se nad "funkce CTAS první". Pokud se domníváte, že vám může pomoct vyřešit problém pomocí `CTAS` , je obecně nejlepší způsob, jak postupovat, je - i v případě, že v důsledku píšete další data.
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>Připojení k nahrazení ANSI pro příkazy aktualizace
Můžete zjistit, že máte komplexní aktualizace, které spojí dohromady pomocí ANSI připojení syntaxe k provedení aktualizace nebo odstranění více než dvě tabulky.

Představte si, že jste museli aktualizovat v této tabulce:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

Původní dotaz může mít hledá přibližně takto:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
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
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Vzhledem k tomu, že SQL Data Warehouse nepodporuje ANSI Příkazy JOIN v `FROM` klauzuli `UPDATE` prohlášení, nelze zkopírovat tento kód přes bez provedení změn mírně.

Můžete použít kombinaci `CTAS` a implicitní spojení nahraďte tento kód:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Odstranit připojení k nahrazení ANSI pro příkazy
Někdy je nejlepší metodou pro odstranění dat použít `CTAS`. Místo odstraněním dat jednoduše vyberte data, která chcete zachovat. Tato především pro `DELETE` příkazy, které používají ansi spojující syntaxe, protože SQL Data Warehouse nepodporuje ANSI Příkazy JOIN v `FROM` klauzuli `DELETE` příkaz.

Příklad převedený příkazu DELETE je k dispozici následující:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Nahraďte příkazy merge
Příkazy Merge lze nahradit, alespoň v rámci, pomocí funkce CTAS. Do jednoho příkazu může konsolidovat úlohy INSERT a aktualizace. Odstraněné záznamy by třeba ukončit vypnout v druhém příkazu.

Následuje příklad UPSERT:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Funkce CTAS doporučení: explicitně stavu datový typ a možnost použití hodnoty Null výstupu
Při migraci kódu můžete zjistit, že spustíte přes tento typ kódování vzoru:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instinktivně si myslíte měli byste migrovat tento kód na funkce CTAS a by byly správné. Je však skrytá problém tady.

Následující kód nepřinese stejný výsledek:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Všimněte si sloupec "výsledek" představuje předat hodnoty datového typu a možnost použití hodnoty Null výrazu. To může vést k jemně odchylky v hodnoty, pokud nejste opatrní.

Zkuste následující kroky jako příklad:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Hodnota uložená pro výsledek se liší. Jako trvalé hodnoty ve sloupci Výsledek se používá v jiné výrazy se změní i větších chyba.

![Funkce CTAS výsledky](media/sql-data-warehouse-develop-ctas/ctas-results.png)

To je zvlášť důležité pro data migrace. I když je pravděpodobně přesnější druhého dotazu došlo k potížím. Data byla odlišná ve srovnání s zdrojovém systému a který vede k otázky integrity migrace. Toto je jedna z těchto výjimečných případech, kdy "nesprávný" odpověď je ve skutečnosti ten správný!

Z důvodu, že vidíte tento rozdíl mezi dvěma výsledky je dolů implicitní typ přetypování. V prvním příkladu definuje tabulky definice sloupce. Když je vložit řádek dojde k konverzi implicitní typu. V druhém příkladu není žádný typ implicitní převod jako výraz definuje datový typ sloupce. Všimněte si také, že sloupec v druhém příkladu jako sloupec s možnou hodnotou Null byla definována zatímco v prvním příkladu má není. Při vytváření tabulky v první Nullable sloupce příklad byl explicitně definován. Ve druhém příkladu, který je právě bylo výrazu a ve výchozím nastavení to by způsobilo definici hodnotu NULL.  

Chcete-li vyřešit tyto problémy musíte explicitně nastavit převodu typu a možnost použití hodnoty Null v části vyberte příkaz funkce CTAS. V části Vytvoření tabulky nelze nastavit tyto vlastnosti.

Následující příklad ukazuje, jak opravit kód:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Je třeba počítat s následujícím:

* CAST nebo CONVERT byl použit
* IsNull – slouží k vynucení možnost použití hodnoty Null nejsou COALESCE
* ISNULL je nejzevnější funkce
* Druhá část ISNULL je konstanta, tzn. 0

> [!NOTE]
> Pro možnost použití hodnoty Null, být správně nastavena je důležité používat ISNULL a není COALESCE. Funkci COALESCE není deterministický funkce a tak výsledkem výrazu bude vždy s možnou hodnotou Null. ISNULL se liší. Je deterministický. Proto když druhou částí funkce ISNULL konstanta, nebo literál pak výsledná hodnota není NULL.
> 
> 

Tento tip není právě užitečné k zajištění integrity výpočtů. Je také důležité k přepnutí oddílu tabulky. Představte si, že máte tato tabulka definován jako vaše fakt:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Hodnota pole je však počítané výraz, který není součástí zdrojová data.

Vytvoření oddílů datovou sadu můžete chtít provést:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
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
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Dotaz by byl spuštěn perfektně dobře. Problém je při pokusu o provedení přepínač oddílu. Definice tabulek se neshodují. Chcete-li definice tabulky odpovídat funkce CTAS má být změněn.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
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
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Můžete zobrazit proto konzistenci typu a udržování možnost použití hodnoty NULL vlastnosti funkce CTAS je vhodné engineering nejlepší. Pomáhá zachovat integritu do výpočtů a také zajistí, že přepnutí oddílu je možné.

Podrobnosti najdete [funkce CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) dokumentaci. Je jedním z nejdůležitějších příkazy v Azure SQL Data Warehouse. Ujistěte se, že rozumíte důkladně.

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj, najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

