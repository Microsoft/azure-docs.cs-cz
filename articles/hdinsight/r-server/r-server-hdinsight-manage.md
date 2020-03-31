---
title: Správa clusteru služeb ML na HDInsight – Azure
description: Zjistěte, jak spravovat různé úkoly v clusteru ML Services v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: b2c16c27c0dfc0c30a99c52544cc4d2278eadfc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647726"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Správa clusteru služeb ML na Azure HDInsight

V tomto článku se dozvíte, jak spravovat existující cluster služeb ML v Azure HDInsight provádět úlohy, jako je přidání více souběžných uživatelů, vzdálené připojení k clusteru služby ML, změna výpočetního kontextu atd.

## <a name="prerequisites"></a>Požadavky

* Cluster služeb ML na hdinsightu. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte **služby ML** pro typ **clusteru**.

* Klient Secure Shell (SSH): Klient SSH slouží k vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo v clusteru. Další informace naleznete [v tématu Použití SSH s HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="enable-multiple-concurrent-users"></a>Povolení několika souběžných uživatelů

Můžete povolit více souběžných uživatelů pro cluster ML Services na HDInsight přidáním více uživatelů pro hraniční uzel, na kterém je spuštěna verze komunity RStudio. Při vytváření clusteru HDInsight musíte zadat dva uživatele – uživatele HTTP a uživatele SSH:

![Parametry přihlášení na portál HDI Azure](./media/r-server-hdinsight-manage/hdi-concurrent-users1.png)

- **Uživatelské jméno přihlášení clusteru:** Uživatel HTTP pro ověřování prostřednictvím brány HDInsight, která slouží k ochraně vytvořených clusterů HDInsight. Tento uživatel HTTP se používá pro přístup k uživatelskému rozhraní Apache Ambari, Uživatelské rozhraní Apache Hadoop YARN a další součásti uživatelského rozhraní.
- **Uživatelské jméno Secure Shell (SSH):** Uživatel SSH sloužící k přístupu ke clusteru přes Secure Shell. Tento uživatel je uživatel v systému Linux pro všechny hlavní uzly, pracovní uzly a hraniční uzly. Proto můžete použít Secure Shell pro přístup k jakémukoli uzlu ve vzdáleném clusteru.

Verze komunity serveru R Studio, která se používá v clusteru SLUŽBY ML na serveru HDInsight, přijímá jako přihlašovací mechanismus pouze uživatelské jméno a heslo pro Linux. Nepodporuje předávání tokenů. Takže při pokusu o přístup k R Studio poprvé v clusteru služby ML, je třeba se přihlásit dvakrát.

- Nejprve se přihlaste pomocí přihlašovacích údajů uživatele HTTP prostřednictvím brány HDInsight.

- Potom použijte přihlašovací údaje uživatele SSH pro přihlášení k RStudio.
  
V současné době je možné při zřizování clusteru HDInsight vytvořit pouze jeden uživatelský účet SSH. Chcete-li umožnit více uživatelům přístup k clusteru ML Services na HDInsight, musíte vytvořit další uživatele v systému Linux.

Vzhledem k tomu, že RStudio běží na hraničním uzlu clusteru, existuje několik kroků:

1. Přihlášení k hraničnímu uzlu pomocí existujícího uživatele SSH
2. Přidání dalších uživatelů Linuxu na hraničním uzlu
3. Použití komunitní verze RStudia s vytvořeným uživatelem

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Krok 1: Přihlášení k hraničnímu uzlu pomocí vytvořeného uživatele SSH

Postupujte podle pokynů na [Connect to HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) pro přístup k hraničnímu uzlu. Adresa hraničního uzlu pro cluster ml `CLUSTERNAME-ed-ssh.azurehdinsight.net`services na HDInsight je .

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Krok 2: Přidání dalších uživatelů Linuxu na hraničním uzlu

Pokud chcete přidat uživatele na hraničním uzlu, spusťte následující příkazy:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

Následující snímek obrazovky ukazuje výstupy.

![screenshot výstup souběžných uživatelů](./media/r-server-hdinsight-manage/hdi-concurrent-users2.png)

Po zobrazení výzvy k zadání aktuálního hesla protokolu Kerberos stačí jej ignorovat stisknutím **klávesy Enter.** Možnost `-m` v příkazu `useradd` označuje, že systém pro uživatele vytvoří domovskou složku, kterou vyžaduje komunitní verze RStudia.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Krok 3: Použití komunitní verze RStudia s vytvořeným uživatelem

Přístup k `https://CLUSTERNAME.azurehdinsight.net/rstudio/`RStudiu z aplikace . Pokud se po prvním přihlášení po vytvoření clusteru přihlašujete, zadejte pověření správce clusteru následovaná uživatelskými pověřeními SSH, která jste vytvořili. Pokud se nejedná o vaše první přihlášení, zadejte pouze přihlašovací údaje pro uživatele SSH, který jste vytvořili.

Můžete se také přihlásit pomocí původních přihlašovacích údajů (ve výchozím nastavení je *sshuser*) současně z jiného okna prohlížeče.

Všimněte si také, že nově přidaní uživatelé nemají v systému Linux kořenová oprávnění, ale mají stejný přístup ke všem souborům ve vzdáleném úložišti HDFS a WASB.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Vzdálené připojení ke službám Microsoft ML Services

Přístup k výpočetnímu kontextu HDInsight Spark můžete nastavit ze vzdálené instance klienta ML spuštěného na ploše. Chcete-li tak učinit, musíte zadat možnosti (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches a sshProfileScript) při definování výpočetního kontextu RxSpark na ploše: Například:

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

Další informace naleznete v části Použití serveru Microsoft Machine Learning Server jako klienta Apache Hadoop v části [Jak používat RevoScaleR v výpočetním kontextu Apache Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Použití výpočetního kontextu

Výpočetní kontext vám umožňuje řídit, jestli se výpočty provádějí místně na hraničním uzlu, nebo jsou distribuovány napříč uzly v clusteru HDInsight.  Příklad nastavení výpočetního kontextu pomocí serveru RStudio najdete [v tématu Spuštění skriptu R v clusteru služeb ML v Azure HDInsight pomocí serveru RStudio](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuování kódu R do více uzlů

Pomocí služby ML Services na HDInsight můžete použít existující kód R a `rxExec`spustit jej na více uzlech v clusteru . Tato funkce je užitečná při uklízení parametrů nebo provádění simulací. Následující kód je příklad použití příkazu `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Pokud stále používáte kontext Spark, tento příkaz vrátí hodnotu názvu pro `(Sys.info()["nodename"])` pracovní uzly, na kterých je kód spuštěn. Například v clusteru se čtyřmi uzly očekáváte příjem výstupu podobného následujícímu fragmentu:

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

HdInsight ML Services umožňuje přímý přístup k datům v Hive a parketách pro použití funkcemi ScaleR v kontextu výpočetních zařízení Spark. Tyto možnosti jsou dostupné prostřednictvím nových funkcí zdroje dat ScaleR s názvem RxHiveData a RxParquetData. Funkce pomocí Spark SQL načtou data přímo do struktury DataFrame ve Sparku, aby je ScaleR mohl analyzovat.

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


Další informace o používání těchto nových funkcí naleznete v online `?RxHivedata` nápovědě ve službách ML pomocí příkazů a. `?RxParquetData`  

## <a name="install-additional-r-packages-on-the-cluster"></a>Instalace dalších balíčků R do clusteru

### <a name="to-install-r-packages-on-the-edge-node"></a>Instalace balíčků R na hraniční uzel

Pokud chcete nainstalovat další balíčky R na hraniční `install.packages()` uzel, můžete použít přímo z konzoly R, po připojení k hraničnímu uzlu prostřednictvím SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Instalace balíčků R v pracovním uzlu

Chcete-li nainstalovat balíčky R do pracovních uzlů clusteru, musíte použít akci skriptu. Akce skriptů jsou skripty Bash, které se používají k provádění změn konfigurace clusteru HDInsight nebo k instalaci dalšího softwaru, jako jsou například další balíčky R. 

> [!IMPORTANT]  
> Instalace dalších balíčků R pomocí akcí skriptů je možná jedině po vytvoření clusteru. Nepoužívejte tento postup při vytváření clusteru, protože skript závisí na úplné konfiguraci služby ML Services.

1. Postupujte podle pokynů v [části Přizpůsobit clustery pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

3. V **případě akce Odeslat skript**zadejte následující informace:

   * V **pole Typ skriptu**vyberte **možnost Vlastní**.

   * V **části Název**zadejte název akce skriptu.

     * Do **souboru URI skriptu Bash**zadejte `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Toto je skript, který nainstaluje další balíčky R na pracovní uzel

   * Zaškrtněte políčko pouze pro **pracovníka**.

   * **Parametry:** Balíčky R určené k instalaci. Například `bitops stringr arules`.

   * Zaškrtněte políčko **Zachovat tuto akci skriptu**.  

   > [!NOTE]
   > 1. Ve výchozím nastavení jsou všechny balíčky R nainstalovány ze snímku úložiště MRAN společnosti Microsoft, který je konzistentní s nainstalovanou verzí serveru ML. Pokud chcete nainstalovat novější verze balíčků, vystavujete se riziku nekompatibility. Tento typ instalace však můžete provést zadáním `useCRAN` jako prvního prvku seznamu balíčků, například `useCRAN bitops, stringr, arules`.  
   > 2. Některé balíčky R vyžadují další linuxové systémové knihovny. Pro větší pohodlí je služba HDInsight ML Services předinstalována se závislostmi, které jsou potřeba v prvních 100 nejoblíbenějších balíčcích R. Pokud ale balíčky R, které instalujete, vyžadují ještě další knihovny, budete si muset stáhnout základní skript, které zde používáme, a přidat další kroky k instalaci příslušných systémových knihoven. Upravený skript je pak nutné odeslat do veřejného kontejneru objektů blob v úložišti Azure, a k instalaci balíčků použít upravený skript.
   >    Další informace o vývoji akcí skriptů najdete v tématu [Vývoj akcí skriptů](../hdinsight-hadoop-script-actions-linux.md).

   ![Akce odeslat skript na Portálu Azure](./media/r-server-hdinsight-manage/submit-script-action.png)

4. Vyberte **Vytvořit** a spusťte skript. Po dokončení skriptu jsou balíčky R k dispozici na všech pracovních uzlech.

## <a name="next-steps"></a>Další kroky

* [Zprovoznění clusteru služby ML Services v HDInsight](r-server-operationalize.md)
* [Možnosti výpočetního kontextu pro cluster služby ML na hdinsightu](r-server-compute-contexts.md)
* [Možnosti Azure Storage pro cluster služby ML Services v HDInsight](r-server-storage.md)
