---
title: Použití možností Group by
description: Tipy pro implementaci skupin podle možností v synapse fondu SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 260bd7cc6402a7cb4ed17d004d0f61b7462c51d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213444"
---
# <a name="group-by-options-in-synapse-sql-pool"></a>Možnosti seskupení v synapse fondu SQL

V tomto článku najdete tipy k implementaci skupin podle možností ve fondu SQL.

## <a name="what-does-group-by-do"></a>Co dělá GROUP BY?

Klauzule [Group by](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL agreguje data na souhrnnou sadu řádků. Seskupit podle obsahuje některé možnosti, které fond SQL nepodporuje. Tyto možnosti mají následující alternativní řešení:

* Seskupit podle se SOUHRNem
* SADY SESKUPENÍ
* Seskupit podle s datovou krychlí

## <a name="rollup-and-grouping-sets-options"></a>Možnosti sady možností Shrnutí a seskupení

Nejjednodušší možností je použít SJEDNOCENí k provedení souhrnu místo toho, aby se mohla spoléhat na explicitní syntaxi. Výsledek je přesně stejný.

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

Pomocí předchozího příkladu je prvním krokem definování datové krychle, která definuje všechny úrovně agregace, které chceme vytvořit.

Poznamenejte si vzájemné spojení dvou odvozených tabulek, protože vygeneruje všechny úrovně pro nás. Zbytek kódu je pro formátování:

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

Následující obrázek ukazuje výsledky CTAS:

![Seskupit podle datové krychle](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Druhým krokem je zadání cílové tabulky pro ukládání dočasných výsledků:

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

Třetí krok je smyčka nad naši datovou krychlí sloupců, které provádí agregaci. Dotaz se spustí jednou pro každý řádek v #Cube dočasné tabulce. Výsledky jsou uloženy v #Results dočasné tabulce:

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

Nakonec můžete vrátit výsledky čtením z dočasné tabulky #Results:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Rozbalením kódu v sekcích a generováním konstrukce smyčky se kód bude lépe spravovat a udržovatelný.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).
