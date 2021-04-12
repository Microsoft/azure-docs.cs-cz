---
title: Správa clusteru služeb ML v HDInsight – Azure
description: Naučte se spravovat různé úlohy v clusteru služby ML Services ve službě Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/19/2019
ms.openlocfilehash: a5e623b0429194db6d03beb674679bd10e337844
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869465"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Správa clusteru služeb ML v Azure HDInsight

V tomto článku se dozvíte, jak spravovat existující cluster služeb ML v Azure HDInsight, aby se prováděly úlohy, jako je přidání více souběžných uživatelů, vzdálené připojení ke clusteru služeb ML, změna výpočetního kontextu atd.

## <a name="prerequisites"></a>Požadavky

* Cluster služeb ML v HDInsight. Přečtěte si téma [vytvoření Apache Hadoop clusterů pomocí Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) a výběr **služeb ml** pro **typ clusteru**.

* Klient Secure Shell (SSH): Klient SSH slouží k vzdálenému připojení ke clusteru HDInsight a spouštění příkazů přímo v clusteru. Další informace najdete v tématu [Použití SSH se službou HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="enable-multiple-concurrent-users"></a>Povolení několika souběžných uživatelů

Můžete povolit více souběžných uživatelů pro cluster služeb ML v HDInsight přidáním dalších uživatelů pro hraniční uzel, na kterém je spuštěná verze komunity RStudio. Při vytváření clusteru HDInsight musíte zadat dva uživatele – uživatele HTTP a uživatele SSH:

:::image type="content" source="./media/r-server-hdinsight-manage/hdi-concurrent-users1.png" alt-text="Parametry HDI Azure Portal přihlašovacích údajů" border="true":::

- **Uživatelské jméno přihlášení clusteru:** Uživatel HTTP pro ověřování prostřednictvím brány HDInsight, která slouží k ochraně vytvořených clusterů HDInsight. Tento uživatel HTTP slouží k přístupu k uživatelskému rozhraní Apache Ambari, uživatelskému rozhraní Apache Hadoop nitě a dalším součástem uživatelského rozhraní.
- **Uživatelské jméno Secure Shell (SSH):** Uživatel SSH sloužící k přístupu ke clusteru přes Secure Shell. Tento uživatel je uživatel v systému Linux pro všechny hlavní uzly, pracovní uzly a hraniční uzly. Proto můžete použít Secure Shell pro přístup k jakémukoli uzlu ve vzdáleném clusteru.

Verze Community serveru R, která se používá v clusteru služby ML ve službě HDInsight, přijímá jako mechanismus přihlášení jenom uživatelské jméno a heslo pro Linux. Nepodporuje předávání tokenů. Takže při prvním pokusu o přístup k R Studio v clusteru služby ML Services se budete muset přihlásit dvakrát.

- Nejdřív se přihlaste pomocí přihlašovacích údajů uživatele HTTP prostřednictvím brány HDInsight.

- Pak se pomocí přihlašovacích údajů uživatele SSH přihlaste k RStudio.
  
V současné době je možné při zřizování clusteru HDInsight vytvořit pouze jeden uživatelský účet SSH. Pokud tedy chcete povolit více uživatelům přístup ke clusteru služeb ML v HDInsight, je nutné vytvořit další uživatele v systému Linux.

Vzhledem k tomu, že RStudio běží na hraničním uzlu clusteru, existuje několik kroků:

1. Přihlášení k hraničnímu uzlu pomocí stávajícího uživatele SSH
2. Přidání dalších uživatelů Linuxu na hraničním uzlu
3. Použití komunitní verze RStudia s vytvořeným uživatelem

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Krok 1: použití vytvořeného uživatele SSH k přihlášení k hraničnímu uzlu

Postupujte podle pokynů v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) pro přístup k hraničnímu uzlu. Adresa hraničního uzlu pro cluster služeb ML v HDInsight je `CLUSTERNAME-ed-ssh.azurehdinsight.net` .

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Krok 2: Přidání dalších uživatelů Linuxu na hraničním uzlu

