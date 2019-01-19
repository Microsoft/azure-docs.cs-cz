---
title: Začínáme s katalogem U-SQL ve službě Azure Data Lake Analytics
description: Další informace o použití katalogu U-SQL ke sdílení kódu a data.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: a6faa7037ccbacc0547401dd52bb3b19abd1c474
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54401589"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Začínáme s katalogem U-SQL ve službě Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Vytvořte TVF

V předchozí skript U-SQL opakované použití EXTRAKCE číst ze stejného zdrojového souboru. S U-SQL funkce vracející tabulku (TVF) lze zapouzdřit data pro budoucí využití.  

Tento skript vytvoří TVF volá `Searchlog()` ve výchozí databázi a schéma:

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

Tento skript ukazuje, jak používat funkci TVF, která byla definována v předchozím scénáři:

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

Pokud máte jeden dotaz výrazu, namísto TVF můžete zobrazit U-SQL k zapouzdření tento výraz.

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

Tento skript ukazuje použití definované zobrazení:

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
Jako u tabulek relační databáze, pomocí U-SQL můžete vytvořit tabulku s předdefinovaným schématem nebo vytvořit tabulku, která odvodí schéma z dotazu, který naplní tabulku (označované také jako CREATE TABLE AS SELECT nebo CTAS).

Vytvoření databáze a dvou tabulek pomocí následujícího skriptu:

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
Můžete zadávat dotazy tabulkami, jako jsou ty v předchozím scénáři stejným způsobem, budete dotazovat datových souborů. Místo vytváření sady řádků pomocí EXTRAKCE, můžete teď mohou odkazovat na název tabulky.

Číst z tabulek, upravte transformace skript, který jste použili dříve:

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
 >SELECT nelze v současné době spustit na tabulku v stejný skript jako ta, ve které jste vytvořili v tabulce.

## <a name="next-steps"></a>Další kroky
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
