---
title: Začínáme s jazykem U-SQL v Azure Data Lake Analytics
description: Naučte se základy jazyka U-SQL v Azure Data Lake Analytics. Zapište první dotaz pomocí proměnných na další data ze souborů, transformujte sadu řádků a agregujte data.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 8130679dcc519cecd25abf43902c003ad8047df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672824"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Začínáme s U-SQL v Azure Data Lake Analytics
U-SQL je jazyk, který kombinuje deklarativní SQL s imperativní C# a umožňuje zpracování dat v libovolném měřítku. Prostřednictvím škálovatelné možnosti distribuovaných dotazů u-SQL můžete efektivně analyzovat data napříč relačními obchody, jako je Azure SQL Database. S U-SQL můžete zpracovat nestrukturovaná data použitím schématu při čtení a vkládání vlastní logiky a UDFs. Navíc U-SQL obsahuje rozšiřitelnost, která poskytuje jemně odstupňovanou kontrolu nad tím, jak provést ve velkém měřítku. 

## <a name="learning-resources"></a>Výukové materiály

* [Kurz U-SQL](https://aka.ms/usqltutorial) poskytuje návod s průvodcem většiny jazyka U-SQL. Tento dokument je doporučeno čtení pro všechny vývojáře, kteří se chtějí naučit U-SQL.
* Podrobné informace o **syntaxi jazyka U-SQL**naleznete v [referenční příručce jazyka U-SQL](https://docs.microsoft.com/u-sql/).
* Informace o **filozofii návrhu U-SQL**najdete v příspěvku blogu Visual Studio [Introducing U-SQL – jazyk, který usnadňuje zpracování velkých objemů dat](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Požadavky

Než si projdete ukázky U-SQL v tomto dokumentu, přečtěte si a [dokončete kurz: Vývoj skriptů U-SQL pomocí nástrojů Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Tento kurz vysvětluje mechaniku používání U-SQL s Nástroji Azure Data Lake Tools pro Visual Studio.

## <a name="your-first-u-sql-script"></a>Váš první skript U-SQL

Následující U-SQL skript je jednoduchý a umožňuje nám prozkoumat mnoho aspektů u-SQL jazyka.

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

Tento skript nemá žádné kroky transformace. Čte ze zdrojového `SearchLog.tsv`souboru s názvem , schematizes to, a zapíše sadu řádků zpět do souboru s názvem SearchLog-first-u-sql.csv.

Všimněte si otazníku vedle `Duration` datového typu v poli. To znamená, `Duration` že pole může být null.

### <a name="key-concepts"></a>Klíčové koncepty
* **Proměnné sady řádků**: Každý výraz dotazu, který vytváří sadu řádků, lze přiřadit proměnné. U-SQL následuje t-SQL proměnné pojmenování vzor (`@searchlog`, například) ve skriptu.
* Klíčové slovo **EXTRACT** čte data ze souboru a definuje schéma při čtení. `Extractors.Tsv`je vestavěný extraktor U-SQL pro soubory s oddělenou tabulátorem. Můžete vyvinout vlastní extraktory.
* **Výstup** zapisuje data ze sady řádků do souboru. `Outputters.Csv()`je vestavěný výstup U-SQL k vytvoření souboru s hodnotou oddělené čárkami. Můžete vyvíjet vlastní outputters.

### <a name="file-paths"></a>Cesty k souborům

Příkazy EXTRACT a OUTPUT používají cesty k souborům. Cesty k souborům mohou být absolutní nebo relativní:

Tato absolutní cesta k souboru odkazuje na soubor `mystore`v úložišti Data Lake S názvem :

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Tato následující cesta `"/"`k souboru začíná písmenem . Odkazuje na soubor ve výchozím účtu Data Lake Store:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Použití skalárních proměnných

Můžete také použít skalární proměnné, abyste usnadnili údržbu skriptu. Předchozí U-SQL skript lze také zapsat jako:

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

## <a name="transform-rowsets"></a>Transformace sad řádků

Pomocí **funkce SELECT** můžete transformovat sady řádků:

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

Klauzule WHERE používá [logický výraz Jazyka C#](/dotnet/csharp/language-reference/operators/index). Výraz jazyka C# můžete použít k provádění vlastních výrazů a funkcí. Můžete dokonce provádět složitější filtrování jejich kombinací s logickými spojkami (AND) a disjunkcemi (ORs).

Následující skript používá Metodu DateTime.Parse() a spojku.

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
 >Druhý dotaz pracuje na výsledku první sady řádků, která vytvoří složený ze dvou filtrů. Můžete také znovu použít název proměnné a názvy jsou vymezeny lexikálně.

## <a name="aggregate-rowsets"></a>Agregované sady řádků
U-SQL vám dává známé ORDER BY, GROUP BY a agregace.

Následující dotaz vyhledá celkovou dobu trvání na oblast a zobrazí prvních pět dob trvání v pořadí.

Sady řádků U-SQL nezachovávají své pořadí pro další dotaz. Chcete-li tedy objednat výstup, musíte do příkazu VÝSTUP přidat PŘÍKAZ:

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

U-SQL ORDER BY klauzule vyžaduje použití FETCH klauzule ve výrazu SELECT.

U-SQL having klauzule lze omezit výstup na skupiny, které splňují podmínky HAVING:

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

Pokročilé scénáře agregace naleznete v referenční dokumentaci U-SQL pro [agregační, analytické a referenční funkce](/u-sql/built-in-functions)

## <a name="next-steps"></a>Další kroky
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
