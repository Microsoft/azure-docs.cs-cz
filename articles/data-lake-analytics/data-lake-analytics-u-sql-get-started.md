---
title: Začínáme s jazykem U-SQL v Azure Data Lake Analytics
description: Seznamte se se základy jazyka U-SQL v Azure Data Lake Analytics. Napište svůj první dotaz pomocí proměnných pro další data ze souborů, transformujte sadu řádků a agregovaná data.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/23/2017
ms.openlocfilehash: 5920f2b5823e568d56d13107200eb0025a654db0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219917"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Začínáme s U-SQL v Azure Data Lake Analytics

U-SQL je jazyk, který kombinuje deklarativní SQL s imperativně v jazyce C#, který umožňuje zpracovávat data v jakémkoli měřítku. Díky škálovatelné funkci distribuovaného dotazu U-SQL můžete efektivně analyzovat data napříč relačními úložišti, jako je Azure SQL Database. Pomocí jazyka U-SQL můžete zpracovávat nestrukturovaná data, a to použitím schématu při čtení a vkládání vlastní logiky a UDF. U-SQL navíc nabízí rozšiřitelnost, která vám poskytne přesnější kontrolu nad tím, jak se dá provádět ve velkém měřítku.

## <a name="learning-resources"></a>Studijní materiály

* [Kurz u-SQL](https://aka.ms/usqltutorial) poskytuje podrobný návod pro většinu jazyka u-SQL. Tento dokument se doporučuje přečíst u všech vývojářů, kteří se chtějí dozvědět U-SQL.
* Podrobné informace o **syntaxi jazyka u-SQL** najdete v tématu Referenční dokumentace [jazyka u-SQL](/u-sql/).
* Informace o **filozofie návrhu u-SQL** najdete v příspěvku na blogu sady Visual Studio [Představujeme u-SQL – jazyk, který usnadňuje zpracování velkých](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)objemů dat.

## <a name="prerequisites"></a>Předpoklady

Než projdete ukázky U-SQL v tomto dokumentu, přečtěte si a dokončete [kurz: vývoj skriptů u-SQL pomocí data Lakech nástrojů pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Tento kurz vysvětluje mechanismy použití U-SQL s Nástroje Azure Data Lake pro Visual Studio.

## <a name="your-first-u-sql-script"></a>Váš první skript U-SQL

Následující skript U-SQL je jednoduchý a umožňuje nám prozkoumat mnoho aspektů jazyka U-SQL.

```usql
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

Tento skript nemá žádné kroky transformace. Načte ze zdrojového souboru `SearchLog.tsv` s názvem, schematizes ho a zapíše sadu řádků zpátky do souboru s názvem SearchLog-first-u-sql.csv.

Všimněte si otazníku vedle datového typu v `Duration` poli. To znamená, že `Duration` pole může mít hodnotu null.

### <a name="key-concepts"></a>Klíčové koncepty

* **Proměnné sady řádků**: každý výraz dotazu, který vytváří sadu řádků, lze přiřadit proměnné. U-SQL následuje vzor pojmenování proměnných T-SQL ( `@searchlog` například) ve skriptu.
* Klíčové slovo **Extract** čte data ze souboru a definuje schéma při čtení. `Extractors.Tsv` je vestavěný extraktor U-SQL pro soubory s hodnotami oddělenými tabulátorem. Můžete vyvíjet vlastní extraktory.
* **Výstup** zapisuje data ze sady řádků do souboru. `Outputters.Csv()` je vestavěný výstup U-SQL, který umožňuje vytvořit soubor s hodnotami oddělenými čárkami. Můžete vyvíjet vlastní výstupy.

### <a name="file-paths"></a>Cesty k souborům

Příkazy EXTRACT a OUTPUT používají cesty k souborům. Cesty k souborům můžou být absolutní nebo relativní:

Následující absolutní cesta k souboru odkazuje na soubor v Data Lake Store s názvem `mystore` :

```usql
adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv
```

Následující cesta k souboru začíná na `"/"` . Odkazuje na soubor ve výchozím účtu Data Lake Store:

```usql
/output/SearchLog-first-u-sql.csv
```

## <a name="use-scalar-variables"></a>Použití skalárních proměnných

Pomocí skalárních proměnných také můžete usnadnit údržbu skriptu. Předchozí skript U-SQL může být také zapsán jako:

```usql
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
```

## <a name="transform-rowsets"></a>Transformovat sady řádků

Použijte **možnost vybrat** k transformaci sad řádků:

```usql
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
```

Klauzule WHERE používá [logický výraz jazyka C#](/dotnet/csharp/language-reference/operators/index). K vlastním výrazům a funkcím můžete použít jazyk C# Expression. Složitější filtrování můžete dokonce spojit s logickými spoji (and) a disjunkci (ORs).

Následující skript používá metodu DateTime. Parse () a kombinaci.

```usql
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
```

 >[!NOTE]
 >Druhý dotaz pracuje na výsledku první sady řádků, která vytvoří složený ze dvou filtrů. Můžete také znovu použít název proměnné a názvy mají rozsah lexikální.

## <a name="aggregate-rowsets"></a>Agregovat sady řádků

U-SQL získáte známé pořadí podle, seskupení a agregace.

Následující dotaz najde celkovou dobu trvání na oblast a pak zobrazí pět hlavních dob trvání v daném pořadí.

Sady řádků U-SQL neuchovávají pořadí pro další dotaz. Proto je nutné do příkazu OUTPUT Přidat ORDER by, aby bylo možné seřadit výstup:

```usql
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
```

Klauzule ORDER BY v jazyce U-SQL vyžaduje použití klauzule FETCH ve výrazu SELECT.

Pomocí klauzule U-SQL je možné omezit výstup do skupin, které odpovídají podmínkám splnění:

```usql
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
```

V případě pokročilých agregačních scénářů si přečtěte referenční dokumentaci U-SQL pro [agregační, analytické a referenční funkce](/u-sql/built-in-functions) .

## <a name="next-steps"></a>Další kroky

* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)