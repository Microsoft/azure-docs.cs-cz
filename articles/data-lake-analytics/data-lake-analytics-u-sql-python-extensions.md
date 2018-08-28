---
title: Rozšíření skriptů U-SQL pomocí Pythonu v Azure Data Lake Analytics
description: Zjistěte, jak spustit kód v Pythonu v skriptů U-SQL pomocí Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: b2179f9d81a2dad877c8ae58471f7440eb9edbe7
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43050987"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Rozšíření skriptů U-SQL pomocí kódu v Pythonu v Azure Data Lake Analytics

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozšíření Python jsou nainstalovány ve vašem účtu Azure Data Lake Analytics.

* Na webu Azure Portal přejděte do účtu Data Lake Analytics
* V nabídce vlevo v části **ZAČÍNÁME** klikněte na **ukázkové skripty**
* Klikněte na tlačítko **nainstalovat rozšíření U-SQL** pak **OK**

## <a name="overview"></a>Přehled 

Rozšíření Pythonu pro U-SQL umožňují vývojářům provádět masivně paralelní provádění kódu v Pythonu. Následující příklad ukazuje základní kroky:

* Použití `REFERENCE ASSEMBLY` příkaz povolení rozšíření Python pro skript U-SQL
* Použití `REDUCE` operace rozdělit vstupní data na klíč
* Rozšíření Pythonu pro U-SQL zahrnují integrované redukční funkci (`Extension.Python.Reducer`), která spustí kód v Pythonu na každý vrchol přiřazené redukční funkci
* Skript U-SQL obsahuje vložený kód Pythonu, který má funkci s názvem `usqlml_main` , který přijímá pandas DataFrame jako vstup a vrátí pandas DataFrame jako výstup.

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

## <a name="how-python-integrates-with-u-sql"></a>Jak se integruje Python pomocí U-SQL

### <a name="datatypes"></a>Datové typy

* Jako jsou převést řetězcové a číselné sloupce v U-SQL-mezi Pandas a jazykem U-SQL
* Hodnoty Null U-SQL se převedou do a z Pandas `NA` hodnoty

### <a name="schemas"></a>Schémata

* Index vektory v Pandas nejsou podporovány v U-SQL. Všechny snímky vstupní data v Pythonu – funkce mají vždy číselný index od 0 do počtu řádků minus 1 64-bit. 
* U-SQL datové sady nemůže mít duplicitní názvy sloupců
* Názvy sloupců datových sad U-SQL, které nejsou řetězce. 

### <a name="python-versions"></a>Verze Pythonu
Je podporován pouze Python 3.5.1 (zkompilován pro Windows). 

### <a name="standard-python-modules"></a>Standardní moduly Pythonu
Jsou zahrnuty všechny standardní moduly Pythonu.

### <a name="additional-python-modules"></a>Další moduly Pythonu
Kromě standardní knihovny jazyka Python jsou zahrnuty několik běžně používaných python knihoven:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Zprávy o výjimkách
V současné době se výjimka v kódu Pythonu zobrazí jako obecný vrcholu selhání. Chybové zprávy úloh U-SQL v budoucnu, se zobrazí zpráva o výjimce Python.

### <a name="input-and-output-size-limitations"></a>Vstup a výstup omezení velikosti
Každý vrchol má omezené množství paměti přidělené k němu. Toto omezení je v současné době 6 GB pro jednotkách analýzy. Protože vstupních a výstupních datových rámců musí existovat v paměti v kódu Pythonu, celková velikost pro vstup a výstup nesmí přesáhnout 6 GB.

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Pomocí funkcí okna U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
* [Pomocí nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
