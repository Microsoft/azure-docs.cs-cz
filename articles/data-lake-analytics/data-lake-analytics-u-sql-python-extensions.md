---
title: Rozšíření skriptů U-SQL s Pythonem v Azure Data Lake Analytics
description: Postup spuštění kódu jazyka Python v skriptů U-SQL pomocí Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 3e895a6ea9bccc0d210f43748edb3eea80ddc6ad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625076"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Rozšíření skriptů U-SQL s kódem jazyka Python v Azure Data Lake Analytics

## <a name="prerequisites"></a>Požadavky

Než začnete, zkontrolujte, jestli že rozšíření Python jsou nainstalované ve vašem účtu Azure Data Lake Analytics.

* Přejděte do jste účet Data Lake Analytics na portálu Azure
* V levé nabídce v části **ZAČÍNÁME** klikněte na **ukázkové skripty**
* Klikněte na tlačítko **instalovat rozšíření U-SQL** pak **OK**

## <a name="overview"></a>Přehled 

Rozšíření Python pro U-SQL umožňují vývojářům provádět masivně paralelní provádění kódu jazyka Python. Následující příklad ukazuje základní kroky:

* Použití `REFERENCE ASSEMBLY` příkaz povolení rozšíření Python pro skript U-SQL
* Pomocí `REDUCE` operace vstupní data pro klíč oddílu
* Rozšíření Python pro U-SQL zahrnují předdefinované reduktorem (`Extension.Python.Reducer`) spouští se v jednotlivých vrchol přiřazené reduktorem kód Python
* Skript U-SQL obsahuje vestavěný kód Python, který má funkci s názvem `usqlml_main` který přijímá pandas DataFrame jako vstup a vrátí pandas DataFrame jako výstup.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Jak Python integruje s jazykem U-SQL

### <a name="datatypes"></a>Datové typy

* Řetězec a číselné sloupce z U-SQL se převedou jako-mezi Pandas a jazykem U-SQL
* Hodnoty Null U-SQL se převedou do a z Pandas `NA` hodnoty

### <a name="schemas"></a>Schémata

* Index vektorů v Pandas nejsou podporovány v U-SQL. Všechny snímky vstupní data ve funkci Python mít vždy 64-bit číselné index od 0 do počet řádků minus 1. 
* Datové sady U-SQL nemůže mít duplicitní názvy sloupců
* Názvy sloupců datových sad U-SQL, které nejsou typu řetězec. 

### <a name="python-versions"></a>Verze jazyka Python
Je podporován pouze Python 3.5.1 (zkompilovaném pro Windows). 

### <a name="standard-python-modules"></a>Standardní moduly jazyka Python
Všechny standardní moduly Python jsou zahrnuty.

### <a name="additional-python-modules"></a>Další moduly jazyka Python
Kromě standardní knihovny jazyka Python mají několik běžně používané python knihovny:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Zprávy o výjimkách
V současné době výjimku v kódu jazyka Python objeví jako obecný vrchol selhání. Chybové zprávy, které úlohy U-SQL v budoucnu, se zobrazí zpráva o výjimce Python.

### <a name="input-and-output-size-limitations"></a>Vstup a výstup omezení velikosti
Všechny vrcholy má omezené množství paměti přidělené k němu. V současné době je tento limit pro AU 6 GB. Vzhledem k tomu, že vstupní a výstupní DataFrames musí existovat v paměti v kódu jazyka Python, celková velikost vstupní a výstupní nesmí překročit 6 GB.

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Pro úlohy Azure Data Lake Analytics pomocí U-SQL okno funkce](data-lake-analytics-use-window-functions.md)
* [Pomocí nástroje Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
