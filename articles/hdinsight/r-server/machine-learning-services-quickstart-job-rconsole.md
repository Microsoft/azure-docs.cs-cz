---
title: 'Rychlý start: Spustit skript R v clusteru služby ML v Azure HDInsight pomocí konzoly R'
description: V tomto rychlém startu můžete spustit skript R v clusteru služby ML v Azure HDInsight pomocí konzoly R.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 682ee4f44dcdd2619668645fa7a8aa22cb645273
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450923"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>Rychlý start: Spustit skript R v clusteru služby ML v Azure HDInsight pomocí konzoly R

ML služeb v Azure HDInsight umožňuje skripty R používat Apache Spark a Apache Hadoop MapReduce ke spouštění distribuovaných výpočtů. Služby ML Určuje, jak jsou spouštěny nastavení výpočetního kontextu volání. Hraničním uzlu clusteru poskytuje praktické místo k připojení ke clusteru a ke spuštění skriptů jazyka R. Díky hraniční uzel máte možnost spuštění paralelizované distribuované funkce RevoScaleR mezi jádry hraničního uzlu serveru. Můžete také spustit je na uzlech clusteru s použitím jeho RevoScaleR Hadoop Map Reduce nebo kontexty služby compute Apache Spark.

V tomto rychlém startu se dozvíte, jak chcete spustit skript R s konzoly R, který předvádí použití Sparku k distribuovaným výpočtům jazyka r. Bude definovat výpočetní kontext provádět výpočty místně na hraničním uzlu a znovu distribuovány napříč uzly v clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster služby ML v HDInsight. Zobrazit [vytvořit Apache Hadoop clusterů pomocí webu Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **služby ML** pro **typ clusteru**.

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="connect-to-r-console"></a>Připojte se ke konzole R

1. Připojení k hraničnímu uzlu clusteru ML služby HDInsight pomocí SSH. Upravte následující příkaz tak, že nahradíte `CLUSTERNAME` s názvem vašeho clusteru a potom zadejte příkaz:

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

    Tento krok může trvat přibližně 10 minut na dokončení.

1. Některé informace o datech a nadefinujeme dva zdroje dat. V konzole R, zadejte následující kód:

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

1. Spusťte logistickou regresi přes data s využitím **místní** výpočetního kontextu. V konzole R, zadejte následující kód:

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

    Výpočtů by se měla dokončit během 7 minut. Byste měli vidět výstup, který končí řádky podobný následujícímu fragmentu kódu:

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

1. Spusťte stejnou logistickou regresi pomocí **Spark** kontextu. Kontext Spark distribuuje zpracování do všech pracovních uzlů v clusteru HDInsight. V konzole R, zadejte následující kód:

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

    Výpočtů by se měla dokončit během 5 minut.

1. Pokud chcete konzolu R zavřít, použijte následující příkaz:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu, můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Odstranění clusteru, naleznete v tématu [odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak chcete spustit skript R s konzoly R, který jsme vám ukázali použití Sparku k distribuovaným výpočtům jazyka r.  Přejděte k dalšímu článku další možnosti, které jsou k dispozici k určení, zda a jak je provádění paralelizována přes jader hraničním uzlu nebo clusteru HDInsight.

> [!div class="nextstepaction"]
>[Možnosti výpočetního kontextu pro služby ML v HDInsight](./r-server-compute-contexts.md)