---
title: 'Rychlý Start: RStudio Server & ML Services pro R-Azure HDInsight'
description: V rychlém startu spustíte skript R v clusteru služby ML ve službě Azure HDInsight pomocí serveru RStudio.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 7604a327c43ed1942d336399b6d8b9dbb1e64d75
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935717"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-rstudio-server"></a>Rychlý Start: spuštění skriptu R v clusteru služby ML ve službě Azure HDInsight pomocí serveru RStudio

Služba ML Services v Azure HDInsight umožňuje skriptům R používat Apache Spark a Apache Hadoop MapReduce ke spouštění distribuovaných výpočtů. Služba ML řídí, jak se spouštějí volání, nastavením výpočetního kontextu. Hraniční uzel clusteru poskytuje vhodné místo pro připojení ke clusteru a spouštění skriptů jazyka R. Pomocí hraničního uzlu máte možnost spouštět paralelní distribuované funkce RevoScaleR napříč jádry serveru hraničního uzlu. Můžete je také spouštět v uzlech clusteru pomocí RevoScaleR mapy Hadoop pro redukci nebo Apache Spark výpočetních kontextů.

V tomto rychlém startu se dozvíte, jak spustit skript R s RStudio serverem, který ukazuje použití Sparku pro distribuované výpočty R. Definujete výpočetní kontext pro místní provádění výpočtů na hraničním uzlu a znovu rozšíříte napříč uzly v clusteru HDInsight.

## <a name="prerequisite"></a>Požadavek

Cluster služeb ML v HDInsight. Přečtěte si téma [vytvoření Apache Hadoop clusterů pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) a výběr **služeb ml** pro **typ clusteru**.

## <a name="connect-to-rstudio-server"></a>Připojení k RStudio Serveru

RStudio server běží na hraničním uzlu clusteru. V části `CLUSTERNAME` název clusteru služby ml, který jste vytvořili, použijte následující adresu URL:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Při prvním přihlášení budete muset dvakrát ověřit. Pro první výzvu k ověření zadejte přihlašovací jméno a heslo správce clusteru. výchozí hodnota je `admin` . Pro druhé zobrazení výzvy k ověření zadejte přihlašovací jméno a heslo SSH, výchozí hodnota je `sshuser` . Další přihlašovací údaje vyžadují jenom pověření SSH.

Po připojení by vaše obrazovka měla vypadat podobně jako na následujícím snímku obrazovky:

![Přehledy webové konzoly R Studio](./media/ml-services-quickstart-job-rstudio/connect-to-r-studio1.png)

## <a name="use-a-compute-context"></a>Použití výpočetního kontextu

1. Z RStudio serveru pomocí následujícího kódu načtěte ukázková data do výchozího úložiště pro HDInsight:

    ```RStudio
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Dokončení tohoto kroku může trvat přibližně 8 minut.

1. Vytvořte nějaké informace o datech a definujte dva zdroje dat. Do RStudio zadejte následující kód:

    ```RStudio
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Spusťte z dat logistickou regresi pomocí **místního** výpočetního kontextu. Do RStudio zadejte následující kód:

    ```RStudio
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    Výpočty by měly být dokončeny během přibližně 7 minut. Měl by se zobrazit výstup, který končí řádky podobné následujícímu fragmentu kódu:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘**_’ 0.001 ‘_*’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Spusťte stejnou logistickou regresi pomocí kontextu **Spark** . Kontext Spark distribuuje zpracování do všech pracovních uzlů v clusteru HDInsight. Do RStudio zadejte následující kód:

    ```RStudio
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    Výpočty by měly být dokončeny během přibližně 5 minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Pokud chcete odstranit cluster, přečtěte si téma [odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak spustit skript R se serverem RStudio, který ukázal použití Sparku pro distribuované výpočty R.  V dalším článku se dozvíte, jaké možnosti jsou k dispozici pro určení toho, zda a jak je provádění paralelně v jádrech hraničního uzlu nebo clusteru HDInsight.

> [!div class="nextstepaction"]
>[Možnosti výpočetního kontextu pro služby ML v HDInsight](./r-server-compute-contexts.md)

> [!NOTE]
> Tato stránka popisuje funkce softwaru RStudio. K Microsoft Azure HDInsight není přidružena společnost RStudio, Inc.
