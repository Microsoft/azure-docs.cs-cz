---
title: Rozšíření skriptů U-SQL pomocí R v Azure Data Lake Analytics
description: Zjistěte, jak spustit kód R ve skriptech U-SQL pomocí Azure Data Lake Analytics. Vloždom kód R vřadit nebo odkazovat ze souborů.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: c5dd3f493e85afc925b639c142a293eed1e8cbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672686"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Rozšíření skriptů U-SQL pomocí kódu R v Azure Data Lake Analytics

Následující příklad ilustruje základní kroky pro nasazení kódu R:
* Pomocí `REFERENCE ASSEMBLY` příkazu povolte rozšíření R pro skript U-SQL Script.
* Pomocí `REDUCE` operace rozdělte vstupní data na klíči.
* Rozšíření R pro U-SQL zahrnují vestavěný reduktor (`Extension.R.Reducer`), který spouští kód R na každém vrcholu přiřazeném reduktoru. 
* Použití vyhrazených pojmenovaných `inputFromUSQL` `outputToUSQL` datových rámců volaných a respektive předání dat mezi U-SQL a R. Jsou pevné názvy identifikátorů Vstupní a výstupní dataframe (to znamená, že uživatelé nemohou tyto předdefinované názvy vstupních a výstupních identifikátorů DataFrame měnit).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Vložení kódu R do skriptu U-SQL

Kód R můžete zadat pomocí příkazového parametru `Extension.R.Reducer`. Můžete například deklarovat skript R jako proměnnou řetězce a předat jej jako parametr reduktoru.


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

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Uchování kódu R v samostatném souboru a odkazování na skript U-SQL

Následující příklad ilustruje složitější použití. V tomto případě je kód R nasazen jako prostředek, který je skript U-SQL.

Uložte tento kód R jako samostatný soubor.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Pomocí skriptu U-SQL nasadit tento skript R s příkazem DEPLOY RESOURCE.

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

## <a name="how-r-integrates-with-u-sql"></a>Jak se R integruje s U-SQL

### <a name="datatypes"></a>Datové typy
* Řetězce a číselné sloupce z U-SQL jsou převedeny jako-je mezi `double`R `string` `bool`DataFrame a U-SQL [podporované typy: , , , `integer`]. `byte`
* Datový `Factor` typ není podporován v U-SQL.
* `byte[]`musí být serializován jako base64 `string`kódovaný .
* U-SQL řetězce lze převést na faktory v kódu R, jakmile U-SQL vytvořit R `stringsAsFactors: true`vstupní datový rámec nebo nastavením parametru reduktoru .

### <a name="schemas"></a>Schémata
* Datové sady U-SQL nemohou mít duplicitní názvy sloupců.
* Názvy sloupců datových sad U-SQL musí být řetězce.
* Názvy sloupců musí být stejné ve skriptech U-SQL a R.
* Sloupec jen pro čtení nemůže být součástí výstupního datového rámce. Vzhledem k tomu, že sloupce jen pro čtení jsou automaticky vloženy zpět do tabulky U SQL, pokud je součástí výstupního schématu UDO.

### <a name="functional-limitations"></a>Funkční omezení
* Modul R nelze vytvořit instanci dvakrát ve stejném procesu. 
* V současné době U-SQL nepodporuje Combiner UDO pro předpověď pomocí dělených modelů generovaných pomocí Redukční UDO. Uživatelé mohou deklarovat rozdělené modely jako prostředek a `ExtR_PredictUsingLMRawStringReducer.usql`používat je ve skriptu R (viz ukázkový kód)

### <a name="r-versions"></a>Verze R
Podporován je pouze R 3.2.2.

### <a name="standard-r-modules"></a>Standardní R moduly

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

### <a name="input-and-output-size-limitations"></a>Omezení velikosti vstupů a výstupů
Ke každému vrcholu je přiřazeno omezené množství paměti. Vzhledem k tomu, že vstupní a výstupní datové rámce musí existovat v paměti v kódu R, celková velikost vstupu a výstupu nesmí překročit 500 MB.

### <a name="sample-code"></a>Příklad kódu
Další ukázkový kód je k dispozici ve vašem účtu Data Lake Store po instalaci rozšíření U-SQL Advanced Analytics. Cesta pro další ukázkový `<your_account_address>/usqlext/samples/R`kód je: . 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Nasazení vlastních modulů R s U-SQL

Nejprve vytvořte vlastní modul R a zazipujte jej a poté nahrajte vlastní soubor modulu R z zipu do úložiště ADL. V tomto příkladu nahrajeme magittr_1.5.zip do kořenového adresáře výchozího účtu ADLS pro účet ADLA, který používáme. Jakmile nahrajete modul do úložiště ADL, deklarujte jej jako použití nasazení `install.packages` prostředku, aby byl k dispozici ve skriptu U-SQL a volání k jeho instalaci.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";

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
* [Použití funkcí okna U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
