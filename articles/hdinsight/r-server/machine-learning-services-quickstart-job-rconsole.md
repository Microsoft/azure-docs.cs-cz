---
title: 'Úvodní příručka: Skript R na konzoli ML Services & R – Azure HDInsight'
description: V rychlém startu spustíte skript R v clusteru služeb ML v Azure HDInsight pomocí konzoly R.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: af41a7569dedc9a56f67be8ae791d7185e0c7489
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73241596"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>Úvodní příručka: Spuštění skriptu R v clusteru služby ML v Azure HDInsight pomocí konzoly R

Ml Services na Azure HDInsight umožňuje Skripty R používat Apache Spark a Apache Hadoop MapReduce ke spuštění distribuovaných výpočtů. Služba ML Services řídí způsob spouštění volání nastavením výpočetního kontextu. Hraniční uzel clusteru poskytuje vhodné místo pro připojení ke clusteru a spuštění skriptů R. S hraničním uzlem máte možnost spustit paralelizované distribuované funkce RevoScaleR přes jádra serveru hraničního uzlu. Můžete je také spustit přes uzly clusteru pomocí revoscaler je Hadoop mapa snížit nebo Apache Spark výpočetní kontexty.

V tomto rychlém startu se dozvíte, jak spustit skript R s konzolou R, která ukazuje použití Spark pro distribuované výpočty R. Definujete výpočetní kontext k místnímu provádění výpočtů na hraničním uzlu a znovu distribuujte mezi uzly v clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster služeb ML na hdinsightu. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **služby ML** pro typ **clusteru**.

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="connect-to-r-console"></a>Připojení ke konzoli R

1. Připojte se k hraničnímu uzlu clusteru HDInsight služby ML services pomocí ssh. Upravte níže uvedený `CLUSTERNAME` příkaz nahrazením názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

1. Z relace SSH pomocí následujícího příkazu spusťte konzolu R:

    ```
    R
    ```

    Měl by se zobrazit výstup s verzí ML Serveru a dalšími informacemi.


## <a name="use-a-compute-context"></a>Použití výpočetního kontextu

1. Na příkazovém řádku `>` můžete zadat kód R. Pomocí následujícího kódu načtěte ukázková data do výchozího úložiště pro HDInsight:

    ```R
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

    Tento krok může trvat přibližně 10 minut.

1. Vytvořte některé informace o datech a definujte dva zdroje dat. Do konzoly R zadejte následující kód:

    ```R
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

1. Spusťte logistickou regresi přes data pomocí **místního** výpočetního kontextu. Do konzoly R zadejte následující kód:

    ```R
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    Výpočty by měly být dokončeny asi za 7 minut. Měli byste vidět výstup, který končí řádky podobné následující úryvek:

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
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Spusťte stejnou logistickou regresi pomocí kontextu **Spark.** Kontext Spark distribuuje zpracování do všech pracovních uzlů v clusteru HDInsight. Do konzoly R zadejte následující kód:

    ```R
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

    Výpočty by měly být dokončeny asi za 5 minut.

1. Pokud chcete konzolu R zavřít, použijte následující příkaz:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Pokud chcete odstranit cluster, přečtěte si informace [o odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového příkazu k Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak spustit skript R s konzolou R, která demonstrovala použití Spark pro distribuované výpočty R.  Přejdete k dalšímu článku a dozvíte se možnosti, které jsou k dispozici k určení, zda a jak je provádění paralelizováno mezi jádry hraničního uzlu nebo clusteru HDInsight.

> [!div class="nextstepaction"]
>[Možnosti výpočetního kontextu pro služby ML na hdinsightu](./r-server-compute-contexts.md)