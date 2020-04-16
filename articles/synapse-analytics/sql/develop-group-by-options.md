---
title: Použití možností GROUP BY v synapse SQL
description: Synapse SQL umožňuje vývoj řešení implementací různých možností GROUP BY.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 261f75344d250ae8a8d9687f4bcd80535d11716b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429041"
---
# <a name="group-by-options-in-synapse-sql"></a>Možnosti SESKAZeNÍ v Synapse SQL
Synapse SQL umožňuje vývoj řešení implementací různých možností GROUP BY. 

## <a name="what-does-group-by-do"></a>Co dělá GROUP BY

Klauzule [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL agreguje data do souhrnné sady řádků.

SQL on-demand podporuje celou řadu možností GROUP BY. Fond SQL podporuje omezený počet možností GROUP BY.

## <a name="group-by-options-supported-in-sql-pool"></a>Možnosti GROUP BY podporované ve fondu SQL

GROUP BY má některé možnosti, které fond SQL nepodporuje. Tyto možnosti mají řešení, která jsou následující:

* SKUPINA BY S ROLLUP
* SKUPINY SAD
* SKUPINA BY S KOSTKOU

### <a name="rollup-and-grouping-sets-options"></a>Možnosti sad souhrnů a seskupení

Nejjednodušší možností je použít UNION ALL k provedení souhrnu spíše než se spoléhat na explicitní syntaxi. Výsledek je přesně stejný

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

### <a name="cube-options"></a>Volby datové krychle

Je možné vytvořit group by with cube pomocí union all přístupu. Problém je, že kód může rychle stát těžkopádné a těžkopádné. Chcete-li tento problém zmírnit, můžete použít tento pokročilejší přístup.

Prvním krokem je definovat 'krychle', která definuje všechny úrovně agregace, které chceme vytvořit. Poznamenejte si cross join dvou odvozených tabulek, protože generuje všechny úrovně. Zbytek kódu je k dispozici pro formátování.

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

Následující obrázek znázorňuje výsledky [příkazu VYTVOŘIT TABULKU JAKO SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest):

![Seskupit podle krychle](./media/develop-group-by-options/develop-group-by-cube.png)

Druhým krokem je určení cílové tabulky pro ukládání průběžných výsledků:

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

Třetím krokem je smyčka přes krychli sloupců provádějících agregaci. Dotaz bude spuštěn jednou pro každý řádek v #Cube dočasné tabulce. Výsledky jsou uloženy v tabulce #Results temp:

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

Další tipy pro vývoj najdete v [tématu přehled vývoje](develop-overview.md).
