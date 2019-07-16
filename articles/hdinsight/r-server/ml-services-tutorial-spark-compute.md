---
title: 'Kurz: Použití R ve Sparku výpočetního kontextu v Azure HDInsight'
description: Kurz – Začínáme s R a Spark v clusteru Azure HDInsight, Machine Learning services.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: f072b6905881da7b7854b0e51d690dbbd40dffb5
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227445"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Kurz: Použití R ve Sparku výpočetního kontextu v Azure HDInsight

Tento kurz obsahuje podrobný Úvod k používání funkce R v Apache Sparku, které běží v clusteru Azure HDInsight, Machine Learning services.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Stáhněte si ukázková data do místního úložiště
> * Zkopírovat data do výchozího úložiště
> * Nastavení datové sady
> * Vytvoření zdroje dat
> * Vytvoření výpočetního kontextu pro Spark
> * Přizpůsobit lineární model
> * Použít složené souborů XDF
> * Převést XDF do sdíleného svazku clusteru

## <a name="prerequisites"></a>Požadavky

* Cluster Azure HDInsight, Machine Learning services. Přejděte na [vytvořit Apache Hadoop clusterů pomocí webu Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) a pro **typ clusteru**vyberte **služby ML**.

## <a name="connect-to-rstudio-server"></a>Připojení k RStudio Serveru

Přihlašovací stránce RStudio serveru běží na hraničním uzlu clusteru. Přejděte na následující web (kde *CLUSTERNAME* v adrese URL je název clusteru služby HDInsight, Machine Learning, který vytvořili):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Při prvním přihlášení se ověřujete dvakrát. Při první výzva ověřování, zadejte uživatelské jméno správce clusteru a heslo (výchozí hodnota je *správce*). Druhá výzva ověřování, zadejte uživatelské jméno SSH a heslo (výchozí hodnota je *sshuser*). Následující přihlášení vyžadovat jenom přihlašovací údaje pro SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Stáhněte si ukázková data do místního úložiště

*Letecká společnost 2012 včas datovou sadu* se skládá z 12 soubory oddělených čárkou, které obsahují letu příchod a odchod podrobnosti pro všechny obchodní lety v rámci USA v roce 2012. Tato datová sada je velká s více než 6 milióny pozorování.

1. Inicializace několika proměnných prostředí. V konzole RStudio serveru zadejte následující kód:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. V pravém podokně, vyberte **prostředí** kartu. Proměnné se zobrazí v části **hodnoty**.

    ![RStudio](./media/ml-services-tutorial-spark-compute/rstudio.png)

1.  Vytvořte místní adresář a stáhněte si ukázková data. V RStudio zadejte následující kód:

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

    Stažený soubor by měl být dokončeno v 9.5 minutách.

## <a name="copy-the-data-to-default-storage"></a>Zkopírovat data do výchozího úložiště

Umístění souboru systému HDFS (Hadoop Distributed) je zadán s `airDataDir` proměnné. V RStudio zadejte následující kód:

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

V kroku by měl být dokončeno v asi 10 sekund.

## <a name="set-up-a-dataset"></a>Nastavení datové sady

1. Vytvořte objekt systému souborů, který používá výchozí hodnoty. V RStudio zadejte následující kód:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Vzhledem k tomu, že původní soubory CSV místo těžkopádným názvy proměnných, zadáte *colInfo* seznam tak, aby byly lépe zvládnutelné. V RStudio zadejte následující kód:

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

## <a name="create-data-sources"></a>Vytvoření zdroje dat

Ve výpočetním kontextu Spark můžete vytvořit zdroje dat pomocí následující funkce:

|Funkce | Popis |
|---------|-------------|
|`RxTextData` | Zdroj dat text oddělený čárkami. |
|`RxXdfData` | Data ve formátu dat souborů XDF. V RevoScaleR se mění formát souborů XDF pro Hadoop pro ukládání dat v sadě složené soubory spíše než jeden soubor. |
|`RxHiveData` | Generuje objekt zdroje dat Hive.|
|`RxParquetData` | Generuje Parquet zdroj dat objektu.|
|`RxOrcData` | Vytvoří objekt zdroje dat Orc.|

Vytvoření [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) s použitím souborů, které jste zkopírovali do rozhraní HDFS. V RStudio zadejte následující kód:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Vytvoření výpočetního kontextu pro Spark

Načtení dat a spuštění analýzy na pracovní uzly, nastavte výpočetní kontext ve skriptu pro [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). V tomto kontextu funkcí jazyka R automaticky distribuuje úlohy mezi všech pracovních uzlů, nemá vestavěné požadavky pro správu úloh nebo fronty. Výpočetní kontext Spark pokládáme stav, prostřednictvím `RxSpark` nebo `rxSparkConnect()` Sparku k vytvoření výpočetního kontextu a používá `rxSparkDisconnect()` se vraťte k místním výpočetním kontextu. V RStudio zadejte následující kód:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Přizpůsobit lineární model

1. Použití [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) fungovat podle lineární model se pomocí vaší `airDS` zdroj dat. V RStudio zadejte následující kód:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Tento krok by měl být dokončeno v 2 až 3 minut.

1. Zkontrolujte výsledky. V RStudio zadejte následující kód:

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

    Výsledky naznačují, že jste zpracovává všechna data, 6 milionů pozorování pomocí souboru CSV v zadaném adresáři. Protože jste zadali `cube = TRUE`, máte odhadované koeficient pro každý den v týdnu (a ne zachycení).

