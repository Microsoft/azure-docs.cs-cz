---
title: Použití katalogu U-SQL v Azure Data Lake Analytics
description: Přečtěte si, jak pomocí katalogu U-SQL sdílet kód a data. Vytvářejte funkce s hodnotou tabulky, vytvářejte zobrazení, vytvářejte tabulky a dotazujte se na ně.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: f3b9f14be4422373fb30f8c3d4909fd9c9546fdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672848"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Začínáme s katalogem U-SQL v Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Vytvoření TVF

V předchozím skriptu U-SQL jste zopakovali použití aplikace EXTRACT ke čtení ze stejného zdrojového souboru. Pomocí funkce u-SQL s hodnotou tabulky (TVF) můžete zapouzdřit data pro budoucí opakované použití.  

Následující skript vytvoří TVF `Searchlog()` volané ve výchozí databázi a schématu:

```
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
RETURN;
END;
```

Následující skript ukazuje, jak používat TVF, který byl definován v předchozím skriptu:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Vytváření zobrazení

Pokud máte jeden dotaz výraz, namísto TVF můžete použít U-SQL VIEW zapouzdřit tento výraz.

Následující skript vytvoří zobrazení `SearchlogView` volané ve výchozí databázi a schématu:

```
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

Následující skript ukazuje použití definovaného zobrazení:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>Vytvoření tabulek
Stejně jako u relačních databázových tabulek můžete pomocí u-SQL vytvořit tabulku s předdefinovaným schématem nebo vytvořit tabulku, která odvodí schéma z dotazu, který vyplní tabulku (také známou jako CREATE TABLE AS SELECT nebo CTAS).

Vytvořte databázi a dvě tabulky pomocí následujícího skriptu:

```
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Dotazy na tabulky
Tabulky můžete dotazovat, například tabulky vytvořené v předchozím skriptu, stejným způsobem jako datové soubory. Místo vytváření sady řádků pomocí aplikace EXTRACT můžete nyní odkazovat na název tabulky.

Chcete-li číst z tabulek, upravte dříve použitý transformační skript:

```
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >V současné době nelze spustit SELECT v tabulce ve stejném skriptu jako ten, kde jste tabulku vytvořili.

## <a name="next-steps"></a>Další kroky
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