Pokud chcete přidat uživatele na hraničním uzlu, spusťte následující příkazy:

```bash
# Add a user 
sudo useradd <yournewusername> -m

# Set password for the new user
sudo passwd <yournewusername>
```

Na následujícím snímku obrazovky vidíte výstupy.

:::image type="content" source="./media/r-server-hdinsight-manage/hdi-concurrent-users2.png" alt-text="počet souběžných uživatelů s výstupem obrazovky" border="true":::

Po zobrazení výzvy k zadání hesla "aktuální heslo protokolu Kerberos", stačí stisknout klávesu **ENTER** , aby se ignorovala. Možnost `-m` v příkazu `useradd` označuje, že systém pro uživatele vytvoří domovskou složku, kterou vyžaduje komunitní verze RStudia.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Krok 3: Použití komunitní verze RStudia s vytvořeným uživatelem

Přístup k RStudio z `https://CLUSTERNAME.azurehdinsight.net/rstudio/` . Pokud se přihlašujete poprvé po vytvoření clusteru, zadejte přihlašovací údaje Správce clusteru a přihlašovací údaje uživatele SSH, které jste vytvořili. Pokud se nejedná o vaše první přihlášení, zadejte pouze pověření pro uživatele SSH, kterého jste vytvořili.

Můžete se také přihlásit pomocí původních přihlašovacích údajů (ve výchozím nastavení je *sshuser*) souběžně z jiného okna prohlížeče.

Všimněte si také, že nově přidaní uživatelé nemají v systému Linux kořenová oprávnění, ale mají stejný přístup ke všem souborům ve vzdáleném úložišti HDFS a WASB.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Vzdálené připojení k Microsoft ML Services

Můžete nastavit přístup ke výpočetnímu kontextu HDInsight Spark ze vzdálené instance klienta ML běžící na ploše. K tomu je nutné zadat možnosti (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches a sshProfileScript) při definování výpočetního kontextu výpočetního rxspark na ploše: například:

```r
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
```

Další informace najdete v části "použití Microsoft Machine Learning Server jako klienta Apache Hadoop" v tématu [použití RevoScaleR ve výpočetním kontextu Apache Spark](/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) .

## <a name="use-a-compute-context"></a>Použití výpočetního kontextu

