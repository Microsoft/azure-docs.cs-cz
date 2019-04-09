---
title: Vytvoření TABLE AS SELECT (CTAS) ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Vysvětlení a příklady s příkazem Vytvořit TABLE AS SELECT (CTAS) ve službě Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: ea95a13277927b485bb9da3b75b84cce4337bf88
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280430"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Vytvoření TABLE AS SELECT (CTAS) ve službě Azure SQL Data Warehouse

Vysvětlení a příklady kódování pro vývoj řešení pomocí příkazu T-SQL vytvořte tabulku AS SELECT (CTAS) ve službě Azure SQL Data Warehouse.

## <a name="create-table-as-select-ctas"></a>VYTVOŘENÍ TABLE AS SELECT (CTAS)

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) nebo příkazu CTAS je jednou z vašich nejdůležitějších funkcí T-SQL k dispozici. Funkce CTAS je paralelní operace, která vytvoří novou tabulku na základě výstupu příkazu SELECT. Funkce CTAS je nejjednodušší a nejrychlejší způsob, jak vytvořit a vložení dat do tabulky pomocí jediného příkazu.

## <a name="selectinto-vs-ctas"></a>VYBERTE... DO sady vs. CTAS

Funkce CTAS je mimořádně účtovat podle verze [vyberte... DO](/sql/t-sql/queries/select-into-clause-transact-sql) příkazu.

Níže je příklad jednoduchý příkaz SELECT... DO:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

VYBERTE... DO neumožňuje změnu metody distribuce nebo typ indexu jako součást operace. `[dbo].[FactInternetSales_new]` vytvoří výchozí typ distribuce ROUND_ROBIN a struktura tabulky výchozí jako CLUSTEROVANÝ INDEX COLUMNSTORE.

Použití příkazu CTAS budete moct zadat současně distribuci tabulkových dat, jakož i struktura tabulky typu. Chcete-li převést předchozího příkladu na CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> Pokud se pouze snažíte změnit index vaše `CTAS` operace a zdrojová tabulka je provádět distribuci hodnot hash vaše `CTAS` operace provede nejlépe, pokud chcete zachovat stejný typ sloupce a data distribuce. Tím se vyhnete pro různé distribuce přesun dat v průběhu operace, což je efektivnější.

## <a name="using-ctas-to-copy-a-table"></a>Použití příkazu CTAS k kopírování tabulky

Například jeden z nejčastěji používaných používá `CTAS` vytváří kopii tabulky. Chcete-li změnit jazyka DDL. Pokud například jste původně vytvořili tabulky jako `ROUND_ROBIN` a chcete ho změnit na tabulka distribuovaná na sloupci, `CTAS` je, jak by změnit sloupec distribuce. `CTAS` je také možné změnit typy rozdělení do oddílů, indexování nebo sloupec.

Řekněme, že jste vytvořili tuto tabulku pomocí výchozí typ distribuce `ROUND_ROBIN` bez zadání distribučního sloupce v `CREATE TABLE`.

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

Teď chcete vytvořit novou kopii této tabulky s clusterovaný Columnstore Index, takže můžete využít výkon tabulek Clusterované Columnstore. Také chcete distribuovat této tabulky na ProductKey, protože jsou předvídání spojení na tomto sloupci a chcete zabránit přesunu dat během spojení ProductKey. A konečně můžete také chtít přidat dělení na OrderDateKey, takže můžete rychle odstranit stará data odstranit staré oddíly. Tady je příkaz CTAS staré tabulky budou zkopírovány do nové tabulky.

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

Nakonec můžete přejmenovat tabulek do odkládacího souboru v nové tabulky a pak vyřadit staré tabulky.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="using-ctas-to-work-around-unsupported-features"></a>Použití příkazu CTAS obejít nepodporované funkce

Funkce CTAS lze také vyřešit řadu nepodporované funkce uvedené níže. Tato metoda může být velmi často se situace win/win jako pouze váš kód bude kompatibilní s, ale to se často spustí rychleji na SQL Data Warehouse. Tento výkon je výsledkem jeho plně paralelizované návrhu. Mezi scénáře, které je možné pracovat s CTAS kolem patří:

* SPOJENÍ standardu ANSI na aktualizace
* Spojení standardu ANSI na odstranění
* SLOUČENÍ – příkaz

> [!NOTE]
> Zamyslete se nad "CTAS první". Řešení potíží pomocí `CTAS` je obecně dobrý nápad, i v případě, že vytváříte více dat ve výsledku.

## <a name="ansi-join-replacement-for-update-statements"></a>Nahrazení spojení standardu ANSI pro příkazy update

Může se stát, že máte komplexní aktualizace, které spojí dohromady pomocí syntaxe spojení standardu ANSI provádět aktualizace nebo odstranění více než dvě tabulky.

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

Původní dotaz může mít podívali podobné následujícímu příkladu:

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

SQL Data Warehouse nepodporuje spojení standardu ANSI `FROM` klauzuli `UPDATE` příkazu, aniž byste ho upravovali nelze použít v předchozím příkladu.

