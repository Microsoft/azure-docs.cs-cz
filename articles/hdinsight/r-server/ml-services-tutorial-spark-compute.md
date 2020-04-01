---
title: 'Kurz: Použití R v výpočetním kontextu Spark v Azure HDInsight'
description: Kurz – Začínáme s R a Spark em v clusteru služeb Azure HDInsight Machine Learning Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 73ca0d089ab758fb13e69d341337139d79194cc5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121941"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Kurz: Použití R v výpočetním kontextu Spark v Azure HDInsight

Tento kurz poskytuje podrobný úvod k používání funkcí R v Apache Spark, které běží v clusteru služeb Azure HDInsight Machine Learning.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Stažení ukázkových dat do místního úložiště
> * Kopírování dat do výchozího úložiště
> * Nastavení datové sady
> * Vytváření zdrojů dat
> * Vytvoření výpočetního kontextu pro Spark
> * Přizpůsobit lineární model
> * Použití složených souborů XDF
> * Převést XDF na CSV

## <a name="prerequisites"></a>Požadavky

* Cluster služeb Azure HDInsight Machine Learning. Přejděte na [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) a v případě **typu clusteru**vyberte **služby ML Services**.

## <a name="connect-to-rstudio-server"></a>Připojení k RStudio Serveru

Server RStudio je spuštěn na hraničním uzlu clusteru. Přejděte na následující web (kde *cluster CLUSTERNAME* v adrese URL je název clusteru služeb HDInsight Machine Learning, který jste vytvořili):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Při prvním přihlášení se dvakrát ověřujete. Při první výzvě k ověření zadejte uživatelské jméno a heslo správce clusteru (výchozí je *správce*). Na druhé výzvy k ověření zadejte uživatelské jméno A heslo SSH (výchozí je *sshuser*). Následné přihlášení vyžadují pouze pověření SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Stažení ukázkových dat do místního úložiště

*Soubor dat společnosti On-Time 2012* se skládá z 12 souborů oddělených čárkami, které obsahují údaje o příletu a odletu pro všechny komerční lety v USA za rok 2012. Tato datová sada je velká s více než 6 miliony pozorování.

