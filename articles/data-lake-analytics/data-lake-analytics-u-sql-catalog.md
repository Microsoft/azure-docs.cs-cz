---
title: Použití katalogu U-SQL v Azure Data Lake Analytics
description: Naučte se používat katalog U-SQL ke sdílení kódu a dat. Vytvořte funkce vracející tabulku, vytvořte zobrazení, vytvořte tabulky a Dotazujte je.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/09/2017
ms.openlocfilehash: f92aadc8ccf18dd91b5dd4b35285f60b174e4cf7
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220070"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Začínáme s katalogem U-SQL v Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Vytvoření TVF

V předchozím skriptu U-SQL se pokusíte použít EXTRAKCi pro čtení ze stejného zdrojového souboru. Funkce vracející tabulku U-SQL (TVF) umožňuje zapouzdřit data pro budoucí opakované použití.  

Následující skript vytvoří TVF s názvem `Searchlog()` ve výchozí databázi a schématu:

```usql
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

Následující skript vám ukáže, jak používat TVF, které jste definovali v předchozím skriptu:

```usql
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

Pokud máte jeden výraz dotazu místo TVF, můžete k zapouzdření tohoto výrazu použít zobrazení U-SQL.

Následující skript vytvoří zobrazení nazvané `SearchlogView` ve výchozí databázi a schématu:

```usql
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

Následující skript demonstruje použití definovaného zobrazení:

```usql
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

## <a name="create-tables"></a>Vytváření tabulek
Stejně jako u tabulek relačních databází můžete pomocí U-SQL vytvořit tabulku s předdefinovaným schématem nebo vytvořit tabulku, která bude toto schéma odvodit z dotazu, který tuto tabulku naplní (označovaný také jako CREATE TABLE jako SELECT nebo CTAS).

Pomocí následujícího skriptu vytvořte databázi a dvě tabulky:

```usql
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
Můžete zadávat dotazy na tabulky, například ty, které byly vytvořeny v předchozím skriptu, stejně jako datové soubory. Místo vytvoření sady řádků pomocí EXTRAKCe teď můžete odkazovat na název tabulky.

Chcete-li číst z tabulek, upravte transformační skript, který jste použili dříve:

```usql
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
 >V současné době nemůžete spustit příkaz SELECT na tabulce ve stejném skriptu jako ten, ve kterém jste tabulku vytvořili.

## <a name="next-steps"></a>Další kroky
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
