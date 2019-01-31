---
title: Skupiny pomocí možnosti v Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro implementaci skupinu podle možnosti ve službě Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 69a76ae9d6f355fe401b438ec2ab89d6606ba46c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55463476"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Seskupit podle možnosti ve službě SQL Data Warehouse
Tipy pro implementaci skupinu podle možnosti ve službě Azure SQL Data Warehouse pro vývoj řešení.

## <a name="what-does-group-by-do"></a>GROUP BY k čemu slouží?

[Group](/sql/t-sql/queries/select-group-by-transact-sql) klauzule T-SQL agreguje data do souhrnu sady řádků. GROUP BY obsahuje některé možnosti, které SQL Data Warehouse nepodporuje. Tyto možnosti mají alternativní řešení.

Tyto možnosti jsou

* Klauzule GROUP BY souhrn
* GROUPING SETS
* Klauzule GROUP BY datové KRYCHLE

## <a name="rollup-and-grouping-sets-options"></a>Rollup a grouping sets možnosti
Je nejjednodušší možnost UNION ALL místo toho použít k provedení této kumulativní nespoléhat se na explicitní syntaxe. Výsledkem je přesně tatáž

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

Pomocí souhrnné požadavků v předchozím příkladu následující agregace:

* Země a oblasti
* Země
* Celkový součet

Nahraďte KUMULATIVNÍ a vrátí stejné výsledky, můžete použít UNION ALL a explicitně určit požadovanou agregaci:

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

Nahradit GROUPING SETS, ukázkové zásady platí. Potřebujete vytvořit unií všechny oddíly úrovních agregace, které chcete zobrazit.

## <a name="cube-options"></a>Možnosti datové krychle
Je možné vytvořit SKUPINU podle s datovou KRYCHLI UNION ALL přístup. Problém je, že kód může být náročné a nepraktické. Pokud chcete tento problém zmírnit, můžete použít toto rozšířené přístup.

Použijeme výše uvedený příklad.

Prvním krokem je definování "datové krychle", který definuje všechny úrovně seskupení, který chcete vytvořit. Je důležité si poznamenejte křížové spojení dvou odvozených tabulek. Tím se vygeneruje všechny úrovně pro nás. Zbývající část kódu je ve skutečnosti pro formátování.

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

Následuje ukázka výsledky příkazu CTAS:

![Seskupit datové krychle](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Druhým krokem je určit cílovou tabulku pro ukládání dočasné výsledků:

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

Je třetí krok k vytvoření smyčky přes naše datová krychle sloupců provádí agregaci. Dotaz se spustí jednou pro každý řádek v dočasné tabulce #Cube a ukládání výsledků do dočasné tabulky #Results

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

Kód rozdělení do oddílů a generování uvozuje konstruktor cyklu, kód stane spravovatelné a udržovatelný.

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

