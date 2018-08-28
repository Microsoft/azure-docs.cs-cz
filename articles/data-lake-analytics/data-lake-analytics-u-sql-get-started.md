---
title: Začínáme s jazykem U-SQL ve službě Azure Data Lake Analytics
description: Naučte se základy jazyka U-SQL ve službě Azure Data Lake Analytics.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 1822ac9b539aa196601c6c07ccc8d0544fd5f3dd
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042424"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Začínáme s jazykem U-SQL ve službě Azure Data Lake Analytics
U-SQL je jazyk, který spojuje deklarativní SQL pomocí imperativního jazyka C# umožňuje zpracovávat data v libovolném měřítku. Prostřednictvím funkce škálovatelné a distribuované dotazu U-SQL můžete efektivně analyzovat data napříč relačními úložišť, jako je Azure SQL Database. Pomocí U-SQL můžete zpracovávání nestrukturovaných dat použitím schéma při čtení a vložení vlastní logiky a uživatelem definovanými funkcemi. Navíc U-SQL obsahuje rozšíření, která umožňuje velice přesně kontrolovat, jak provádět ve velkém měřítku. 

## <a name="learning-resources"></a>Studijní materiály

* [Kurzu U-SQL](http://aka.ms/usqltutorial) poskytuje návod většinu jazyk U-SQL. Tento dokument se doporučuje pro všechny vývojáře, kteří chtějí naučit jazyk U-SQL pro čtení.
* Podrobné informace o **syntaxi jazyka U-SQL**, najdete v článku [referenční příručka jazyka U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).
* K pochopení **filozofie návrhu U-SQL**, najdete v příspěvku blogu Visual Studio [Představujeme jazyk U-SQL – objekt, který usnadňuje velké objemy dat zpracování](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Požadavky

Než budete postupovat ukázky U-SQL v tomto dokumentu, přečtěte si a dokončete [kurz: vývoj U-SQL skriptů pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Tento kurz vysvětluje mechanismus pomocí U-SQL s Azure Data Lake Tools pro Visual Studio.

## <a name="your-first-u-sql-script"></a>Váš první skript U-SQL

Následující skript U-SQL je jednoduchý a umožňují prozkoumat mnoho aspektů týkajících se jazyk U-SQL.

```
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

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Tento skript nemá žádné kroky transformace. Načte ze zdrojového souboru, který volá `SearchLog.tsv`schematizes ho a zapíše sadu řádků zpět do souboru s názvem SearchLog-first-u-sql.csv.

Všimněte si, že otazník u dat zadejte `Duration` pole. To znamená, že `Duration` pole může mít hodnotu null.

### <a name="key-concepts"></a>Klíčové koncepty
* **Sada řádků proměnné**: každý výraz dotazu, který generuje sadu řádků je možné přiřadit k proměnné. U-SQL následuje vzor proměnné pojmenování T-SQL (`@searchlog`, například) ve skriptu.
* **EXTRAHOVAT** – klíčové slovo číst data ze souboru a definuje schéma při čtení. `Extractors.Tsv` je integrované Extraktor U-SQL pro kartu oddělenými soubory. Můžete vyvíjet vlastních extraktorů.
* **Výstup** zapisuje data ze sady řádků do souboru. `Outputters.Csv()` je integrované outputter U-SQL k vytvoření souboru čárkami oddělených hodnot. Můžete vyvíjet vlastní výstupní moduly.

### <a name="file-paths"></a>Cesty k souborům

EXTRAKCE a výstup příkazů pomocí cesty k souborům. Cesty k souboru může být absolutní nebo relativní:

Tato následující absolutní cestu k souboru odkazuje na soubor v Data Lake Store s názvem `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Následující cesta k souboru začíná `"/"`. Odkazuje na soubor ve výchozí účet Data Lake Store:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Použít skalární proměnné

Skalární proměnné můžete použít také snazší údržbu vašeho skriptu. Předchozí skript U-SQL lze také zapsat jako:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformace sady řádků

Použití **vyberte** transformace sady řádků:

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

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

Používá klauzuli WHERE [jazyka C# logický výraz](https://msdn.microsoft.com/library/6a71f45d.aspx). Výraz jazyka C# můžete použít vlastní výrazy a funkce. Dokonce můžete provést složitější filtrování podle jejich sloučením s spojky logické (a) a disjunctions (OR).

Následující skript používá metodu DateTime.Parse() a spojení.

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

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >Druhý dotaz funguje na výsledek první sadu řádků, které vytvoří složeného dva filtry. Můžete také znovu použít název proměnné a jsou lexikálně obor názvů.

## <a name="aggregate-rowsets"></a>Agregační sady řádků
U-SQL vám známou klauzule ORDER BY, GROUP BY a agregace.

Následující dotaz najde celková doba trvání v jedné oblasti a pak zobrazí horní části pět doby trvání v pořadí.

Sady řádků U-SQL Nezachovávat hodnotu jejich pořadí pro další dotaz. Proto k seřazení výstup, je třeba přidat klauzule ORDER BY výstup příkazu:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

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

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

V klauzuli ORDER BY v U-SQL vyžaduje použití klauzulí FETCH ve výrazu SELECT.

Klauzule HAVING U-SQL je možné omezit výstup do skupin, které splňují zadanou podmínku HAVING:

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

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
        GROUP BY Region
        HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Pro scénáře pokročilých agregace, naleznete v referenční dokumentaci U-SQL pro [agregovat, analýzy a odkazují na funkce](https://msdn.microsoft.com/library/azure/mt621335.aspx)

## <a name="next-steps"></a>Další postup
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
