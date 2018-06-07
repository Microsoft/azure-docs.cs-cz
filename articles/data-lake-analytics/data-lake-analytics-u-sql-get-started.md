---
title: Začínáme s jazykem U-SQL v Azure Data Lake Analytics
description: Naučte se základy jazyka U-SQL v Azure Data Lake Analytics.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 2903046154808da5113f7b32a04bbfba254a07ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623447"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Začínáme s jazykem U-SQL v Azure Data Lake Analytics
U-SQL je jazyk, který kombinuje deklarativní SQL s imperativní jazyka C# k umožňují zpracování dat v jakémkoli měřítku. Prostřednictvím funkce škálovatelné a distribuovaných dotazů U-SQL můžete efektivně analyzovat data napříč relační úložiště, jako je například Azure SQL Database. Pomocí U-SQL můžete zpracovat nestrukturovaných dat tak, že použití schématu na čtení a vložení vlastní logiky a funkcí UDF. Navíc U-SQL zahrnuje rozšíření, která poskytuje jemně odstupňovanou kontrolu nad postup provést ve velkém měřítku. 

## <a name="learning-resources"></a>Studijní materiály

* [U-SQL kurzu](http://aka.ms/usqltutorial) poskytuje návod s asistencí pro většinu jazykem U-SQL. Tento dokument se doporučuje čtení pro všechny vývojáře, kteří požadují další U-SQL.
* Podrobné informace o **syntaxe jazyka U-SQL**, najdete v článku [referenční příručka jazyka U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).
* Zjistit, **filosofie návrhu U-SQL**, naleznete v příspěvku blogu Visual Studio [představení U-SQL – A jazyk, který usnadňuje Big zpracování dat](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Požadavky

Než přejdete pomocí U-SQL ukázky v tomto dokumentu, přečtěte si a dokončete [kurz: vývoj U-SQL skriptů pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Tento kurz vysvětluje mechanismů U-SQL pomocí nástrojů Azure Data Lake pro Visual Studio.

## <a name="your-first-u-sql-script"></a>Váš první skript U-SQL

Následující skript U-SQL je snadná a umožňují nám prozkoumat mnoho aspektů jazykem U-SQL.

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

Tento skript nemá žádné kroky transformace. Kromě toho přečte ze zdrojového souboru s názvem `SearchLog.tsv`, schematizes ho a zapíše sadu řádků zpět do souboru s názvem SearchLog-first-u – sql.csv.

Všimněte si znaky otazníku vedle data, zadejte `Duration` pole. Znamená to, že `Duration` pole může mít hodnotu null.

### <a name="key-concepts"></a>Klíčové koncepty
* **Sada řádků proměnné**: každý výraz dotazu, který vytvoří sadu řádků lze přiřadit k proměnné. U-SQL se následující T-SQL proměnné pojmenování (`@searchlog`, například) ve skriptu.
* **EXTRAHOVAT** – klíčové slovo čte data ze souboru a definuje schéma pro čtení. `Extractors.Tsv` je integrované Extraktor U-SQL pro soubory karta oddělených hodnot. Můžete vyvíjet vlastní extraktory.
* **Výstup** zapisuje data ze sady řádků do souboru. `Outputters.Csv()` je integrované outputter U-SQL vytvořte soubor oddělených čárkou. Můžete vyvíjet vlastní outputters.

### <a name="file-paths"></a>Cesty k souborům

EXTRAKCE a výstup příkazy pomocí cesty k souborům. Cesty k souboru může být absolutní nebo relativní:

Tato následující absolutní cestu k souboru odkazuje na soubor v Data Lake Store s názvem `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Následující cesta k souboru začíná `"/"`. Odkazuje na soubor v výchozího účtu Data Lake Store:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Použijte skalární proměnné

Skalární proměnné můžete použít také v zájmu snazší údržby vašeho skriptu. Předchozí skript U-SQL můžete zapsat také jako:

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

Použití **vyberte** k transformaci sady řádků:

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

Klauzule WHERE používá [C# logický výraz](https://msdn.microsoft.com/library/6a71f45d.aspx). Výraz jazyka C# můžete provést vlastní výrazy a funkce. Můžete provést i složitější filtrování je kombinací s logické spojky (operátoru and) a disjunctions (ORs).

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
 >Druhý dotaz pracuje na výsledek první sadu řádků, které vytvoří složené dva filtry. Můžete také znovu použít název proměnné a lexikálně oborem názvů.

## <a name="aggregate-rowsets"></a>Agregační sady řádků
U-SQL vám poskytuje známé ORDER BY, GROUP BY a agregace.

Následující dotaz najde celkovou dobu trvání každou oblast a potom zobrazí horní pět doby v pořadí.

Sady řádků U-SQL nezachováte jejich pořadí pro další dotaz. Proto pořadí výstup, musíte přidat ORDER BY výstup příkazu:

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

V klauzuli ORDER BY v U-SQL vyžaduje použití klauzuli FETCH ve výrazu SELECT.

V klauzuli NUTNOSTI U-SQL umožňuje omezit výstup do skupin, které splňují podmínku HAVING:

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

Agregace pokročilé scénáře, najdete v dokumentaci odkaz na U-SQL pro [agregovat, analytické a odkazovat na funkce](https://msdn.microsoft.com/library/azure/mt621335.aspx)

## <a name="next-steps"></a>Další postup
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
