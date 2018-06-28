---
title: Správa služby ML clusteru v HDInsight - Azure | Microsoft Docs
description: Zjistěte, jak ke správě clusteru služby ML služby v Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: bb3af3b1614c8afc98d2dcf12ecb53fb80b6037a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049740"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Správě clusteru ML služby v Azure HDInsight

V tomto článku zjistěte, jak spravovat existující cluster ML služby v Azure HDInsight k provedení úlohy, jako je přidání mulitiple souběžných uživatelů, vzdáleném připojení ke clusteru služby ML, změna kontextu výpočetní atd.

## <a name="prerequisites"></a>Požadavky

* **Cluster služby ML služby v HDInsight**: pokyny najdete v tématu [Začínáme se službou ML v HDInsight](r-server-get-started.md).

* **Klient Secure Shell (SSH):** Klient SSH slouží k vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo v clusteru. Další informace najdete v tématu [použití SSH s HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Povolení několika souběžných uživatelů

Přidáním více uživatelů pro hraniční uzel, na kterém běží verze Rstudia komunity můžete povolit více souběžných uživatelů pro cluster služby ML v HDInsight. Při vytváření clusteru HDInsight musíte zadat dva uživatele – uživatele HTTP a uživatele SSH:

![Souběžný uživatel 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Uživatelské jméno přihlášení clusteru:** Uživatel HTTP pro ověřování prostřednictvím brány HDInsight, která slouží k ochraně vytvořených clusterů HDInsight. Tento uživatel HTTP slouží k přístupu k uživatelskému rozhraní Ambari, uživatelskému rozhraní YARN a dalším komponentám uživatelského rozhraní.
- **Uživatelské jméno Secure Shell (SSH):** Uživatel SSH sloužící k přístupu ke clusteru přes Secure Shell. Tento uživatel je uživatel v systému Linux pro všechny hlavní uzly, pracovní uzly a hraniční uzly. Proto můžete použít Secure Shell pro přístup k jakémukoli uzlu ve vzdáleném clusteru.

Verze komunity serveru R Studio použitá v clusteru služby ML v HDInsight akceptuje pouze Linux uživatelské jméno a heslo jako přihlášení mechanismus. Nepodporuje předávání tokenů. Ano při pokusu o přístup k R Studio poprvé v clusteru služby ML, budete muset přihlásit dvakrát.

- První přihlášení pomocí pověření uživatele HTTP přes bránu HDInsight. 

- Pak použijte přihlašovací údaje uživatele SSH pro přihlášení k Rstudia.
  
V současné době je možné při zřizování clusteru HDInsight vytvořit pouze jeden uživatelský účet SSH. Povolit více uživatelů pro přístup ke clusteru služby ML v HDInsight, takže musíte vytvořit další uživatele v systému Linux.

Protože Rstudia běží na clusteru hraniční uzel, existují zde několik kroků:

1. Použití stávajícího uživatele SSH k přihlášení k uzlu edge
2. Přidání dalších uživatelů Linuxu na hraničním uzlu
3. Použití komunitní verze RStudia s vytvořeným uživatelem

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Krok 1: Použití vytvořeného uživatele SSH k přihlášení k uzlu edge

Postupujte podle pokynů v [připojení k HDInsight (Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) pro přístup k uzlu edge. Adresa uzlu edge pro cluster služby ML v HDInsight je `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Krok 2: Přidání dalších uživatelů Linuxu na hraničním uzlu

Pokud chcete přidat uživatele na hraničním uzlu, spusťte následující příkazy:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

Následující snímek obrazovky ukazuje výstupy.

![Souběžný uživatel 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Po zobrazení výzvy „Aktuální heslo protokolu Kerberos:“ ji ignorujte stisknutím klávesy **Enter**. Možnost `-m` v příkazu `useradd` označuje, že systém pro uživatele vytvoří domovskou složku, kterou vyžaduje komunitní verze RStudia.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Krok 3: Použití komunitní verze RStudia s vytvořeným uživatelem

Přístup k Rstudia z https://CLUSTERNAME.azurehdinsight.net/rstudio/. Pokud jste se přihlašují poprvé po vytvoření clusteru, zadejte přihlašovací údaje Správce clusteru, za nímž následuje přihlašovacích údajů uživatele SSH, které jste vytvořili. Pokud to není prvním přihlášení, zadejte přihlašovací údaje pouze pro uživatele SSH, kterou jste vytvořili.

Můžete se také přihlásit pomocí přihlašovacích údajů, původní (ve výchozím nastavení, je *sshuser*) z jiného okna prohlížeče současně.

Všimněte si také, že nově přidaní uživatelé nemají v systému Linux kořenová oprávnění, ale mají stejný přístup ke všem souborům ve vzdáleném úložišti HDFS a WASB.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Vzdálené připojení ke službám Microsoft ML

Nastavením přístupu do kontextu výpočtů HDInsight Hadoop Spark ze vzdálené instance systému klienta ML spuštěná na ploše. Chcete-li to provést, musíte zadat možnosti (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches a sshProfileScript) při definování RxSpark výpočetní kontext na pracovní ploše: Příklad:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

Další informace najdete v části "Použití Machine Learning jako Hadoop klienta systému Microsoft Server" v [použití RevoScaleR v kontextu výpočtů Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Použití výpočetního kontextu

Výpočetní kontext vám umožňuje řídit, jestli se výpočty provádějí místně na hraničním uzlu, nebo jsou distribuovány napříč uzly v clusteru HDInsight.

1. V RStudio Serveru nebo konzole R (v rámci relace SSH) pomocí následujícího kódu načtěte ukázková data do výchozího úložiště pro službu HDInsight:

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
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

2. Dále vytvořte některé informace o data a definovat dvou zdrojů dat.

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

3. Spusťte logistic regression přes data s využitím místní výpočetní kontextu.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Měli byste vidět výstup, který končí řádky podobná následující fragment kódu:

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

4. Spusťte stejné logistic regression pomocí kontextu Spark. Kontext Spark distribuuje zpracování do všech pracovních uzlů v clusteru HDInsight.

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


   > [!NOTE]
   > Distribuovat výpočty napříč uzly clusteru můžete také pomocí MapReduce. Další informace o kontextu výpočetní najdete v tématu [výpočetní kontextu možnosti pro ML služby clusteru v HDInsight](r-server-compute-contexts.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuování kódu R do více uzlů

Službou ML v HDInsight, můžete využít existující kód R a spusťte napříč několika uzly v clusteru pomocí `rxExec`. Tato funkce je užitečná při uklízení parametrů nebo provádění simulací. Následující kód je příklad použití příkazu `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Pokud stále používáte kontext Spark nebo MapReduce, tento příkaz vrátí hodnoty nodename (název uzlu) všech pracovních uzlů, ve kterých je spuštěný kód `(Sys.info()["nodename"])`. Například na cluster se čtyřmi uzly očekáváte příjem výstup podobný následující fragment kódu:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-hive-and-parquet"></a>Přístup k datům v Hive a Parquet

ML služby HDInsight umožňuje přímý přístup k datům v Hive a Parquet pro použití ve ScaleR úkolů v rámci výpočtů Spark. Tyto možnosti jsou dostupné prostřednictvím nových funkcí zdroje dat ScaleR s názvem RxHiveData a RxParquetData. Funkce pomocí Spark SQL načtou data přímo do struktury DataFrame ve Sparku, aby je ScaleR mohl analyzovat.

Následuje vzorový kód pro použití těchto nových funkcí:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Další informace o těchto nových funkcí naleznete v online nápovědě ve ML Services prostřednictvím použití `?RxHivedata` a `?RxParquetData` příkazy.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Nainstalujte další balíčky R na clusteru

### <a name="to-install-r-packages-on-the-edge-node"></a>K instalaci balíčků R na uzlu edge

Pokud chcete nainstalovat další balíčky R na uzlu edge, můžete použít `install.packages()` přímo z v konzole R po připojení k uzlu edge prostřednictvím SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>K instalaci balíčků R v pracovním uzlu

Chcete-li instalovat balíčky R na pracovních uzlech clusteru, musíte použít akci skriptu. Akce skriptů jsou skripty Bash, které se používají k provádění změn konfigurace clusteru HDInsight nebo k instalaci dalšího softwaru, jako jsou například další balíčky R. 

> [!IMPORTANT]
> Instalace dalších balíčků R pomocí akcí skriptů je možná jedině po vytvoření clusteru. Tento postup nepoužívejte při vytváření clusteru, protože skript závisí na službách ML úplně konfigurován.
>
>

1. Postupujte podle kroků v [přizpůsobit clustery pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

3. Pro **odeslat akci se skripty**, zadejte následující informace:

   * Pro **typ skriptu**, vyberte **vlastní**.

   * Pro **název**, zadejte název akce skriptu.

    * Pro **Bash skriptu URI**, zadejte `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Toto je skript, který nainstaluje další balíčky R v pracovním uzlu

   * Vyberte zaškrtávací pole pouze pro **pracovní**.

   * **Parametry:** Balíčky R určené k instalaci. Například `bitops stringr arules`.

   * Zaškrtněte políčko **zachovat tuto akci skriptu**.  

   > [!NOTE]
   > 1. Ve výchozím nastavení nejsou nainstalovány všechny balíčky R ze snímku konzistentní s verzí serveru ML, která byla nainstalována aplikace Microsoft MRAN úložiště. Pokud chcete nainstalovat novější verze balíčků, vystavujete se riziku nekompatibility. Tento typ instalace však můžete provést zadáním `useCRAN` jako prvního prvku seznamu balíčků, například `useCRAN bitops, stringr, arules`.  
   > 2. Některé balíčky R vyžadují další linuxové systémové knihovny. Pro usnadnění práce služby HDInsight ML obsahuje předem nainstalovaná závislosti potřebné balíčky R nejoblíbenější nejvyšší 100. Pokud ale balíčky R, které instalujete, vyžadují ještě další knihovny, budete si muset stáhnout základní skript, které zde používáme, a přidat další kroky k instalaci příslušných systémových knihoven. Upravený skript je pak nutné odeslat do veřejného kontejneru objektů blob v úložišti Azure, a k instalaci balíčků použít upravený skript.
   >    Další informace o vývoji akcí skriptů najdete v tématu [Vývoj akcí skriptů](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Přidání akce skriptu](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Vyberte **Vytvořit** a spusťte skript. Po dokončení skriptu jsou balíčky R k dispozici na všech pracovních uzlech.

## <a name="next-steps"></a>Další postup

* [Zprovoznění služby ML clusteru v HDInsight](r-server-operationalize.md)
* [Výpočetní kontextu možnosti pro cluster ML Service v HDInsight](r-server-compute-contexts.md)
* [Azure možnosti úložiště pro cluster služby ML v HDInsight](r-server-storage.md)
