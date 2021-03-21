---
title: 'Kurz: použití R v výpočetním kontextu Sparku ve službě Azure HDInsight'
description: Kurz – Začínáme s R a Sparkem v clusteru Azure HDInsight Machine Learning Services.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: bd6015529fb521e3b157e46ee808aea43e993dee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935663"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Kurz: použití R v výpočetním kontextu Sparku ve službě Azure HDInsight

V tomto kurzu najdete podrobný Úvod k použití funkcí R v Apache Spark, které běží na clusteru Azure HDInsight Machine Learning Services.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Stažení ukázkových dat do místního úložiště
> * Kopírovat data do výchozího úložiště
> * Nastavení datové sady
> * Vytváření zdrojů dat
> * Vytvoření výpočetního kontextu pro Spark
> * Přizpůsobení lineárního modelu
> * Použití složených souborů XDF
> * Převést XDF na CSV

## <a name="prerequisites"></a>Předpoklady

* Cluster služby Azure HDInsight Machine Learning Services. V [části vytvořit Apache Hadoop clustery pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) a pro **typ clusteru** vyberte **služby ml**.

## <a name="connect-to-rstudio-server"></a>Připojení k RStudio Serveru

RStudio server běží na hraničním uzlu clusteru. V následujícím webu (kde *název_clusteru* v adrese URL je název clusteru HDInsight Machine Learning Services, který jste vytvořili):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Při prvním přihlášení se dvakrát ověří. Na výzvu k prvnímu ověření zadejte uživatelské jméno a heslo správce clusteru (výchozí nastavení je *admin*). Na druhé výzvě pro ověření zadejte uživatelské jméno a heslo SSH (výchozí hodnota je *sshuser*). Další přihlášení vyžadují jenom přihlašovací údaje SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Stažení ukázkových dat do místního úložiště

*Datová sada na leteckých 2012 v čase* se skládá ze 12 souborů oddělených čárkami, které obsahují podrobnosti o příjezdu a odchodu letu pro všechny komerční lety v USA po dobu roku 2012. Tato datová sada je velká, s více než 6 000 000 pozorováními.

