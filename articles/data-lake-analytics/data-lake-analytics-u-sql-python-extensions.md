---
title: Rozšíření skriptů U-SQL s Pythonem v Azure Data Lake Analytics
description: Přečtěte si, jak spustit kód Pythonu ve skriptech U-SQL pomocí Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813400"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Rozšíření skriptů U-SQL pomocí kódu Pythonu v Azure Data Lake Analytics

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozšíření Pythonu jsou nainstalovaná ve vašem účtu Azure Data Lake Analytics.

* Přejděte k účtu Data Lake Analytics na webu Azure Portal
* V levém menu klikněte v části **ZAČÍNÁME** na **Ukázkové skripty**
* Klikněte na **Nainstalovat rozšíření U-SQL a** pak **OK**

## <a name="overview"></a>Přehled 

Rozšíření Pythonu pro U-SQL umožňují vývojářům provádět masivně paralelní spuštění kódu Pythonu. Následující příklad ilustruje základní kroky:

* Použití `REFERENCE ASSEMBLY` příkazu k povolení rozšíření Pythonu pro U-SQL Script
* Použití `REDUCE` operace k rozdělení vstupních dat na klíč
* Rozšíření Pythonu pro U-SQL obsahují vestavěný reduktor (`Extension.Python.Reducer`), který spouští kód Pythonu na každém vrcholu přiřazeném reduktoru
* Skript U-SQL obsahuje vložený kód Pythonu, `usqlml_main` který má funkci nazvanou, která přijímá pandas DataFrame jako vstup a vrací pandas DataFrame jako výstup.

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

## <a name="how-python-integrates-with-u-sql"></a>Jak se Python integruje s U-SQL

### <a name="datatypes"></a>Datové typy

* Řetězce a číselné sloupce z U-SQL jsou převedeny jako -je mezi Pandas a U-SQL
* U-SQL Nulls jsou převedeny na `NA` a z Pandy hodnoty

### <a name="schemas"></a>Schémata

* Index vektory v Pandy nejsou podporovány v U-SQL. Všechny rámce vstupních dat ve funkci Pythonu mají vždy 64bitový číselný index od 0 do počtu řádků mínus 1. 
* Datové sady U-SQL nemohou obsahovat duplicitní názvy sloupců.
* Názvy sloupců datových sad U-SQL, které nejsou řetězci. 

### <a name="python-versions"></a>Verze Pythonu
Podporován je pouze Python 3.5.1 (kompilovaný pro Windows). 

### <a name="standard-python-modules"></a>Standardní moduly Pythonu
Součástí balení jsou všechny standardní moduly Pythonu.

### <a name="additional-python-modules"></a>Další moduly Pythonu
Kromě standardních knihoven Pythonu je zahrnuto několik běžně používaných knihoven pythonu:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Zprávy o výjimky
V současné době se výjimka v kódu Pythonu zobrazí jako obecné selhání vrcholu. V budoucnu se chybové zprávy U-SQL Job zobrazí zpráva o výjimce Pythonu.

### <a name="input-and-output-size-limitations"></a>Omezení velikosti vstupů a výstupů
Ke každému vrcholu je přiřazeno omezené množství paměti. V současné době je tento limit 6 GB pro AU. Vzhledem k tomu, že vstupní a výstupní datové rámce musí existovat v paměti v kódu Pythonu, celková velikost vstupu a výstupu nesmí překročit 6 GB.

## <a name="see-also"></a>Viz také
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Použití funkcí okna U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
