---
title: Použití možností Group by v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro implementaci skupin podle možností v Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2f6614f32c31338c9cf4f00307c475db4e02f553
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479636"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Seskupit podle možností v SQL Data Warehouse
Tipy pro implementaci skupin podle možností v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="what-does-group-by-do"></a>Co dělá GROUP BY?

Klauzule [Group by](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL agreguje data na souhrnnou sadu řádků. GROUP BY obsahuje některé možnosti, které SQL Data Warehouse nepodporuje. Tyto možnosti mají alternativní řešení.

Tyto možnosti jsou

* Seskupit podle se SOUHRNem
* SADY SESKUPENÍ
* Seskupit podle s datovou krychlí

## <a name="rollup-and-grouping-sets-options"></a>Možnosti sady možností Shrnutí a seskupení
Nejjednodušší možností je použít místo toho SJEDNOCENí, aby se souhrn prováděl místo spoléhání na explicitní syntaxi. Výsledek je přesně stejný

Následující příklad používá příkaz GROUP BY s možností ROLLUP:
```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Pomocí SOUHRNu si předchozí příklad vyžádá následující agregace:

* Země a oblast
* Country
* Celkový součet

Chcete-li nahradit souhrn a vrátit stejné výsledky, můžete použít příkaz UNION ALL a explicitně zadat požadované agregace:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Chcete-li nahradit sady seskupení, použije se princip ukázky. Pro úrovně agregace, které chcete zobrazit, stačí vytvořit pouze oddíly SJEDNOCENí.

## <a name="cube-options"></a>Možnosti datové krychle
Je možné vytvořit skupinu pomocí datové krychle s použitím přístupu UNION ALL. Problémem je to, že kód může být rychle náročný a nepraktický. Pro zmírnění tohoto problému můžete použít tento pokročilejší přístup.

Pojďme použít výše uvedený příklad.

Prvním krokem je definování datové krychle, která definuje všechny úrovně agregace, které chceme vytvořit. Je důležité si povšimnout VZÁJEMNÉho spojení dvou odvozených tabulek. Tím se vygeneruje všechny úrovně pro nás. Zbytek kódu je opravdu pro formátování.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Následující příklad ukazuje výsledky CTAS:

![Seskupit podle datové krychle](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Druhým krokem je zadání cílové tabulky pro uložení dočasných výsledků:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Třetí krok je smyčka nad naši datovou krychlí sloupců, které provádí agregaci. Dotaz se spustí jednou pro každý řádek v #Cube dočasné tabulce a výsledky se uloží do dočasné tabulky #Results

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Nakonec můžete vracet výsledky pouhým čtením z #Results dočasné tabulky.

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Poškozením kódu v sekcích a generováním konstrukce smyčky se kód bude lépe spravovat a udržovatelný.

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).

