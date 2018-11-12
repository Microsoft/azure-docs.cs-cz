---
title: Správa služby ML clusteru v HDInsight – Azure
description: Další informace o správě clusteru služby ML služby v Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 35b80223552181e44beac011f5fb541158466acc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255395"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Správa služby ML clusteru v Azure HDInsight

V tomto článku se dozvíte, jak spravovat existující cluster v Azure HDInsight, provádět úkoly, jako je přidání mulitiple souběžných uživatelů, vzdálené připojení ke clusteru služby ML, změna výpočetní kontext a další služby ML.

## <a name="prerequisites"></a>Požadavky

* **Cluster služby ML v HDInsight**: pokyny najdete v tématu [začít pracovat se službami ML na HDInsight](r-server-get-started.md).

* **Klient Secure Shell (SSH):** Klient SSH slouží k vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo v clusteru. Další informace najdete v tématu [použití SSH se službou HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Povolení několika souběžných uživatelů

Více souběžných uživatelů pro služby ML clusteru v HDInsight můžete povolit přidáním dalších uživatelů pro hraniční uzel, na kterém běží komunitní verze Rstudia. Při vytváření clusteru HDInsight musíte zadat dva uživatele – uživatele HTTP a uživatele SSH:

![Souběžný uživatel 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Uživatelské jméno přihlášení clusteru:** Uživatel HTTP pro ověřování prostřednictvím brány HDInsight, která slouží k ochraně vytvořených clusterů HDInsight. Tento uživatel HTTP slouží k přístupu k uživatelskému rozhraní Ambari, uživatelskému rozhraní YARN a dalším komponentám uživatelského rozhraní.
- **Uživatelské jméno Secure Shell (SSH):** Uživatel SSH sloužící k přístupu ke clusteru přes Secure Shell. Tento uživatel je uživatel v systému Linux pro všechny hlavní uzly, pracovní uzly a hraniční uzly. Proto můžete použít Secure Shell pro přístup k jakémukoli uzlu ve vzdáleném clusteru.

R Studio Server Community verze použitá v clusteru služby ML v HDInsight přijímá jako přihlašovací mechanismus pouze Linuxové uživatelské jméno a heslo. Nepodporuje předávání tokenů. Ano při pokusu o přístup k R Studio poprvé v clusteru služby ML, musíte se přihlásit dvakrát.

- Nejdřív přihlásit pomocí přihlašovacích údajů uživatele HTTP prostřednictvím brány HDInsight. 

- Pak pomocí přihlašovacích údajů uživatele SSH přihlaste k Rstudiu.
  
V současné době je možné při zřizování clusteru HDInsight vytvořit pouze jeden uživatelský účet SSH. Pokud chcete povolit přístup k služby ML clusteru v HDInsight více uživatelům, proto musíte vytvořit další uživatele v systému Linux.

Protože RStudio běží na hraničním uzlu clusteru, existuje několik kroků:

1. Přihlaste se k hraničnímu uzlu pomocí existujícího uživatele SSH
2. Přidání dalších uživatelů Linuxu na hraničním uzlu
3. Použití komunitní verze RStudia s vytvořeným uživatelem

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Krok 1: Přihlášení k hraničnímu uzlu pomocí vytvořeného uživatele SSH

Postupujte podle pokynů na adrese [připojení k HDInsight (Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) pro přístup k hraničnímu uzlu. Adresa hraničního uzlu pro cluster služby ML na HDInsight je `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Krok 2: Přidání dalších uživatelů Linuxu na hraničním uzlu

Pokud chcete přidat uživatele na hraničním uzlu, spusťte následující příkazy:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

Následující snímek obrazovky ukazuje výstupy.

![Souběžný uživatel 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Po zobrazení výzvy k "aktuální heslo protokolu Kerberos:", stačí stisknout kombinaci kláves **Enter** ignorujte. Možnost `-m` v příkazu `useradd` označuje, že systém pro uživatele vytvoří domovskou složku, kterou vyžaduje komunitní verze RStudia.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Krok 3: Použití komunitní verze RStudia s vytvořeným uživatelem

Přístup k přihlašovací stránce RStudio z https://CLUSTERNAME.azurehdinsight.net/rstudio/. Pokud jsou přihlášení poprvé po vytvoření clusteru, zadejte přihlašovací údaje Správce clusteru, za nímž následuje přihlašovacích údajů uživatele SSH, který jste vytvořili. Pokud toto není vaše první přihlášení, zadejte pouze přihlašovací údaje uživatele SSH, který jste vytvořili.

Můžete se také přihlásit pomocí původních přihlašovacích údajů (ve výchozím nastavení je to *sshuser*) současně z jiného okna prohlížeče.

Všimněte si také, že nově přidaní uživatelé nemají v systému Linux kořenová oprávnění, ale mají stejný přístup ke všem souborům ve vzdáleném úložišti HDFS a WASB.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Vzdálené připojení ke službám Microsoft ML

Můžete nastavit přístup k výpočetním kontextu Spark v HDInsight ze vzdálené instance ML klienta spuštěná na ploše. Uděláte to tak, je nutné zadat možnosti (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches a sshProfileScript) při definování RxSpark výpočetním kontextu na ploše: Příklad:

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

Další informace najdete v části "Použití Microsoft Machine Learning Server jako klienta Hadoop" v [použití RevoScaleR ve výpočetním kontextu Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

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

2. V dalším kroku některé informace o datech a nadefinujeme dva zdroje dat.

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

3. Spusťte logistickou regresi nad daty v místním výpočetním kontextu.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Byste měli vidět výstup, který končí řádky podobný následujícímu fragmentu kódu:

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

4. Spusťte stejnou logistickou regresi využívající kontext Spark. Kontext Spark distribuuje zpracování do všech pracovních uzlů v clusteru HDInsight.

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


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuování kódu R do více uzlů

Pomocí služby ML na HDInsight, můžete vzít existující kód R a spustit ho napříč několika uzly v clusteru pomocí příkazu `rxExec`. Tato funkce je užitečná při uklízení parametrů nebo provádění simulací. Následující kód je příklad použití příkazu `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Pokud stále používáte kontext Spark, tento příkaz vrátí hodnoty nodename pracovních uzlů, který kód `(Sys.info()["nodename"])` je spouštět. Například v clusteru se čtyřmi uzly, které by se měl zobrazit výstup podobný následujícímu fragmentu kódu:

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

Služby ML HDInsight umožňuje přímý přístup k datům v Hive a Parquet pro použití ve funkcích scaler ve výpočetním kontextu Spark. Tyto možnosti jsou dostupné prostřednictvím nových funkcí zdroje dat ScaleR s názvem RxHiveData a RxParquetData. Funkce pomocí Spark SQL načtou data přímo do struktury DataFrame ve Sparku, aby je ScaleR mohl analyzovat.

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


Další informace o používání těchto nových funkcí najdete v online nápovědě služby ML pomocí `?RxHivedata` a `?RxParquetData` příkazy.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Instalace dalších balíčků R na clusteru

### <a name="to-install-r-packages-on-the-edge-node"></a>Instalace balíčků R na hraničním uzlu

Pokud chcete k instalaci dalších balíčků R na hraničním uzlu, můžete použít `install.packages()` přímo v rámci konzoly R po připojení k hraničnímu uzlu pomocí SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Balíčky R nainstalovat na pracovním uzlu

Balíčky R nainstalovat na pracovní uzly clusteru, musíte použít akci skriptu. Akce skriptů jsou skripty Bash, které se používají k provádění změn konfigurace clusteru HDInsight nebo k instalaci dalšího softwaru, jako jsou například další balíčky R. 

> [!IMPORTANT]
> Instalace dalších balíčků R pomocí akcí skriptů je možná jedině po vytvoření clusteru. Nepoužívejte tento postup během vytváření clusteru, protože skript se spoléhá na zcela konfiguraci služby ML.
>
>

1. Postupujte podle kroků uvedených v [přizpůsobení clusterů pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

3. Pro **odeslat akci skriptu**, zadejte následující informace:

   * Pro **typ skriptu**vyberte **vlastní**.

   * Pro **název**, zadejte název akce skriptu.

    * Pro **URI skriptu Bash**, zadejte `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Toto je skript, který se nainstaluje na pracovní uzel další balíčky r.

   * Vyberte zaškrtávací pole pouze pro **pracovního procesu**.

   * **Parametry:** Balíčky R určené k instalaci. Například `bitops stringr arules`.

   * Zaškrtněte políčko pro **zachovat tuto akci se skripty**.  

   > [!NOTE]
   > 1. Ve výchozím nastavení všechny balíčky R instalují ze snímku úložiště Microsoft MRAN, který je konzistentní s verzí ML Server, která byla nainstalována. Pokud chcete nainstalovat novější verze balíčků, vystavujete se riziku nekompatibility. Tento typ instalace však můžete provést zadáním `useCRAN` jako prvního prvku seznamu balíčků, například `useCRAN bitops, stringr, arules`.  
   > 2. Některé balíčky R vyžadují další linuxové systémové knihovny. Pro usnadnění práce služby ML HDInsight dodává s předinstalovanou s nejvyšší 100 nejoblíbenějších balíčků r. potřebné závislosti. Pokud ale balíčky R, které instalujete, vyžadují ještě další knihovny, budete si muset stáhnout základní skript, které zde používáme, a přidat další kroky k instalaci příslušných systémových knihoven. Upravený skript je pak nutné odeslat do veřejného kontejneru objektů blob v úložišti Azure, a k instalaci balíčků použít upravený skript.
   >    Další informace o vývoji akcí skriptů najdete v tématu [Vývoj akcí skriptů](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Přidání akce skriptu](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Vyberte **Vytvořit** a spusťte skript. Po dokončení skriptu jsou balíčky R k dispozici na všech pracovních uzlech.

## <a name="next-steps"></a>Další postup

* [Zprovoznění clusteru služby ML Services v HDInsight](r-server-operationalize.md)
* [Možnosti výpočetního kontextu pro cluster služby ML v HDInsight](r-server-compute-contexts.md)
* [Možnosti Azure Storage pro cluster služby ML Services v HDInsight](r-server-storage.md)