Výpočetní kontext vám umožňuje řídit, jestli se výpočty provádějí místně na hraničním uzlu, nebo jsou distribuovány napříč uzly v clusteru HDInsight.  Příklad nastavení výpočetního kontextu se serverem RStudio najdete v tématu [spuštění skriptu R v clusteru služby ml ve službě Azure HDInsight pomocí serveru RStudio](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuování kódu R do více uzlů

Pomocí služby ML Services v HDInsight můžete stávající kód R použít a spustit ho napříč několika uzly v clusteru pomocí `rxExec` . Tato funkce je užitečná při uklízení parametrů nebo provádění simulací. Následující kód je příklad použití příkazu `rxExec`:

```r
rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
```

Pokud stále používáte kontext Spark, tento příkaz vrátí hodnotu Node pro pracovní uzly, `(Sys.info()["nodename"])` na kterých je spuštěný kód. Například v clusteru se čtyřmi uzly očekáváte, že dostanete výstup podobný následujícímu fragmentu kódu:

```r
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
```

## <a name="access-data-in-apache-hive-and-parquet"></a>Přístup k datům v Apache Hive a Parquet

Služba HDInsight ML Services umožňuje přímý přístup k datům v podregistru a Parquet pro použití funkcemi škálování ve výpočetním kontextu Spark. Tyto možnosti jsou dostupné prostřednictvím nových funkcí zdroje dat ScaleR s názvem RxHiveData a RxParquetData. Funkce pomocí Spark SQL načtou data přímo do struktury DataFrame ve Sparku, aby je ScaleR mohl analyzovat.

Následuje vzorový kód pro použití těchto nových funkcí:

```r
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
```

Další informace o používání těchto nových funkcí najdete v online nápovědě služby ML prostřednictvím použití `?RxHivedata` `?RxParquetData` příkazů a.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Instalace dalších balíčků R na clusteru

### <a name="to-install-r-packages-on-the-edge-node"></a>Instalace balíčků R na hraničním uzlu

Pokud chcete na hraničním uzlu nainstalovat další balíčky R, můžete použít `install.packages()` přímo v konzole R, a to po připojení k hraničnímu uzlu přes SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Instalace balíčků jazyka R na pracovní uzel

Pokud chcete nainstalovat balíčky R na pracovní uzly clusteru, musíte použít akci skriptu. Akce skriptů jsou skripty Bash, které se používají k provádění změn konfigurace clusteru HDInsight nebo k instalaci dalšího softwaru, jako jsou například další balíčky R. 

> [!IMPORTANT]  
> Instalace dalších balíčků R pomocí akcí skriptů je možná jedině po vytvoření clusteru. Nepoužívejte tento postup během vytváření clusteru, protože skript spoléhá na zcela nakonfigurovanou službu ML.

1. Postupujte podle kroků v části [Přizpůsobení clusterů pomocí akce skriptu](../hdinsight-hadoop-customize-cluster-linux.md).

3. Pro **akci odeslání skriptu** zadejte následující informace:

   * Jako **typ skriptu** vyberte **vlastní**.

   * Do pole **název** zadejte název akce skriptu.

     * V případě **identifikátoru URI bash skriptu** zadejte  `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh` . Toto je skript, který do pracovního uzlu nainstaluje další balíčky R.

   * Zaškrtněte políčko pouze pro **pracovní proces**.

   * **Parametry:** Balíčky R určené k instalaci. Například `bitops stringr arules`.

   * Zaškrtněte políčko, pokud chcete **zachovat tuto akci skriptu**.  

   > [!NOTE]
   > 1. Ve výchozím nastavení jsou všechny balíčky R nainstalované ze snímku úložiště Microsoft MRAN, který je v souladu s nainstalovanou verzí ML Server. Pokud chcete nainstalovat novější verze balíčků, vystavujete se riziku nekompatibility. Tento typ instalace však můžete provést zadáním `useCRAN` jako prvního prvku seznamu balíčků, například `useCRAN bitops, stringr, arules`.  
   > 2. Některé balíčky R vyžadují další linuxové systémové knihovny. Pro usnadnění práce jsou služby HDInsight ML předem nainstalovány se závislostmi, které jsou potřebné pro nejčastější 100 nejoblíbenějších balíčků R. Pokud ale balíčky R, které instalujete, vyžadují ještě další knihovny, budete si muset stáhnout základní skript, které zde používáme, a přidat další kroky k instalaci příslušných systémových knihoven. Upravený skript je pak nutné odeslat do veřejného kontejneru objektů blob v úložišti Azure, a k instalaci balíčků použít upravený skript.
   >    Další informace o vývoji akcí skriptů najdete v tématu [Vývoj akcí skriptů](../hdinsight-hadoop-script-actions-linux.md).

   :::image type="content" source="./media/r-server-hdinsight-manage/submit-script-action.png" alt-text="Azure Portal odeslat akci skriptu" border="true":::

4. Vyberte **Vytvořit** a spusťte skript. Po dokončení skriptu jsou balíčky R k dispozici na všech pracovních uzlech.

## <a name="next-steps"></a>Další kroky

* [Zprovoznění clusteru služby ML Services v HDInsight](r-server-operationalize.md)
* [Možnosti výpočetního kontextu pro cluster služby ML v HDInsight](r-server-compute-contexts.md)
* [Možnosti Azure Storage pro cluster služby ML Services v HDInsight](r-server-storage.md)