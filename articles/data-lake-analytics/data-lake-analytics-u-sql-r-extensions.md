---
title: Rozšíření skriptů U-SQL s jazykem R ve službě Azure Data Lake Analytics
description: Zjistěte, jak spustit kód jazyka R v skriptů U-SQL pomocí Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: b7e6c4911081d3b83ed99ab7316cb6fd810a0d60
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048721"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Rozšíření skriptů U-SQL s kódem R ve službě Azure Data Lake Analytics

Následující příklad ukazuje základní kroky pro nasazování kódu R:
* Použití `REFERENCE ASSEMBLY` příkaz umožňující rozšíření R skript U-SQL.
* Použití` REDUCE` operace rozdělit vstupní data na klíč.
* Rozšíření U-SQL R zahrnují integrované redukční funkci (`Extension.R.Reducer`), který spouští kód R na každý vrchol přiřazené redukční funkci. 
* Využití vyhrazených s názvem datových rámců volá `inputFromUSQL` a `outputToUSQL `k předávání dat mezi U-SQL a R. vstupní a výstupní datový rámec názvy identifikátorů jsou pevné (to znamená, uživatelů není možné změnit názvy těchto předdefinovaných vstupu a výstupu datového rámce identifikátory).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Vložení kódu R do skriptu U-SQL

Můžete vložené R kód skriptu U-SQL s použitím parametru příkazu `Extension.R.Reducer`. Například můžete deklarovat skript jazyka R jako proměnnou s řetězcem a předáváme jako parametr do redukční funkci.


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Zachovat v samostatném souboru kódu jazyka R a na něj odkazovat skript U-SQL

Následující příklad ukazuje mnohem složitější využití. V tomto případě kód R se nasadí jako prostředek, který je skript U-SQL.

Uložte tento kód R jako samostatný soubor.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Použijte skript U-SQL k nasazení tohoto skriptu jazyka R s příkazem nasazení prostředků.

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>Jak R integruje do U-SQL

### <a name="datatypes"></a>Datové typy
* Jako jsou převést řetězcové a číselné sloupce v U-SQL-mezi R DataFrame a jazykem U-SQL [podporované typy: `double`, `string`, `bool`, `integer`, `byte`].
* `Factor` Není podporován v U-SQL.
* `byte[]` musí být serializován jako kódování base64 `string`.
* U-SQL řetězce lze převést na faktorech v kódu jazyka R, jakmile U-SQL vytvořit vstupní datový rámec R nebo tak, že nastavíte parametr redukční funkci `stringsAsFactors: true`.

### <a name="schemas"></a>Schémata
* U-SQL datové sady nemůže mít duplicitní názvy sloupců.
* Názvy sloupců datových sad U-SQL musí být řetězce.
* Názvy sloupců musí být stejné v U-SQL a skripty jazyka R.
* Sloupec jen pro čtení nemůže být součástí výstupu datového rámce. Protože je jen pro čtení sloupců se automaticky vloží zpět v tabulce U-SQL, pokud je součástí výstupní schéma UDO.

### <a name="functional-limitations"></a>Funkční omezení
* Nelze vytvořit instanci R stroje dvakrát v rámci stejného procesu. 
* U-SQL v současné době nepodporuje kombinační UDO pro předpověď na základě dělené modely vygenerované pomocí UDO redukční funkci. Uživatelé mohou deklarovat dělené modely jako zdroje a jejich použití v jejich skript jazyka R (ukázkový kód `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>Verze jazyka R:
Je podporován pouze R 3.2.2.

### <a name="standard-r-modules"></a>Standardní modulů R

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>Vstup a výstup omezení velikosti
Každý vrchol má omezené množství paměti přidělené k němu. Protože vstupních a výstupních datových rámců musí existovat v paměti v kódu jazyka R, celková velikost pro vstup a výstup může mít maximálně 500 MB.

### <a name="sample-code"></a>Vzorový kód
Další ukázkový kód je k dispozici v účtu Data Lake Store, po instalaci rozšíření U-SQL Advanced Analytics. Cesta pro další ukázkový kód je: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Nasazení vlastní R moduly pomocí U-SQL

Nejdřív vytvořit vlastní modul R a zip ji a pak nahrajte soubor ZIP vlastní modul R s vaším úložištěm ADL. V tomto příkladu nahrajeme magittr_1.5.zip do kořenového účtu ADLS výchozí účet ADLA, který se používá. Jakmile nahrajete do úložiště ADL modulu, deklarujte ho jako ji dejte k dispozici ve skriptu U-SQL a volání pomocí prostředků nasazení `install.packages` k její instalaci.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomePackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>Další kroky
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Pomocí funkcí okna U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
