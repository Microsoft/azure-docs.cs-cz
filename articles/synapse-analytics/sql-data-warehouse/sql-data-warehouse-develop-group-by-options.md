---
title: Použití seskupit podle možností
description: Tipy pro implementaci skupiny podle možností v fondu Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 28ac075d043f7605b6dfdac6879063fbe9308123
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619045"
---
# <a name="group-by-options-in-synapse-sql-pool"></a>Seskupit podle možností v fondu Synapse SQL

V tomto článku najdete tipy pro implementaci skupiny podle možností ve fondu SQL.

## <a name="what-does-group-by-do"></a>Co dělá GROUP BY?

Klauzule [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL agreguje data do souhrnné sady řádků. GROUP BY má některé možnosti, které fond SQL nepodporuje. Tyto možnosti mají řešení, která jsou následující:

* SKUPINA BY S ROLLUP
* SKUPINY SAD
* SKUPINA BY S KOSTKOU

## <a name="rollup-and-grouping-sets-options"></a>Možnosti sad souhrnů a seskupení

Nejjednodušší možností je použít UNION ALL k provedení souhrnu spíše než se spoléhat na explicitní syntaxi. Výsledek je naprosto stejný.

Následující příklad pomocí příkazu GROUP BY s možností ROLLUP:
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

Pomocí funkce ROLLUP předchozí příklad požaduje následující agregace:

* Země a region
* Země
* Celkový součet

Chcete-li nahradit funkce ROLLUP a vrátit stejné výsledky, můžete použít UNION ALL a explicitně zadat požadované agregace:

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

Chcete-li nahradit sady seskupení, použije se princip vzorku. Stačí vytvořit UNION VŠECHNY oddíly pro úrovně agregace, které chcete zobrazit.

## <a name="cube-options"></a>Volby datové krychle
Je možné vytvořit group by with cube pomocí union all přístupu. Problém je, že kód může rychle stát těžkopádné a těžkopádné. Chcete-li tento problém zmírnit, můžete použít tento pokročilejší přístup.

Pomocí předchozího příkladu je prvním krokem definování "krychli", která definuje všechny úrovně agregace, které chceme vytvořit. 

Vezměte na vědomí CROSS JOIN dvou odvozených tabulek, protože to generuje všechny úrovně pro nás. Zbytek kódu je k dispozici pro formátování:

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

Následující obrázek znázorňuje výsledky CTAS:

![Seskupit podle krychle](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Druhým krokem je určení cílové tabulky pro uložení průběžných výsledků:

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

Třetím krokem je smyčka přes naši krychli sloupců provádějících agregaci. Dotaz bude spuštěn jednou pro každý řádek v #Cube dočasné tabulce. Výsledky jsou uloženy v tabulce #Results temp:

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

Nakonec můžete vrátit výsledky čtením z #Results dočasné tabulky:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Rozdělením kódu do oddílů a generováním cyklinky konstrukce, kód se stává více zvládnutelné a udržovatelné.

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v [tématu přehled vývoje](sql-data-warehouse-overview-develop.md).

