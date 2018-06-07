---
title: Začínáme s katalogem U-SQL v Azure Data Lake Analytics
description: Další informace o použití katalogu U-SQL pro sdílení kódu a data.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: saveenr
editor: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: 35a39733987eba7060049db7005c1f6bc0058e63
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624348"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Začínáme s katalogem U-SQL v Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Vytvoření TVF

V předchozích skript U-SQL opakované použití EXTRAKCE číst ze stejné zdrojového souboru. S U-SQL funkce vracející tabulku (TVF) může zapouzdřit data pro budoucí využití.  

Tento skript vytvoří TVF, názvem `Searchlog()` ve výchozí databázi a schéma:

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

Tento skript je ukázkou, jak používat funkci TVF, která byla definována v předchozí skript:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SerachLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Vytvoření zobrazení

Pokud máte jeden dotaz výrazu, místo TVF můžete zobrazení U-SQL pro zapouzdření tento výraz.

Tento skript vytvoří zobrazení s názvem `SearchlogView` ve výchozí databázi a schéma:

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

Následující skript demonstruje použití definované zobrazení:

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
Jako s tabulkami relační databáze pomocí U-SQL můžete vytvořit tabulku s předdefinovaným schématem nebo vytvořit tabulku, která odvodí, že schéma z dotazu, který naplní tabulky (také označované jako CREATE TABLE AS SELECT nebo funkce CTAS).

Vytvořte databázi a dvě tabulky pomocí následující skript:

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

## <a name="query-tables"></a>Dotazu na tabulky
Můžete dotazovat tabulkami, jako jsou ty, vytvořené v předchozí skript stejným způsobem dotazování datové soubory. Místo vytvoření pomocí EXTRAKCE sadu řádků, teď můžete odkazovat na název tabulky.

Čtení z tabulek, upravte transformace skript, který jste použili dříve:

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
 >S výběrem nelze v současné době spustit v tabulce ve skriptu stejný jako ten, na které jste vytvořili v tabulce.

## <a name="next-steps"></a>Další kroky
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