1. Inicializovat několik proměnných prostředí. V konzole RStudio Server zadejte následující kód:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. V pravém podokně vyberte kartu **Životní prostředí.** Proměnné jsou zobrazeny v části **Hodnoty**.

    ![HdInsight R studio webová konzole](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Vytvořte místní adresář a stáhněte ukázková data. V RStudiu zadejte následující kód:

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

    Stahování by mělo být dokončeno asi za 9,5 minuty.

## <a name="copy-the-data-to-default-storage"></a>Kopírování dat do výchozího úložiště

Umístění Hadoop Distributed File System (HDFS) `airDataDir` je určeno s proměnnou. V RStudiu zadejte následující kód:

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

Krok by měl být dokončen asi za 10 sekund.

## <a name="set-up-a-dataset"></a>Nastavení datové sady

1. Vytvořte objekt systému souborů, který používá výchozí hodnoty. V RStudiu zadejte následující kód:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Vzhledem k tomu, že původní soubory CSV mají spíše nepraktický název proměnných, zadáte seznam *colInfo,* aby byly lépe spravovatelné. V RStudiu zadejte následující kód:

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

V kontextu výpočetních zařízení Spark můžete vytvářet zdroje dat pomocí následujících funkcí:

|Funkce | Popis |
|---------|-------------|
|`RxTextData` | Textový zdroj dat oddělený čárkami. |
|`RxXdfData` | Data ve formátu datového souboru XDF. V revoscaler, formát souboru XDF je upraven pro Hadoop ukládat data ve složené sadě souborů, nikoli jeden soubor. |
|`RxHiveData` | Generuje objekt zdroje dat Hive.|
|`RxParquetData` | Generuje objekt zdroje dat parket.|
|`RxOrcData` | Generuje objekt zdroje dat orků.|

Vytvořte objekt [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) pomocí souborů, které jste zkopírovali do HDFS. V RStudiu zadejte následující kód:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Vytvoření výpočetního kontextu pro Spark

Chcete-li načíst data a spustit analýzy na pracovních uzlech, nastavte výpočetní kontext ve skriptu na [RxSpark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). V tomto kontextu r funkce automaticky distribuovat zatížení mezi všechny pracovní uzly, bez předdefinované požadavky pro správu úloh nebo fronty. Výpočetní kontext Spark se `RxSpark` `rxSparkConnect()` nastavuje prostřednictvím nebo `rxSparkDisconnect()` k vytvoření výpočetního kontextu Spark a používá se k návratu do místního výpočetního kontextu. V RStudiu zadejte následující kód:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Přizpůsobit lineární model

1. Použijte funkci [rxLinMod,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) aby se `airDS` vešla do lineárního modelu pomocí zdroje dat. V RStudiu zadejte následující kód:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Tento krok by měl být dokončen za 2 až 3 minuty.

1. Zkontrolujte výsledky. V RStudiu zadejte následující kód:

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

    Výsledky ukazují, že jste zpracovali všechna data, 6 milionů pozorování, pomocí všech souborů CSV v zadaném adresáři. Protože jste `cube = TRUE`zadali , máte odhadovaný koeficient pro každý den v týdnu (a ne zachytit).

## <a name="use-composite-xdf-files"></a>Použití složených souborů XDF

Jak jste viděli, můžete analyzovat soubory CSV přímo s R na Hadoop. Analýzu však můžete provést rychleji, pokud data uložíte v efektivnějším formátu. Formát souboru R XDF je efektivní, ale je poněkud upraven pro HDFS tak, aby jednotlivé soubory zůstaly v rámci jednoho bloku HDFS. (Velikost bloku HDFS se liší od instalace k instalaci, ale je obvykle buď 64 MB nebo 128 MB.) 

Při použití [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) na Hadoop vytvořit sadu složených souborů `RxTextData` XDF, `AirDS` zadáte zdroj dat, `RxXdfData` jako je například inData a zdroj dat se fileSystem nastavena na systém souborů HDFS jako outFile argument. Potom můžete použít `RxXdfData` objekt jako argument data v následných R analýzách.

1. Definujte `RxXdfData` objekt. V RStudiu zadejte následující kód:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Nastavte velikost bloku 250000 řádků a určete, že čteme všechna data. V RStudiu zadejte následující kód:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importujte data `rxImport`pomocí aplikace . V RStudiu zadejte následující kód:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Tento krok by měl být dokončen během několika minut.

1. Přeceňte stejný lineární model pomocí nového, rychlejšího zdroje dat. V RStudiu zadejte následující kód:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Krok by měl být dokončen za méně než minutu.

1. Zkontrolujte výsledky. Výsledky by měly být stejné jako ze souborů CSV. V RStudiu zadejte následující kód:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Převést XDF na CSV

### <a name="in-a-spark-context"></a>V kontextu Spark

Pokud jste převedli soubory CSV do formátu souboru XDF pro větší efektivitu při spouštění analýz, ale nyní chcete převést data zpět na CSV, můžete tak učinit pomocí [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Chcete-li vytvořit složku souborů CSV, nejprve vytvořte `RxTextData` objekt pomocí názvu adresáře jako argumentu souboru. Tento objekt představuje složku, ve které chcete vytvořit soubory CSV. Tento adresář je vytvořen `rxDataStep`při spuštění rozhraní . Potom přejděte `RxTextData` na tento `outFile` objekt `rxDataStep`v argumentu . Každý soubor CSV, který je vytvořen, je pojmenován na základě názvu adresáře a následuje číslo.

Předpokládejme, že chcete zapisovat složku souborů CSV `airDataXdf` v HDFS z složeného XDF po provedení logistické regrese a předpovědi, aby nové soubory CSV obsahovaly předpokládané hodnoty a zbytky. V RStudiu zadejte následující kód:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Tento krok by měl být dokončen asi za 2,5 minuty.

Vypsal `rxDataStep` jeden soubor CSV pro každý soubor XDFD ve vstupním složeném souboru XDF. Toto je výchozí chování pro zápis souborů CSV ze složených souborů XDF do HDFS, pokud je výpočetní kontext nastaven na `RxSpark`hodnotu .

### <a name="in-a-local-context"></a>V místním kontextu

Případně, když jste hotovi provádění analýz, můžete přepnout výpočetní `local` kontext zpět, aby využili `RxTextData` dva argumenty v rámci které vám o něco větší `createFileSet` `rowsPerOutFile`kontrolu, když píšete soubory CSV hdfs: a . Pokud nastavíte hodnotu `createFileSet` `TRUE`, je do zadaného adresáře zapsána složka souborů CSV. Pokud nastavíte na `createFileSet` `FALSE`, je zapsán jeden soubor CSV. Druhý argument můžete nastavit `rowsPerOutFile`na celé číslo, které označuje, kolik řádků má `createFileSet` být `TRUE`zapsáno do každého souboru CSV, když je .

V RStudiu zadejte následující kód:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Tento krok by měl být dokončen asi za 10 minut.

Při použití `RxSpark` výpočetní `createFileSet` kontext, výchozí `TRUE` `rowsPerOutFile` a nemá žádný vliv. Proto pokud chcete vytvořit jeden CSV nebo přizpůsobit počet řádků `rxDataStep` na `local` soubor, proveďte v kontextu výpočetní (data mohou být stále v HDFS).

## <a name="final-steps"></a>Závěrečné kroky

1. Vyčistěte data. V RStudiu zadejte následující kód:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Zastavte vzdálenou aplikaci Spark. V RStudiu zadejte následující kód:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Ukončete relaci R. V RStudiu zadejte následující kód:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení kurzu můžete chtít odstranit cluster. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není používán. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když se nepoužívají.

Pokud chcete odstranit cluster, přečtěte si informace [o odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového příkazu k Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat funkce R v Apache Spark, které běží v clusteru služeb HDInsight Machine Learning. Další informace najdete v těchto článcích:

* [Možnosti výpočetního kontextu pro cluster služeb Azure HDInsight Machine Learning](r-server-compute-contexts.md)
* [R Funkce pro Spark na Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
