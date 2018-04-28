---
title: Skupiny pomocí možnosti v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro implementaci skupiny pomocí možnosti v Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0548983df23b158385783ac777b23268b5ac7d01
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Seskupit podle možností v SQL Data Warehouse
Tipy pro implementaci skupiny pomocí možnosti v Azure SQL Data Warehouse na vývoj řešení.

## <a name="what-does-group-by-do"></a>GROUP BY k čemu slouží?

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) klauzule T-SQL agreguje data souhrnu sadu řádků. GROUP BY obsahuje některé možnosti, které SQL Data Warehouse nepodporuje. Tyto možnosti mají alternativní řešení.

Tyto možnosti jsou

* GROUP BY se ZAHRNUTÍM
* GROUPING SETS
* Klauzule GROUP BY datové KRYCHLE

## <a name="rollup-and-grouping-sets-options"></a>Souhrn a seskupení nastaví možnosti
Nejjednodušší možnost je UNION ALL místo toho chcete použít k provedení kumulativní z nespoléhá se na explicitní syntaxe. Výsledkem je přesně stejný

Následující příklad pomocí příkazu GROUP BY s souhrn možností:
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

Pomocí KUMULATIVNÍ požadavky v předchozím příkladu agregace následující:

* Zemí a oblastí
* Země
* Celkový součet

Pokud chcete nahradit KUMULATIVNÍ a vrátí stejné výsledky, můžete použít UNION ALL a explicitně zadáte požadované agregace:

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

Pokud chcete nahradit GROUPING SETS, ukázka princip platí. Potřebujete vytvořit UNION ALL oddíly pro úrovně agregace, které chcete zobrazit.

## <a name="cube-options"></a>Možnosti datové krychle
Je možné vytvořit SKUPINU podle s datovou KRYCHLI UNION ALL přístup. Problém je, že kód může být pracné a nepraktické. Toto riziko lze snížit, můžete použít tento pokročilejší přístup.

Použijeme v předchozím příkladu.

Prvním krokem je definování 'datové krychle, která definuje všechny úrovně agregace, který chcete vytvořit. Je důležité si poznamenejte CROSS JOIN dvě odvozené tabulky. Tím se vytvoří všechny úrovně pro nás. Zbytek kód skutečně existuje pro formátování.

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

Na obrázku výsledky funkce CTAS:

![Seskupit podle datové krychle](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Druhým krokem je zadání cílové tabulku pro ukládání dočasné výsledky:

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

Třetí krok je smyčku naší datové krychle sloupců provádění agregace. Budou spuštěny jednou pro každý řádek v dočasné tabulce #Cube a ukládání výsledků do dočasné tabulky #Results dotazu

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

A konečně můžete se vrátit výsledky podle jednoduše čtení z dočasné tabulky #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Kód rozdělení do oddílů a generování opakování konstrukce, kód se změní na správu a udržovatelný.

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj, najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

