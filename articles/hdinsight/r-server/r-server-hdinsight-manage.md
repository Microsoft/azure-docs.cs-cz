---
title: Správa služby ML clusteru v HDInsight – Azure
description: Další informace o správě clusteru služby ML služby v Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: fa838f371607f3c0b0f76f81d6755c842a5901f7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448963"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Správa služby ML clusteru v Azure HDInsight

V tomto článku se dozvíte, jak spravovat existující cluster v Azure HDInsight, provádět úkoly, jako je přidání několika souběžných uživatelů, vzdálené připojení ke clusteru služby ML, změna výpočetní kontext a další služby ML.

## <a name="prerequisites"></a>Požadavky

* Cluster služby ML v HDInsight. Zobrazit [vytvořit Apache Hadoop clusterů pomocí webu Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **služby ML** pro **typ clusteru**.


* Klient Secure Shell (SSH): Klient SSH slouží ke vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo na clusteru. Další informace najdete v tématu [použití SSH se službou HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Povolení několika souběžných uživatelů

Více souběžných uživatelů pro služby ML clusteru v HDInsight můžete povolit přidáním dalších uživatelů pro hraniční uzel, na kterém běží komunitní verze Rstudia. Při vytváření clusteru HDInsight musíte zadat dva uživatele – uživatele HTTP a uživatele SSH:

![Souběžný uživatel 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Uživatelské jméno přihlášení clusteru:** Uživatel HTTP pro ověřování prostřednictvím brány HDInsight, která slouží k ochraně vytvořených clusterů HDInsight. Tento uživatel HTTP slouží k přístupu k rozhraní Apache Ambari, uživatelskému rozhraní YARN Apache Hadoop, jakož i dalším komponentám uživatelského rozhraní.
- **Uživatelské jméno Secure Shell (SSH):** Uživatel SSH sloužící k přístupu ke clusteru přes Secure Shell. Tento uživatel je uživatel v systému Linux pro všechny hlavní uzly, pracovní uzly a hraniční uzly. Proto můžete použít Secure Shell pro přístup k jakémukoli uzlu ve vzdáleném clusteru.

R Studio Server Community verze použitá v clusteru služby ML v HDInsight přijímá jako přihlašovací mechanismus pouze Linuxové uživatelské jméno a heslo. Nepodporuje předávání tokenů. Ano při pokusu o přístup k R Studio poprvé v clusteru služby ML, musíte se přihlásit dvakrát.

- Nejdřív přihlásit pomocí přihlašovacích údajů uživatele HTTP prostřednictvím brány HDInsight. 

- Pak pomocí přihlašovacích údajů uživatele SSH přihlaste k Rstudiu.
  
V současné době je možné při zřizování clusteru HDInsight vytvořit pouze jeden uživatelský účet SSH. Pokud chcete povolit přístup k služby ML clusteru v HDInsight více uživatelům, proto musíte vytvořit další uživatele v systému Linux.

Protože RStudio běží na hraničním uzlu clusteru, existuje několik kroků:

1. Přihlaste se k hraničnímu uzlu pomocí existujícího uživatele SSH
2. Přidání dalších uživatelů Linuxu na hraničním uzlu
3. Použití komunitní verze RStudia s vytvořeným uživatelem

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Krok 1: Přihlaste se k hraničnímu uzlu pomocí vytvořeného uživatele SSH

Postupujte podle pokynů na adrese [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) pro přístup k hraničnímu uzlu. Adresa hraničního uzlu pro cluster služby ML na HDInsight je `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

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

Můžete nastavit přístup k výpočetním kontextu Spark v HDInsight ze vzdálené instance ML klienta spuštěná na ploše. Uděláte to tak, je nutné zadat možnosti (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches a sshProfileScript) při definování RxSpark výpočetním kontextu na vašem počítači: Příklad:

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

Další informace najdete v části "Pomocí Microsoft Machine Learning Server jako Apache klienta Hadoop" v [použití RevoScaleR ve výpočetním kontextu Apache Sparku](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Použití výpočetního kontextu

Výpočetní kontext vám umožňuje řídit, jestli se výpočty provádějí místně na hraničním uzlu, nebo jsou distribuovány napříč uzly v clusteru HDInsight.  Příklad nastavení výpočetního kontextu s prostředím RStudio serveru najdete v tématu [spustit skript R v clusteru služby ML v Azure HDInsight pomocí přihlašovací stránce RStudio serveru](machine-learning-services-quickstart-job-rstudio.md).

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

## <a name="access-data-in-apache-hive-and-parquet"></a>Přístup k datům v Apache Hive a Parquet

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

1. Postupujte podle kroků uvedených v [přizpůsobení clusterů pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

3. Pro **odeslat akci skriptu**, zadejte následující informace:

   * Pro **typ skriptu**vyberte **vlastní**.

   * Pro **název**, zadejte název akce skriptu.

     * Pro **URI skriptu Bash**, zadejte `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Toto je skript, který se nainstaluje na pracovní uzel další balíčky r.

   * Vyberte zaškrtávací pole pouze pro **pracovního procesu**.

   * **Parametry**: Balíčky R k instalaci. Například `bitops stringr arules`.

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
