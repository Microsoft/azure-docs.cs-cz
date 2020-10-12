---
title: Rozšíří skripty U-SQL pomocí R v Azure Data Lake Analytics
description: Naučte se spouštět kód R ve skriptech U-SQL pomocí Azure Data Lake Analytics. Vložení kódu jazyka R nebo odkazů ze souborů
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.openlocfilehash: 1c054dcfe12ad1d2098fc5352183839704e39330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87127396"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Rozšíří se skripty U-SQL s kódem R v Azure Data Lake Analytics

Následující příklad znázorňuje základní kroky pro nasazení kódu R:

* Pomocí `REFERENCE ASSEMBLY` příkazu povolte pro skript U-SQL rozšíření R.
* Pomocí `REDUCE` operace můžete rozdělit vstupní data na klíč.
* Rozšíření R pro U-SQL obsahují vestavěný snižovalo ( `Extension.R.Reducer` ), které spouští R kód na každém vrcholu přiřazeném k tomuto nástroji.
* Použití vyhrazených pojmenovaných datových rámců `inputFromUSQL` a `outputToUSQL` k předávání dat mezi názvy U-SQL a R. vstupní a výstupní identifikátory identifikátorů dataframe jsou pevné (to znamená, že uživatelé nemůžou měnit tyto předdefinované názvy vstupních a výstupních identifikátorů datových rámců).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Vložení kódu R do skriptu U-SQL

Kód R můžete vložit do skriptu U-SQL pomocí parametru příkazu `Extension.R.Reducer` . Například můžete deklarovat skript jazyka R jako proměnnou řetězce a předat ji jako parametr pro redukci.

```usql
REFERENCE ASSEMBLY [ExtR];

DECLARE @myRScript = @"
inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
#do not return readonly columns and make sure that the column names are the same in usql and r cripts,
outputToUSQL=data.frame(summary(lm.fit)$coefficients)
colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
outputToUSQL
";

@RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, ReturnType:"dataframe");
```

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Ponechte kód R v samostatném souboru a odkazujte ho na skript U-SQL.

Následující příklad znázorňuje komplexnější využití. V tomto případě je kód R nasazený jako prostředek, který je skript U-SQL.

Uložte tento kód R jako samostatný soubor.

```usql
load("my_model_LM_Iris.rda")
outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
```

Pomocí skriptu U-SQL nasaďte tento skript R pomocí příkazu nasadit prostředek.

```usql
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
```

## <a name="how-r-integrates-with-u-sql"></a>Jak se R integruje s U-SQL

### <a name="datatypes"></a>Datové typy

* Řetězcové a číselné sloupce z jazyka U-SQL jsou převáděny tak, jak jsou mezi R dataframe a U-SQL [podporované typy: `double` , `string` , `bool` , `integer` , `byte` ].
* `Factor`Datový typ není podporován v U-SQL.
* `byte[]` musí být serializován jako kódovaný v kódování Base64 `string` .
* Řetězce u-SQL je možné převést na faktory v kódu R. po vytvoření vstupního datového rámce U-SQL vytvořte vstupní datový rámec R nebo nastavením parametru pro snížení `stringsAsFactors: true` .

### <a name="schemas"></a>Schémata

* Datové sady U-SQL nemůžou mít duplicitní názvy sloupců.
* Názvy sloupců U-SQL DataSets musí být řetězce.
* Názvy sloupců musí být ve skriptech U-SQL a R stejné.
* Sloupec jen pro čtení nemůže být součástí výstupního datového rámce. Vzhledem k tomu, že sloupce ReadOnly jsou automaticky vloženy zpět do tabulky U-SQL, pokud je součástí výstupního schématu UDO.

### <a name="functional-limitations"></a>Funkční omezení

* Ve stejném procesu nejde vytvořit instanci modulu R dvakrát.
* V současné době U-SQL nepodporuje kombinaci Udo pro předpověď pomocí dělených modelů generovaných pomocí nástroje pro redukci Udo. Uživatelé mohou deklarovat rozdělené modely jako prostředek a použít je ve skriptu jazyka R (viz ukázkový kód `ExtR_PredictUsingLMRawStringReducer.usql` ).

### <a name="r-versions"></a>Verze R

Podporuje se jenom R 3.2.2.

### <a name="standard-r-modules"></a>Standardní moduly R

```usql
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
```

### <a name="input-and-output-size-limitations"></a>Omezení velikosti vstupu a výstupu

Ke každému vrcholu je přiřazena omezená velikost paměti. Vzhledem k tomu, že vstupní a výstupní datový rámec musí existovat v paměti v kódu R, celková velikost vstupu a výstupu nemůže být větší než 500 MB.

### <a name="sample-code"></a>Ukázka kódu

Další vzorový kód je dostupný v účtu Data Lake Store po instalaci rozšíření U-SQL Advanced Analytics. Cesta pro další vzorový kód je: `<your_account_address>/usqlext/samples/R` .

## <a name="deploying-custom-r-modules-with-u-sql"></a>Nasazení vlastních modulů R pomocí U-SQL

Nejdřív vytvořte vlastní modul R a nahrajte ho a potom do úložiště ADL nahrajte soubor vlastního modulu zip. V tomto příkladu odešleme magittr_1.5.zip do kořenového adresáře výchozího účtu ADLS pro účet ADLA, který používáme. Po nahrání modulu do úložiště ADL ho deklarujete jako prostředek použít nasazení, abyste ho mohli zpřístupnit ve skriptu U-SQL a zavolat ho `install.packages` k instalaci.

```usql
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
```

## <a name="next-steps"></a>Další kroky

* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Použití funkcí okna U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
