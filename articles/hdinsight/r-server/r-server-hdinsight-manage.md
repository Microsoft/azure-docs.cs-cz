---
title: Spravovat cluster R serverem v HDInsight - Azure | Microsoft Docs
description: Zjistěte, jak ke správě clusteru služby R Server v Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 827bcb7bb20f1def9acec8cb2043ea295801583a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="manage-r-server-cluster-on-azure-hdinsight"></a>Spravovat cluster R serverem v Azure HDInsight

V tomto článku zjistěte, jak spravovat existující cluster R serverem v Azure HDInsight k provedení úlohy, jako je přidání mulitiple souběžných uživatelů, vzdáleném připojení serveru R (Microsoft ML serveru) nebo klienta, změna kontextu výpočetní atd.

## <a name="prerequisites"></a>Požadavky

* **Cluster služby R serverem v HDInsight**: pokyny najdete v tématu [začít pracovat s R serverem v HDInsight](r-server-get-started.md).

* **Klient Secure Shell (SSH):** Klient SSH slouží k vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo v clusteru. Další informace najdete v tématu [použití SSH s HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Povolení několika souběžných uživatelů

Přidáním více uživatelů pro hraniční uzel, na kterém běží verze Rstudia komunity můžete povolit více souběžných uživatelů pro cluster R serverem v HDInsight. Při vytváření clusteru HDInsight musíte zadat dva uživatele – uživatele HTTP a uživatele SSH:

![Souběžný uživatel 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Uživatelské jméno přihlášení clusteru:** Uživatel HTTP pro ověřování prostřednictvím brány HDInsight, která slouží k ochraně vytvořených clusterů HDInsight. Tento uživatel HTTP slouží k přístupu k uživatelskému rozhraní Ambari, uživatelskému rozhraní YARN a dalším komponentám uživatelského rozhraní.
- **Uživatelské jméno Secure Shell (SSH):** Uživatel SSH sloužící k přístupu ke clusteru přes Secure Shell. Tento uživatel je uživatel v systému Linux pro všechny hlavní uzly, pracovní uzly a hraniční uzly. Proto můžete použít Secure Shell pro přístup k jakémukoli uzlu ve vzdáleném clusteru.

Verze komunity serveru R Studio použitá v cluster R serverem v HDInsight akceptuje pouze Linux uživatelské jméno a heslo jako mechanismus přihlášení. Nepodporuje předávání tokenů. Ano při pokusu o přístup k R Studio poprvé v clusteru R Server, budete muset přihlásit dvakrát.

- První protokol pomocí pověření uživatele HTTP přes bránu HDInsight. 

- Pak použijte přihlašovací údaje uživatele SSH k přihlášení do Rstudia.
  
V současné době je možné při zřizování clusteru HDInsight vytvořit pouze jeden uživatelský účet SSH. Povolit více uživatelům přístup k cluster R serverem v HDInsight, takže musíte vytvořit další uživatele v systému Linux.

Protože Rstudia běží na clusteru hraniční uzel, existují zde několik kroků:

1. Použití stávajícího uživatele SSH k přihlášení k uzlu edge
2. Přidání dalších uživatelů Linuxu na hraničním uzlu
3. Použití komunitní verze RStudia s vytvořeným uživatelem

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Krok 1: Přihlášení k hraničnímu uzlu pomocí vytvořeného uživatele SSH

Postupujte podle pokynů v [připojení k HDInsight (Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) pro přístup k uzlu edge. Adresa uzlu edge pro cluster R serverem v HDInsight je `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

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

Přístup k Rstudia z https://CLUSTERNAME.azurehdinsight.net/rstudio/. Pokud jste se přihlašují poprvé po vytvoření clusteru, zadejte přihlašovací údaje Správce clusteru, za nímž následuje přihlašovací údaje uživatele SSH, který jste právě vytvořili. Pokud to není prvním přihlášení, zadejte přihlašovací údaje pouze pro uživatele SSH, kterou jste vytvořili.

Souběžně se můžete z jiného okna prohlížeče přihlásit také pomocí původních přihlašovacích údajů (ve výchozím nastavení to je *sshuser*).

Všimněte si také, že nově přidaní uživatelé nemají v systému Linux kořenová oprávnění, ale mají stejný přístup ke všem souborům ve vzdáleném úložišti HDFS a WASB.

## <a name="connect-remotely-to-microsoft-ml-server-or-client"></a>Vzdálené připojení k serveru Microsoft ML nebo klienta

Ze vzdálené instance systému Microsoft ML Server nebo Microsoft ML Client spuštěná na ploše můžete nastavit přístup do kontextu výpočtů HDInsight Hadoop Spark. Chcete-li to provést, musíte zadat možnosti (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches a sshProfileScript) při definování RxSpark výpočetní kontext na pracovní ploše: Příklad:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
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

Další informace najdete v části "Použití R jako Hadoop klienta systému Microsoft Server" v [vytváření kontextu výpočetní pro Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

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

2. V dalším kroku vytvořit některé informace data a definovat dvou zdrojů dat, aby jsme můžete pracovat s daty.

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

    Zobrazený výstup by měl končit řádky, které vypadají přibližně takto:

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
   > Distribuovat výpočty napříč uzly clusteru můžete také pomocí MapReduce. Další informace o výpočetním kontextu najdete v tématu [Možnosti výpočetního kontextu pro R Server ve službě HDInsight](r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuování kódu R do více uzlů

S R serverem v HDInsight, můžete využít existující kód R a spusťte napříč několika uzly v clusteru pomocí `rxExec`. Tato funkce je užitečná při uklízení parametrů nebo provádění simulací. Následující kód je příklad použití příkazu `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Pokud stále používáte kontext Spark nebo MapReduce, tento příkaz vrátí hodnoty nodename (název uzlu) všech pracovních uzlů, ve kterých je spuštěný kód `(Sys.info()["nodename"])`. Například v clusteru se čtyřmi uzly by se měl zobrazit výstup podobný následujícímu:

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"


## <a name="access-data-in-hive-and-parquet"></a>Přístup k datům v Hive a Parquet

Funkce, která je k dispozici v R Serveru 9.1, umožňuje přímý přístup k datům v Hive a Parquet pro použití ve funkcích ScaleR ve výpočetním kontextu Spark. Tyto možnosti jsou dostupné prostřednictvím nových funkcí zdroje dat ScaleR s názvem RxHiveData a RxParquetData. Funkce pomocí Spark SQL načtou data přímo do struktury DataFrame ve Sparku, aby je ScaleR mohl analyzovat.  

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


Další informace o těchto nových funkcí naleznete v online nápovědě ve ML serveru prostřednictvím použití `?RxHivedata` a `?RxParquetData` příkazy.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Nainstalujte další balíčky R na clusteru

### <a name="to-install-r-packages-on-the-edge-node"></a>K instalaci balíčků R na uzlu edge

Pokud chcete nainstalovat další balíčky R na uzlu edge, můžete použít `install.packages()` přímo z v konzole R po připojení k uzlu edge prostřednictvím SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>K instalaci balíčků R v pracovním uzlu

Chcete-li instalovat balíčky R na pracovních uzlech clusteru, musíte použít akci skriptu. Akce skriptů jsou skripty Bash, které se používají k provádění změn konfigurace clusteru HDInsight nebo k instalaci dalšího softwaru, jako jsou například další balíčky R. 

> [!IMPORTANT]
> Instalace dalších balíčků R pomocí akcí skriptů je možná jedině po vytvoření clusteru. Nepoužívejte tento postup během vytváření clusteru, protože skript se spoléhá na úplnou instalaci a konfiguraci R Serveru.
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
   > 1. Ve výchozím nastavení se všechny balíčky R instalují ze snímku úložiště Microsoft MRAN, který je konzistentní s nainstalovanou verzí R Serveru. Pokud chcete nainstalovat novější verze balíčků, vystavujete se riziku nekompatibility. Tento typ instalace však můžete provést zadáním `useCRAN` jako prvního prvku seznamu balíčků, například `useCRAN bitops, stringr, arules`.  
   > 2. Některé balíčky R vyžadují další linuxové systémové knihovny. Pro usnadnění práce jsme předinstalovali potřebné závislosti pro 100 nejoblíbenějších balíčků R. Pokud ale balíčky R, které instalujete, vyžadují ještě další knihovny, budete si muset stáhnout základní skript, které zde používáme, a přidat další kroky k instalaci příslušných systémových knihoven. Upravený skript je pak nutné odeslat do veřejného kontejneru objektů blob v úložišti Azure, a k instalaci balíčků použít upravený skript.
   >    Další informace o vývoji akcí skriptů najdete v tématu [Vývoj akcí skriptů](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Přidání akce skriptu](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Vyberte **Vytvořit** a spusťte skript. Po dokončení skriptu jsou balíčky R k dispozici na všech pracovních uzlech.

## <a name="next-steps"></a>Další postup

* [Zprovoznění clusteru R Serveru v HDInsight](r-server-operationalize.md)
* [Možnosti výpočetního kontextu pro cluster R Serveru v HDInsight](r-server-compute-contexts.md)
* [Možnosti služby Azure Storage pro cluster R Serveru v HDInsight](r-server-storage.md)