1. Inicializujte několik proměnných prostředí. V konzole serveru RStudio zadejte následující kód:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. V pravém podokně vyberte kartu **prostředí** . Proměnné se zobrazí v části **hodnoty**.

    ![Webová konzola HDInsight R Studio](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Vytvořte místní adresář a Stáhněte si ukázková data. Do RStudio zadejte následující kód:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    Stahování se musí dokončit přibližně po 9,5 minutách.

## <a name="copy-the-data-to-default-storage"></a>Kopírovat data do výchozího úložiště

Umístění Hadoop systém souborů DFS (Distributed File System) (HDFS) je určeno `airDataDir` proměnnou. Do RStudio zadejte následující kód:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

Krok by měl být dokončen přibližně po dobu 10 sekund.

## <a name="set-up-a-dataset"></a>Nastavení datové sady

1. Vytvořte objekt systému souborů, který používá výchozí hodnoty. Do RStudio zadejte následující kód:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Vzhledem k tomu, že původní soubory CSV místo toho nepraktický názvy proměnných, zadáte seznam *colInfo* , aby byly lépe spravovatelné. Do RStudio zadejte následující kód:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Vytváření zdrojů dat

Ve výpočetním kontextu Spark můžete vytvořit zdroje dat pomocí následujících funkcí:

|Funkce | Description |
|---------|-------------|
|`RxTextData` | Textový zdroj dat oddělený čárkami. |
|`RxXdfData` | Data ve formátu datových souborů XDF Ve RevoScaleR je formát souboru XDF změněn pro Hadoop pro ukládání dat do složené sady souborů, nikoli do jediného souboru. |
|`RxHiveData` | Vygeneruje objekt zdroje dat podregistru.|
|`RxParquetData` | Generuje objekt zdroje dat Parquet.|
|`RxOrcData` | Generuje objekt zdroje dat ORC.|

Vytvořte objekt [RxTextData](/machine-learning-server/r-reference/revoscaler/rxtextdata) pomocí souborů, které jste zkopírovali do HDFS. Do RStudio zadejte následující kód:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Vytvoření výpočetního kontextu pro Spark

Chcete-li načíst data a spustit analýzy na pracovních uzlech, nastavte výpočetní kontext ve skriptu na [výpočetního rxspark](/machine-learning-server/r-reference/revoscaler/rxspark). V tomto kontextu funkce R automaticky distribuují úlohy napříč všemi pracovními uzly bez integrovaného požadavku pro správu úloh nebo frontu. Výpočetní kontext Spark je vytvořen prostřednictvím `RxSpark` nebo `rxSparkConnect()` k vytvoření výpočetního kontextu Spark a používá `rxSparkDisconnect()` se k návratu do místního výpočetního kontextu. Do RStudio zadejte následující kód:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Přizpůsobení lineárního modelu

1. Použijte funkci [rxLinMod](/machine-learning-server/r-reference/revoscaler/rxlinmod) pro přizpůsobení lineárního modelu pomocí `airDS` zdroje dat. Do RStudio zadejte následující kód:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Tento krok je potřeba dokončit 2 až 3 minuty.

1. Zkontrolujte výsledky. Do RStudio zadejte následující kód:

    ```R
    summary(delayArr)
    ```

    Měly by se zobrazit následující výsledky:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    Výsledky ukazují, že jste zpracovali všechna data, 6 000 000 pozorování, pomocí všech souborů CSV v zadaném adresáři. Vzhledem k tomu, že jste zadali `cube = TRUE` , máte odhadovaný koeficient pro každý den v týdnu (a ne průsečík).

## <a name="use-composite-xdf-files"></a>Použití složených souborů XDF

Jak jste viděli, můžete analyzovat soubory CSV přímo pomocí R v systému Hadoop. Analýzu můžete provádět rychleji, pokud data ukládáte do efektivnějšího formátu. Formát souboru R XDF je efektivní, ale u HDFS se pro HDFS změnil, aby jednotlivé soubory zůstaly v rámci jednoho bloku HDFS. (Velikost bloku HDFS se liší od instalace k instalaci, ale je to obvykle 64 MB nebo 128 MB.) 

Při použití [rxImport](/machine-learning-server/r-reference/revoscaler/rximport) v systému Hadoop k vytvoření sady složených souborů XDF zadáte zdroj dat, jako je `RxTextData` `AirDS` například inData a `RxXdfData` zdroj dat se systémem souborů v systému souborů HDFS jako argumentu pro soubory. Pak můžete `RxXdfData` objekt použít jako argument data v následných analýzách R.

1. Definujte `RxXdfData` objekt. Do RStudio zadejte následující kód:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Nastavte blokovou velikost 250000 řádků a určete, že se všechna data čtou. Do RStudio zadejte následující kód:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importujte data pomocí `rxImport` . Do RStudio zadejte následující kód:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Tento krok je potřeba provést během několika minut.

1. Opakovaný odhad stejného lineárního modelu pomocí nového a rychlejšího zdroje dat. Do RStudio zadejte následující kód:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Krok by měl být dokončen za méně než minutu.

1. Zkontrolujte výsledky. Výsledky by měly být stejné jako ze souborů CSV. Do RStudio zadejte následující kód:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Převést XDF na CSV

### <a name="in-a-spark-context"></a>V kontextu Sparku

Pokud jste soubory CSV převedli na formát souboru XDF, aby se při spouštění analýz zvětšila efektivita, ale teď chcete data převést zpátky na CSV, můžete to udělat pomocí [rxDataStep](/machine-learning-server/r-reference/revoscaler/rxdatastep).

Chcete-li vytvořit složku souborů CSV, nejprve vytvořte `RxTextData` objekt pomocí názvu adresáře jako argumentu souboru. Tento objekt představuje složku, ve které se mají vytvořit soubory CSV. Tento adresář se vytvoří při spuštění `rxDataStep` . Pak přejděte na tento `RxTextData` objekt v `outFile` argumentu `rxDataStep` . Každý vytvořený sdílený svazek clusteru má název založený na názvu adresáře a následuje číslo.

Předpokládejme, že chcete `airDataXdf` po provedení logistické regrese a předpovědi napsat do HDFS ze složené XDF složku souborů CSV. to znamená, že nové soubory CSV obsahují předpovězené hodnoty a zbytky. Do RStudio zadejte následující kód:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Tento krok by měl být dokončen přibližně po 2,5 minutách.

`rxDataStep`Vypsali jsme jeden soubor CSV pro každý soubor XDFD ve vstupním složeném souboru XDF. Toto je výchozí chování při psaní souborů CSV ze složených souborů XDF do HDFS, pokud je kontext výpočtu nastavený na `RxSpark` .

### <a name="in-a-local-context"></a>V místním kontextu

Případně, pokud jste dokončili své analýzy, můžete přepnout výpočetní kontext zpátky na, `local` abyste mohli využít výhod dvou argumentů v rámci `RxTextData` , což vám poskytne poněkud větší kontrolu při zápisu souborů CSV do HDFS: `createFileSet` a `rowsPerOutFile` . Když nastavíte `createFileSet` na `TRUE` , do zadaného adresáře se zapíše složka souborů CSV. Při nastavení `createFileSet` na `FALSE` se zapíše jeden soubor CSV. Můžete nastavit druhý argument, `rowsPerOutFile` na celé číslo, chcete-li určit, kolik řádků se má zapsat do jednotlivých souborů CSV `createFileSet` , pokud je `TRUE` .

Do RStudio zadejte následující kód:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Tento krok je třeba dokončit přibližně po dobu 10 minut.

Když použijete `RxSpark` výpočetní kontext, `createFileSet` Výchozí hodnota `TRUE` `rowsPerOutFile` je a nemá žádný vliv. Proto pokud chcete vytvořit jeden sdílený svazek clusteru nebo přizpůsobit počet řádků na soubor, proveďte `rxDataStep` ve `local` výpočetním kontextu (data mohou být stále v HDFS).

## <a name="final-steps"></a>Poslední kroky

1. Vyčistěte data. Do RStudio zadejte následující kód:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Zastavte vzdálenou aplikaci Spark. Do RStudio zadejte následující kód:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Ukončete relaci jazyka R. Do RStudio zadejte následující kód:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu možná budete chtít cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

Pokud chcete odstranit cluster, přečtěte si téma [odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat funkce R v Apache Spark, které běží na clusteru HDInsight Machine Learning Services. Další informace najdete v následujících článcích:

* [Možnosti výpočetního kontextu pro cluster Azure HDInsight Machine Learning Services](r-server-compute-contexts.md)
* [Funkce R pro Spark v Hadoop](/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)