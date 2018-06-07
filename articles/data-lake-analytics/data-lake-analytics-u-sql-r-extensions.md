---
title: Rozšíření skriptů U-SQL pomocí R v Azure Data Lake Analytics
description: Informace o spouštění kódu jazyka R v skriptů U-SQL pomocí Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 8b22b4238b20f56727d1c7858094328ab8817dad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624920"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Rozšíření skriptů U-SQL pomocí R kódu v Azure Data Lake Analytics

Následující příklad ukazuje základní kroky pro nasazování kódu jazyka R:
* Použití `REFERENCE ASSEMBLY` příkaz umožňující rozšíření R skript U-SQL.
* Použití` REDUCE` operace vstupní data pro klíč oddílu.
* Rozšíření R U-SQL zahrnují předdefinované reduktorem (`Extension.R.Reducer`) spouští kódu jazyka R se v jednotlivých vrchol přiřazené reduktorem. 
* Použití vyhrazených s názvem datových rámců názvem `inputFromUSQL` a `outputToUSQL `v uvedeném pořadí k předávání dat mezi U-SQL a R. vstup a výstup DataFrame opravě názvy identifikátorů (to znamená, uživatelé není možné změnit názvy těchto předdefinovaných vstupu a výstupu DataFrame identifikátory).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Vložení kódu jazyka R ve skriptu U-SQL

Můžete vložený kód R skript U-SQL pomocí parametru příkazového `Extension.R.Reducer`. Můžete například deklarovat R skript jako řetězec proměnné a předejte ji jako parametr reduktorem.


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

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Zachovat kód R v samostatném souboru a odkazovat na skript U-SQL

Následující příklad ukazuje použití složitější. V tomto případě kód R je nasazený jako prostředek, který je skript U-SQL.

Uložte tento kód R jako samostatný soubor.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

K nasazení tohoto skriptu jazyka R s příkazem nasazení prostředků pomocí skriptu U-SQL.

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

## <a name="how-r-integrates-with-u-sql"></a>Jak R integruje s jazykem U-SQL

### <a name="datatypes"></a>Datové typy
* Řetězec a číselné sloupce z U-SQL se převedou jako-mezi R DataFrame a jazykem U-SQL [podporované typy: `double`, `string`, `bool`, `integer`, `byte`].
* `Factor` Datový typ není podporován v U-SQL.
* `byte[]` musí být serializován jako kódováním base64 `string`.
* Řetězce U-SQL můžete převést na faktory v kódu jazyka R, jakmile U-SQL vytvořit vstupní dataframe R nebo nastavením parametru reduktorem `stringsAsFactors: true`.

### <a name="schemas"></a>Schémata
* Datové sady U-SQL nemůže mít duplicitní názvy sloupců.
* Názvy sloupců datových sad U-SQL musí být řetězce.
* Názvy sloupců musí být stejná U-SQL a skripty R.
* Sloupce jen pro čtení nemůže být součástí dataframe výstup. Protože sloupce jen pro čtení jsou automaticky vložit zpět v tabulce U-SQL, pokud je součástí výstupního schématu UDO.

### <a name="functional-limitations"></a>Funkční omezení
* Modul R nelze vytvořit instanci dvakrát v rámci jednoho procesu. 
* U-SQL v současné době nepodporuje kombinační UDO pro použití oddílů modelů generována pomocí reduktorem UDO předpovědi. Uživatelé můžou deklarovat oddílů modely jako prostředek a používat je ve své skriptu R (viz ukázkový kód `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>Verze R
Je podporován pouze R 3.2.2.

### <a name="standard-r-modules"></a>Standardní moduly R

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
Všechny vrcholy má omezené množství paměti přidělené k němu. Vzhledem k tomu, že vstupní a výstupní DataFrames musí existovat v paměti v kódu jazyka R, celková velikost vstupní a výstupní nemůže překročit 500 MB.

### <a name="sample-code"></a>Vzorový kód
Další ukázkový kód je k dispozici v účtu Data Lake Store, po instalaci rozšíření Advanced Analytics U-SQL. Cesta pro další ukázkový kód je: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Nasazení vlastní R modulů s jazykem U-SQL

Nejprve vytvořit vlastní modul R a zip ho a pak nahrajte soubor ZIP vlastní modul R ADL úložiště. V příkladu jsme odešlete magittr_1.5.zip do kořenového adresáře výchozího účtu ADLS ADLA účtu, který používáme. Jakmile nahrajete do úložiště ADL modul, deklarujte ji jako použijte nasazení prostředků a zpřístupněte ji ve skriptu U-SQL a volání `install.packages` k její instalaci.

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
* [Pro úlohy Azure Data Lake Analytics pomocí U-SQL okno funkce](data-lake-analytics-use-window-functions.md)
