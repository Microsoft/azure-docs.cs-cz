---
title: 'Kurz: Použití R ve Sparku výpočetního kontextu v Azure HDInsight'
description: Kurz – Začínáme s R a Spark v služby ML.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 244c62467f187417bbb9f0e54173aad5a7d26d0a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450279"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Kurz: Použití R ve Sparku výpočetního kontextu v Azure HDInsight

Tento kurz obsahuje podrobný Úvod k používání funkce R v Apache Spark spouští v clusteru služby ML v Azure HDInsight.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Stáhněte si ukázková data do místního úložiště
> * Kopírování dat do výchozího úložiště
> * Nastavení datové sady
> * Vytvoření zdroje dat
> * Vytvoření výpočetního kontextu pro Spark
> * Přizpůsobit lineární model
> * Použít složené souborů XDF
> * Převést XDF do sdíleného svazku clusteru

## <a name="prerequisites"></a>Požadavky

* Cluster služby ML v HDInsight. Zobrazit [vytvořit Apache Hadoop clusterů pomocí webu Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **služby ML** pro **typ clusteru**.

## <a name="connect-to-rstudio-server"></a>Připojení k RStudio Serveru

Přihlašovací stránce RStudio serveru běží na hraničním uzlu clusteru. Přejděte na následující adresu URL kde `CLUSTERNAME` je název clusteru služby ML jste vytvořili:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Při prvním přihlášení potřeba dvojí ověření. První zobrazení výzvy k ověření, zadejte přihlašovací jméno správce clusteru a heslo, výchozí hodnota je `admin`. Druhý zobrazení výzvy k ověření, zadejte přihlašování přes SSH a heslo, výchozí hodnota je `sshuser`. Následující přihlášení potřebovat jenom přihlašovací údaje SSH.

## <a name="download-sample-data"></a>Stáhněte si ukázková data

*Letecká společnost 2012 včas datovou sadu* se skládá z 12 oddělených čárkou soubory, které obsahují informace o letu příchod a odchod podrobnosti pro všechny obchodní lety v USA, v roce 2012. To je velké datové sady s více než 6 milionů pozorování.

1. Inicializace několika proměnných prostředí. V konzole RStudio serveru zadejte následující kód:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

    Proměnné se zobrazí na pravé straně obrazovky v části **prostředí** kartu.

    ![RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

2.  Vytvořit místní adresář a stáhněte si ukázková data. V RStudio, zadejte následující kód:

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

    Stažený soubor by se měl dokončit v přibližně 9 a půl minut.

## <a name="copy-data-to-default-storage"></a>Kopírování dat do výchozího úložiště

Umístění HDFS je zadán s `airDataDir` proměnné. V RStudio, zadejte následující kód:

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

Krok by se měla dokončit během přibližně 10 sekund.

## <a name="set-up-data-set"></a>Nastavení datové sady

1. Vytvořte objekt systému souborů, který používá výchozí hodnoty. V RStudio, zadejte následující kód:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

2. Původní soubory CSV mají spíše těžkopádným názvy proměnných, takže My dodáme `colInfo` seznam tak, aby byly lépe zvládnutelné. V RStudio, zadejte následující kód:

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

## <a name="create-data-source"></a>Vytvoření zdroje dat

Ve výpočetním kontextu Spark můžete vytvořit zdroje dat pomocí následující funkce:

|Funkce | Popis |
|---------|-------------|
|`RxTextData` | Zdroj dat text oddělený čárkami. |
|`RxXdfData` | Data ve formátu dat souborů XDF. V RevoScaleR se mění formát souborů XDF pro Hadoop pro ukládání dat v sadě složené soubory spíše než jeden soubor. |
|`RxHiveData` | Generuje objekt zdroje dat Hive.|
|`RxParquetData` | Generuje Parquet zdroj dat objektu.|
|`RxOrcData` | Vytvoří objekt zdroje dat Orc.|

Vytvoření [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) pomocí souborů, které jste zkopírovali do rozhraní HDFS. V RStudio, zadejte následující kód:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-compute-context-for-spark"></a>Vytvoření výpočetního kontextu pro Spark

Načtení dat a spuštění analýzy na pracovní uzly, nastavte výpočetní kontext ve skriptu pro [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). V tomto kontextu funkcí jazyka R automaticky distribuuje úlohy mezi všech pracovních uzlů, nemá vestavěné požadavky pro správu úloh nebo fronty. Výpočetní kontext Spark pokládáme stav, prostřednictvím `RxSpark` nebo `rxSparkConnect()` vytvořit Sparku výpočetní kontext a používá `rxSparkDisconnect()` se vraťte k místním výpočetním kontextu. V RStudio, zadejte následující kód:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Přizpůsobit lineární model

1. Použití [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) fungovat podle lineární model se pomocí vaší `airDS` zdroj dat. V RStudio, zadejte následující kód:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Tento krok by se měla dokončit mezi 2 a 3 minut.

1. Zkontrolujte výsledky. V RStudio, zadejte následující kód:

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

    Všimněte si, že výsledky naznačují zpracovali jsme všechna data, šesti milionů pozorování pomocí souboru CSV v zadaném adresáři. Všimněte si také, protože jsme zadali `cube = TRUE`, máme odhadované koeficient pro každý den v týdnu (a ne zachycení).

## <a name="use-composite-xdf-files"></a>Použít složené souborů XDF

Jak jsme viděli, můžete analyzovat soubory CSV s jazykem R přímo v systému Hadoop, ale analýza je rychleji provést, pokud jsou data uložená ve formátu efektivnější. Formát .xdf R je velmi efektivní, ale upraví trochu pro HDFS tak, aby jednotlivé soubory zůstávají v rámci jednoho bloku HDFS. (Velikost bloku HDFS se liší od instalace k instalaci, ale je obvykle 64 MB nebo 128 MB). Při použití [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) na systému Hadoop, zadáte `RxTextData` zdroje dat, jako `AirDS` jako inData a `RxXdfData` zdroje dat pomocí systému souborů nastavena na systém souborů HDFS jako argument Výstupní_soubor k vytvoření sady složený .xdf soubory. `RxXdfData` Objekt lze pak použít jako argument v dalších analýz R data.

1. Definování `RxXdfData` objektu. V RStudio, zadejte následující kód:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Nastavte velikost bloku 250000 řádků a určit, že jsme četla všechna data. V RStudio, zadejte následující kód:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importovat data pomocí `rxImport`. V RStudio, zadejte následující kód:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Tento krok by se měla dokončit během několika minut.

1. Znovu odhadněte stejný lineární model používání zdroje dat novou, rychlejší. V RStudio, zadejte následující kód:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Krok by se měl dokončit za méně než jednu minutu.

1. Zkontrolujte výsledky. Výsledky by měl být stejný jako z souborů CSV. V RStudio, zadejte následující kód:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Převést XDF do sdíleného svazku clusteru

### <a name="in-a-spark-context"></a>V kontextu Spark

Pokud jste převedli vaše sdíleného svazku clusteru na XDF využít efektivitu při spouštění analýz, ale nyní chcete převést data zpět do sdíleného svazku clusteru můžete to udělat pomocí [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Chcete-li vytvořit složku nebo souborů CSV, nejprve vytvořte `RxTextData` pomocí názvu adresáře jako argument soubor; to představuje složku, ve kterém chcete vytvořit soubory sdíleného svazku clusteru. Tento adresář se vytvoří při spuštění `rxDataStep`. Přejděte na tuto `RxTextData` objekt `outFile` argument `rxDataStep`. Každý sdílený svazek clusteru vytvoří bude mít název, podle názvu adresáře a za nímž následuje číslo.

Předpokládejme, že chceme vypsat složky sdíleného svazku clusteru v HDFS z našich `airDataXdf` složené XDF po jsme provedli logistické regrese a predikcí, tak, aby se nové soubory CSV obsahovat předpovězeným hodnotám a odchylky. V RStudio, zadejte následující kód:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Tento krok by se měla dokončit během minut dva a půl.

Všimněte si, že `rxDataStep` vypsána jeden sdílený svazek clusteru pro každý soubor .xdfd ve vstupním souboru složené XDF. Toto je výchozí chování pro zápis sdíleného svazku clusteru ze složeného XDF do rozhraní HDFS, pokud výpočetní kontext je nastavena na `RxSpark`.

### <a name="in-a-local-context"></a>V rámci místní

Alternativně může přepnout výpočetní kontext zpět `local` po dokončení provádění vaší analýzy a využijte výhod dvou argumentů v rámci `RxTextData` , která poskytují o něco větší kontrolu při zápisu do HDFS, soubory CSV: `createFileSet` a `rowsPerOutFile`. Když `createFileSet` je nastavena na `TRUE`, složku nebo souborů CSV je zapsán do adresáře, který zadáte. Když `createFileSet` je nastavena na `FALSE` zápisu jednoho souboru CSV. Druhý argument, `rowsPerOutFile`, můžete nastavit na celé číslo označuje, kolik řádků chcete zapsat do každý sdílený svazek clusteru souboru, když `createFileSet` je `TRUE`.

V RStudio, zadejte následující kód:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Tento krok by se měla dokončit během 10 minut.

Při použití `RxSpark` výpočetního kontextu, `createFileSet` výchozí hodnota je `TRUE` a `rowsPerOutFile` nemá žádný vliv. Proto pokud chcete vytvořit jeden sdílený svazek clusteru nebo upravit počet řádků na soubor, je nutné provést `rxDataStep` v `local` výpočetním kontextu (dat může být stále v HDFS).

## <a name="final-steps"></a>Závěrečné kroky

1. Vyčistěte Data. V RStudio, zadejte následující kód:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Zastavte vzdálené aplikace Spark. V RStudio, zadejte následující kód:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Ukončení relace R. V RStudio, zadejte následující kód:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení kurzu můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Odstranění clusteru, naleznete v tématu [odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak používat funkce jazyka R v Apache Spark spouští v clusteru služby ML v Azure HDInsight. Další informace najdete v následujících článcích:

* [Možnosti výpočetního kontextu pro služby ML v HDInsight](r-server-compute-contexts.md)
* [Funkce jazyka R pro Spark na systému Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