Můžete použít kombinaci `CTAS` a implicitní spojení k nahrazení v předchozím příkladu:

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

## <a name="ansi-join-replacement-for-delete-statements"></a>Odstranit nahrazení spojení standardu ANSI pro příkazy

Někdy je nejlepším řešením pro odstranění dat použít `CTAS`, zejména u `DELETE` příkazy, které používají ANSI připojení syntaxe. Důvodem je, že SQL Data Warehouse nepodporuje spojení standardu ANSI `FROM` klauzuli `DELETE` příkazu. Místo odstranění dat, vyberte data, která chcete zachovat.

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

Příkazy Merge lze nahradit, alespoň v části použití příkazu CTAS. VKLÁDÁNÍ a aktualizace můžete zkombinovat do jednoho příkazu. Odstraněné záznamy, které by měly být omezené z `SELECT` příkaz chcete vynechat, nechte ve výsledcích.

Následující příklad je určený pro UPSERT:

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
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Funkce CTAS doporučení: Explicitně uvést typ dat a možnost použití hodnoty Null z výstupu

Při přenesení kódu, můžete se setkat, že spustíte mezi tímto typem kódování vzorců:

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

Instinktivně si možná myslíte tento kód by měl migrovat do příkazu CTAS a budete mít správný. Je však skryté problém tady.

Následující kód nevydává stejný výsledek:

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

Všimněte si, že sloupec "výsledek" přenesou data hodnot typu a možnost použití hodnoty Null z výrazu. Provádění data předávat typ může způsobit drobné rozdíly v hodnotách pokud nevyloučíte.

Zkuste v tomto příkladu:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Hodnota uložená pro výsledek se liší. Trvalé hodnoty ve sloupci výsledek při použití v jiných výrazech, stane chyba ještě více významné.

![Výsledky příkazu CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

To je důležité pro data migrace. I když druhý dotaz je pravděpodobně přesnější dojde k problému. Data bude odlišná v porovnání s zdrojového systému a, která vede na otázky integrity migrace. Toto je jedna z těchto výjimečných případech, kdy "nesprávný" odpověď je ve skutečnosti ten správný!

Z důvodu, že můžeme vidět rozdíl mezi dvěma výsledky je implicitní přetypování. V prvním příkladu definuje tabulce definici sloupce. Při vložení řádku dochází implicitní převod typu. V druhém příkladu neexistuje žádný implicitní převod typu jako výrazu definuje datový typ sloupce. Všimněte si také, že sloupce v druhém příkladu byl definovaný jako sloupec s možnou hodnotou Null vzhledem k tomu v prvním příkladu má není. Pokud tabulka byla vytvořena v prvním příkladu, byla explicitně definovány možnosti použití hodnoty NULL sloupce. V druhém příkladu byla ponechána výrazu a ve výchozím nastavení, výsledkem by byla definice hodnotu NULL.

Jak tyto problémy vyřešit, musíte explicitně nastavit možnosti použití hodnoty Null a převodu typu vyberte část příkazu CTAS. V části Vytvoření tabulky nelze nastavit tyto vlastnosti.

Následující příklad ukazuje, jak kód opravit:

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
* ISNULL slouží k vynucení možnosti použití hodnoty Null nejsou COALESCE
* ISNULL je nejzevnější funkce
* Druhá část ISNULL je konstanta, 0

> [!NOTE]
> Pro možnost použití hodnoty Null se správně nastavit je potřeba použít ISNULL a ne COALESCE. COALESCE není deterministické funkce a tedy výsledkem výrazu bude vždy s možnou hodnotou Null. ISNULL se liší. Není deterministický. Proto když je druhá část funkce ISNULL konstanta nebo literál pak výsledná hodnota bude NOT NULL.

Zajištění integrity výpočtů je také důležité pro přepínání oddílů tabulky. Představte si, že máte této tabulce jsou definované jako tabulka faktů:

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

Ale velikost pole je vypočtené výraz. není součástí zdrojová data.

Chcete-li vytvořit dělené datové sady, můžete pomocí následujícího kódu:

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

Dotaz spustí naprosto bez problémů. Problém je při pokusu provést přepnutí oddílu. Definice tabulek se neshodují. Chcete-li definice tabulky shodovat, musí být upravena přidat příkazu CTAS `ISNULL` funkce k zachování atributu možnosti použití hodnoty NULL sloupce.

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

Uvidíte, že typ konzistence a Správa vlastností možnosti použití hodnoty Null na provedení příkazu CTAS je osvědčené technické praxe nejlepší. Pomáhá zachovat integritu ve svých výpočtech a také zajišťuje, že je možné přepínání oddílů.

Odkazovat [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) dokumentaci. Funkce CTAS je jedním z nejdůležitějších příkazy ve službě Azure SQL Data Warehouse. Zajistěte, aby že ho důkladně porozumět.

## <a name="next-steps"></a>Další postup

Další tipy pro vývoj najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