## <a name="use-composite-xdf-files"></a>Použít složené souborů XDF

Jak už víte, můžete analyzovat soubory CSV s jazykem R přímo v systému Hadoop. Ale můžete provádět analýzy rychlejší pokud uchováváte data ve formátu efektivnější. Formát souborů R XDF je efektivní, ale jeho změnil trochu pro HDFS tak, aby jednotlivé soubory zůstávají v rámci jednoho bloku HDFS. (Velikost bloku HDFS se liší od instalace k instalaci, ale je obvykle 64 MB nebo 128 MB). 

Při použití [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) na Hadoop pro vytvoření sady složených souborů XDF zadáte `RxTextData` zdroje dat, jako `AirDS` jako inData a `RxXdfData` zdroje dat pomocí systému souborů nastavena na systém souborů HDFS jako outFile argument. Pak můžete použít `RxXdfData` objektu jako argument v dalších analýz R data.

1. Definování `RxXdfData` objektu. V RStudio zadejte následující kód:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Nastavte velikost bloku 250000 řádků a určit, že jsme četla všechna data. V RStudio zadejte následující kód:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importovat data pomocí `rxImport`. V RStudio zadejte následující kód:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Tento krok by měl být dokončeno za pár minut.

1. Znovu odhadněte stejný lineární model používání zdroje dat novou, rychlejší. V RStudio zadejte následující kód:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    V kroku by měl být dokončeno za méně než jednu minutu.

1. Zkontrolujte výsledky. Výsledky by měl být stejný jako z souborů CSV. V RStudio zadejte následující kód:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Převést XDF do sdíleného svazku clusteru

### <a name="in-a-spark-context"></a>V kontextu Spark

Pokud soubory sdíleného svazku clusteru se převést na formát souborů XDF pro větší efektivitu při spouštění analýz, ale nyní chcete převést data zpět do sdíleného svazku clusteru, můžete to provést pomocí [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Chcete-li vytvořit složku nebo souborů CSV, nejprve vytvořte `RxTextData` s použitím názvu adresáře jako argument soubor. Tento objekt představuje složku, ve kterém chcete vytvořit soubory sdíleného svazku clusteru. Tento adresář se vytvoří při spuštění `rxDataStep`. Přejděte na tuto `RxTextData` objekt `outFile` argument `rxDataStep`. Každý sdílený svazek clusteru, který je vytvořen je s názvem podle názvu adresáře a za nímž následuje číslo.

Předpokládejme, že chcete vypsat složek nebo souborů CSV v HDFS z vaší `airDataXdf` složené XDF po provedení logistické regrese a predikcí, tak, aby se nové soubory CSV obsahovat předpovězeným hodnotám a odchylky. V RStudio zadejte následující kód:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Tento krok by měl být dokončeno v přibližně 2,5 minut.

`rxDataStep` Vypsána jednoho souboru CSV pro každý soubor XDFD ve vstupním souboru složené XDF. Toto je výchozí chování pro zapisování souborů sdíleného svazku clusteru od složených souborů XDF do HDFS, pokud výpočetní kontext je nastavena na `RxSpark`.

### <a name="in-a-local-context"></a>V rámci místní

Můžete také po dokončení provádění vaší analýzy, může přepnout výpočetní kontext zpět `local` výhod dvou argumentů v rámci `RxTextData` , která poskytují o něco větší kontrolu při psaní soubory sdíleného svazku clusteru do rozhraní HDFS: `createFileSet` a `rowsPerOutFile`. Pokud nastavíte `createFileSet` k `TRUE`, složku nebo souborů CSV je zapsán do adresáře, který určíte. Pokud nastavíte `createFileSet` k `FALSE`, je zapsán jednoho souboru CSV. Druhý argument, můžete nastavit `rowsPerOutFile`, na celé číslo označuje, kolik řádků chcete zapsat do každý sdílený svazek clusteru souboru, když `createFileSet` je `TRUE`.

V RStudio zadejte následující kód:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Tento krok by měl být dokončeno v přibližně 10 minut.

Při použití `RxSpark` výpočetního kontextu, `createFileSet` výchozí hodnota je `TRUE` a `rowsPerOutFile` nemá žádný vliv. Proto pokud chcete vytvořit jeden sdílený svazek clusteru nebo upravit počet řádků na soubor, proveďte `rxDataStep` v `local` výpočetním kontextu (data mohou být stále v HDFS).

## <a name="final-steps"></a>Závěrečné kroky

1. Vyčistěte data. V RStudio zadejte následující kód:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Zastavte vzdálené aplikace Spark. V RStudio zadejte následující kód:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Ukončení relace R. V RStudio zadejte následující kód:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete cluster odstranit. S HDInsight vaše data uložená ve službě Azure Storage, takže je můžete clusteru bezpečně odstranit, až nebude používán. Můžete také za cluster služby HDInsight i v případě, že není používán. Vzhledem k tomu, že poplatky za cluster představují než poplatky za úložiště, dává ekonomický smysl odstraňovat clustery, když nejsou používána.

Odstranění clusteru, naleznete v tématu [odstranění clusteru služby HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat R funkce v Apache Sparku, které jsou spuštěny v clusteru služby HDInsight, Machine Learning. Další informace najdete v následujících článcích:

* [Možnosti výpočetního kontextu pro cluster Azure HDInsight, Machine Learning services](r-server-compute-contexts.md)
* [Funkce jazyka R pro Spark na systému Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
