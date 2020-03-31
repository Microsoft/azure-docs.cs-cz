---
title: Tipy pro používání Hadoopu na Linuxu hdinsight - Azure
description: Získejte tipy pro implementaci pro používání clusterů HDInsight (Hadoop) založených na Linuxu ve známém linuxovém prostředí běžícím v cloudu Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 3d9dec0065bb62821fcedcbc4f6e5b578c061caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272458"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informace o používání HDInsightu v Linuxu

Clustery Azure HDInsight poskytují Apache Hadoop ve známém linuxovém prostředí, které běží v cloudu Azure. Pro většinu věcí by měl fungovat přesně jako jakákoli jiná instalace Hadoop-on-Linux. Tento dokument volá konkrétní rozdíly, které byste měli znát.

## <a name="prerequisites"></a>Požadavky

Mnoho kroků v tomto dokumentu používá následující nástroje, které může být nutné nainstalovat do systému.

* [cURL](https://curl.haxx.se/) - používá se ke komunikaci s webovými službami.
* **jq**, procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) – slouží ke vzdálené správě služeb Azure.
* **Klient SSH**. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Uživatelé

Pokud [se nepřipojí k doméně](./domain-joined/hdinsight-security-overview.md), hdinsight by měl být považován za systém **jednoho uživatele.** S clusterem je vytvořen jeden uživatelský účet SSH s oprávněními na úrovni správce. Lze vytvořit další účty SSH, ale mají také přístup správce ke clusteru.

HdInsight spojený s doménou podporuje více uživatelů a podrobnější nastavení oprávnění a rolí. Další informace naleznete v [tématu Správa clusterů HDInsight přilehlých k doméně](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Názvy domén

Plně kvalifikovaný název domény (FQDN), který se má použít `CLUSTERNAME.azurehdinsight.net` při `CLUSTERNAME-ssh.azurehdinsight.net` připojování ke clusteru z Internetu, je nebo (pouze pro SSH).

Interně každý uzel v clusteru má název, který je přiřazen během konfigurace clusteru. Chcete-li najít názvy clusterů, podívejte se na stránku **Hosts** v uživatelském uživatelském uživatelském panelu Ambari. Seznam hostitelů z rozhraní AMbari REST API můžete také vrátit pomocí následujícího příkazu:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Nahraďte `CLUSTERNAME` názvem svého clusteru. Po zobrazení výzvy zadejte heslo pro účet správce. Tento příkaz vrátí dokument JSON, který obsahuje seznam hostitelů v clusteru. [jq](https://stedolan.github.io/jq/) se používá `host_name` k extrahování hodnoty prvku pro každého hostitele.

Pokud potřebujete najít název uzlu pro konkrétní službu, můžete dotaz Ambari pro tuto komponentu. Chcete-li například vyhledat hostitele pro uzel názvu HDFS, použijte následující příkaz:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Tento příkaz vrátí dokument JSON popisující službu a potom [jq](https://stedolan.github.io/jq/) vytáhne pouze hodnotu `host_name` pro hostitele.

## <a name="remote-access-to-services"></a>Vzdálený přístup ke službám

* **Ambari (web)** - `https://CLUSTERNAME.azurehdinsight.net`

    Ověřte pomocí uživatele a hesla správce clusteru a přihlaste se k Ambari.

    Ověřování je prostý text – vždy používejte protokol HTTPS, abyste zajistili, že připojení je zabezpečené.

    > [!IMPORTANT]  
    > Některá webová rozhraní dostupná prostřednictvím uzlů Ambari používající interní název domény. Interní názvy domén nejsou veřejně přístupné přes internet. Při pokusu o přístup k některým funkcím přes Internet se mohou zobrazit chyby "server nebyl nalezen".
    >
    > Chcete-li použít plnou funkčnost webového uživatelského uživatelského uživatelského nastavení Ambari, použijte tunelový propojení SSH k proxy webovému provozu hlavního uzlu clusteru. Viz [Použití tunelového propojení SSH pro přístup k webovému uživatelskému uživatelskému uživatelskému uživatelskému uživatelskému uživatelského nastavení Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie a dalších webových unavačů](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Ověřte pomocí uživatele a hesla správce clusteru.
    >
    > Ověřování je prostý text – vždy používejte protokol HTTPS, abyste zajistili, že připojení je zabezpečené.

* **WebHCat (Templeton)** - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Ověřte pomocí uživatele a hesla správce clusteru.
    >
    > Ověřování je prostý text – vždy používejte protokol HTTPS, abyste zajistili, že připojení je zabezpečené.

* **SSH** - CLUSTERNAME-ssh.azurehdinsight.net na portu 22 nebo 23. Port 22 se používá pro připojení k primárnímu headnode, zatímco 23 se používá pro připojení k sekundární. Další informace o uzlech v hlavových uzlech najdete [v tématu Dostupnost a spolehlivost clusterů Apache Hadoop v HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > K hlavním uzlům clusteru můžete přistupovat pouze prostřednictvím ssh z klientského počítače. Po připojení pak můžete přistupovat k pracovníu uzlům pomocí SSH z headnode.

Další informace najdete v [tématu porty používané službami Apache Hadoop na dokumentu HDInsight.](hdinsight-hadoop-port-settings-for-services.md)

## <a name="file-locations"></a>Umístění souborů

Soubory související s hadoopem lze nalézt `/usr/hdp`v uzlech clusteru na adrese . Tento adresář obsahuje následující podadresáře:

* **2.6.5.3009-43**: Název adresáře je verze platformy Hadoop používaná hdinsightem. Číslo v clusteru se může lišit od čísla uvedeného zde.
* **aktuální**: Tento adresář obsahuje odkazy na podadresáře v adresáři **2.6.5.3009-43.** Tento adresář existuje, takže si nemusíte pamatovat číslo verze.

Ukázková data a soubory JAR lze nalézt na `/example` `/HdiSamples`Hadoop Distributed File System at a .

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage a úložiště datových jezer

Ve většině distribucí Hadoop jsou data uložena v HDFS, které je zálohováno místním úložištěm na počítačích v clusteru. Použití místního úložiště může být nákladné pro cloudové řešení, kde se vám účtují každou hodinu nebo minutu za výpočetní prostředky.

Při použití HDInsight, datové soubory jsou uloženy v škálovatelné a odolné způsobem v cloudu pomocí Azure Blob Storage a volitelně Azure Data Lake Storage. Tyto služby poskytují následující výhody:

* Levné dlouhodobé skladování.
* Usnadnění přístupu z externích služeb, jako jsou webové stránky, nástroje pro nahrávání a stahování souborů, různé jazyky sady SDK a webové prohlížeče.
* Velká kapacita souborů a velké škálovatelné úložiště.

Další informace najdete [v tématu Principy objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) a [úložiště datových jezer](https://azure.microsoft.com/services/storage/data-lake-storage/).

Při použití Azure Storage nebo Data Lake Storage, nemusíte dělat nic zvláštního z HDInsight pro přístup k datům. Například následující příkaz uvádí soubory `/example/data` ve složce bez ohledu na to, jestli jsou uložené ve službě Azure Storage nebo Data Lake Storage:

    hdfs dfs -ls /example/data

V HDInsight prostředky úložiště dat (Azure Blob Storage a Azure Data Lake Storage) jsou odděleny od výpočetních prostředků. Proto můžete vytvořit clustery HDInsight pro výpočty, jak potřebujete, a později odstranit cluster po dokončení práce, mezitím udržet vaše datové soubory bezpečně trvalé v cloudovém úložišti tak dlouho, jak budete potřebovat.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>Identifikátor YUR a schéma

Některé příkazy mohou vyžadovat zadání schématu jako součást identifikátoru URI při přístupu k souboru. Například komponenta Storm-HDFS vyžaduje zadání schématu. Při použití jiného než výchozíúložiště (úložiště přidáno jako "další" úložiště do clusteru), musíte vždy použít schéma jako součást URI.

Při používání [**Azure Storage**](./hdinsight-hadoop-use-blob-storage.md)použijte jedno z následujících schémat URI:

* `wasb:///`: Přístup k výchozímu úložišti pomocí nešifrované komunikace.

* `wasbs:///`: Přístup k výchozímu úložišti pomocí šifrované komunikace.  Schéma wasbs je podporováno pouze od verze HDInsight 3.6.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Používá se při komunikaci s nevýchozím účtem úložiště. Například když máte další účet úložiště nebo při přístupu k datům uloženým ve veřejně přístupném účtu úložiště.

Při použití [**Azure Data Lake Storage Gen2**](./hdinsight-hadoop-use-data-lake-storage-gen2.md)použijte následující schéma URI:

* `abfs://`: Přístup k výchozímu úložišti pomocí šifrované komunikace.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Používá se při komunikaci s nevýchozím účtem úložiště. Například když máte další účet úložiště nebo při přístupu k datům uloženým ve veřejně přístupném účtu úložiště.

Při použití [**Azure Data Lake Storage Gen1**](./hdinsight-hadoop-use-data-lake-store.md)použijte jedno z následujících schémat URI:

* `adl:///`: Přístup k výchozímu úložišti datových jezer pro cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Používá se při komunikaci s nevýchozím úložištěm datových jezer. Používá se také pro přístup k datům mimo kořenový adresář clusteru HDInsight.

> [!IMPORTANT]  
> Při použití úložiště datového jezera jako výchozíúložiště pro HDInsight, musíte zadat cestu v rámci úložiště použít jako kořen úložiště HDInsight. Výchozí cesta `/clusters/<cluster-name>/`je .
>
> Při `/` použití `adl:///` nebo přístupu k datům můžete přistupovat pouze `/clusters/<cluster-name>/`k datům uloženým v kořenovém adresáři (například) clusteru. Chcete-li získat přístup k `adl://<storage-name>.azuredatalakestore.net/` datům kdekoli v úložišti, použijte formát.

### <a name="what-storage-is-the-cluster-using"></a>Jaké úložiště používá cluster

Ambari můžete použít k načtení výchozí konfigurace úložiště pro cluster. Pomocí následujícího příkazu načtěte informace o konfiguraci HDFS pomocí zvlnění a filtrujte je pomocí [jq](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Tento příkaz vrátí první konfiguraci`service_config_version=1`použitou na server ( ), který tyto informace obsahuje. Možná budete muset vypsat všechny konfigurační verze, abyste našli nejnovější.

Tento příkaz vrátí hodnotu podobnou následujícím identifikátorům URI:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`pokud používáte účet Azure Storage.

    Název účtu je název účtu Azure Storage. Název kontejneru je kontejner objektů blob, který je kořenem úložiště clusteru.

* `adl://home`pokud používáte Azure Data Lake Storage. Chcete-li získat název úložiště datového jezera, použijte následující volání REST:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Tento příkaz vrátí následující `<data-lake-store-account-name>.azuredatalakestore.net`název hostitele: .

    Chcete-li získat adresář v úložišti, který je kořenem pro HDInsight, použijte následující volání REST:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Tento příkaz vrátí cestu podobnou `/clusters/<hdinsight-cluster-name>/`následující cestě: .

Informace o úložišti můžete najít také pomocí portálu Azure pomocí následujících kroků:

1. Na [portálu Azure](https://portal.azure.com/)vyberte cluster HDInsight.

2. V části **Vlastnosti** vyberte **Účty úložiště**. Zobrazí se informace o úložišti pro cluster.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Jak lze získat přístup k souborům mimo HDInsight

Existují různé způsoby přístupu k datům mimo cluster HDInsight. Následuje několik odkazů na nástroje a sady SDK, které lze použít pro práci s daty:

Pokud používáte __Azure Storage__, podívejte se na následující odkazy, jak můžete přistupovat ke svým datům:

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2): Příkazy rozhraní příkazového řádku pro práci s Azure. Po instalaci použijte `az storage` příkaz pro pomoc při `az storage blob` používání úložiště nebo pro příkazy specifické pro objekt blob.
* [blobxfer.py](https://github.com/Azure/blobxfer): Skript pythonu pro práci s objekty BLOB ve službě Azure Storage.
* Různé sady SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Rozhraní REST API pro službu Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Pokud používáte __Azure Data Lake Storage__, podívejte se na následující odkazy, jak můžete přistupovat ke svým datům:

* [Webový prohlížeč](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [Rozhraní REST WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Nástroje datového jezera pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>Změna velikosti clusteru

Funkce škálování clusteru umožňuje dynamicky měnit počet datových uzlů používaných clusterem. Operace škálování můžete provádět, zatímco v clusteru jsou spuštěny jiné úlohy nebo procesy.  Viz [také, Škálování clusterů HDInsight](./hdinsight-scaling-best-practices.md)

Různé typy clusterů jsou ovlivněny škálováním následujícím způsobem:

* **Hadoop**: Při škálování počet uzlů v clusteru, některé služby v clusteru jsou restartovány. Operace škálování může způsobit úlohy spuštěné nebo čekající na selhání při dokončení operace škálování. Po dokončení operace můžete úlohy znovu odeslat.
* **HBase**: Regionální servery jsou automaticky vyváženy během několika minut, po dokončení operace škálování. Chcete-li ručně vyvážit místní servery, postupujte takto:

    1. Připojte se ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Ke spuštění prostředí HBase použijte následující:

            hbase shell

    3. Po načtení prostředí HBase použijte následující příkazkem k ručnímu vyvážení regionálních serverů:

            balancer

* **Bouře**: Po provedení operace škálování byste měli znovu vyvážit všechny spuštěné topologie bouře. Vyvážení umožňuje topologii upravit nastavení paralelismu na základě nového počtu uzlů v clusteru. Chcete-li znovu vyvážit spuštěné topologie, použijte jednu z následujících možností:

    * **SSH**: Připojte se k serveru a pomocí následujícího příkazu znovu vyvažte topologii:

            storm rebalance TOPOLOGYNAME

        Můžete také zadat parametry k přepsání rad rovnoběžníku původně poskytované topologie. Například `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` překonfiguruje topologii na 5 pracovních procesů, 3 vykonavatelé pro součást modré hubice a 10 vykonavatelů pro součást žlutého šroubu.

    * **Storm UI**: Pomocí následujících kroků můžete znovu vyvážit topologii pomocí ui.

        1. Otevřete `https://CLUSTERNAME.azurehdinsight.net/stormui` ve webovém `CLUSTERNAME` prohlížeči, kde je název clusteru Storm. Pokud se zobrazí výzva, zadejte název a heslo správce clusteru HDInsight (správce), které jste zadali při vytváření clusteru.
        2. Vyberte topologii, kterou chcete znovu vyvážit, a pak vyberte tlačítko **Znovu vyvážit.** Zadejte zpoždění před provedením operace opětovného vyvážení.

* **Kafka**: Po operacích škálování byste měli znovu vyvážit repliky oddílů. Další informace najdete v [tématu Vysoká dostupnost dat s Apache Kafka na HDInsight](./kafka/apache-kafka-high-availability.md) dokumentu.

Konkrétní informace o škálování clusteru HDInsight najdete v tématu:

* [Správa clusterů Apache Hadoop v HDInsightu pomocí portálu Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Správa clusterů Apache Hadoop v HDInsightu pomocí rozhraní příkazového příkazu Azure](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Jak nainstaluji Hue (nebo jinou součást Hadoop)?

HDInsight je spravovaná služba. Pokud Azure zjistí problém s clusterem, může odstranit uzel selhání a vytvořit uzel nahradit. Pokud ručně nainstalujete věci v clusteru, nejsou trvalé, když dojde k této operaci. Místo toho použijte [hdinsight skript akce](hdinsight-hadoop-customize-cluster-linux.md). Akci skriptu lze použít k provedení následujících změn:

* Nainstalujte a nakonfigurujte službu nebo web.
* Nainstalujte a nakonfigurujte komponentu, která vyžaduje změny konfigurace ve více uzlech v clusteru.

Akce skriptu jsou skripty Bash. Skripty jsou spuštěny během vytváření clusteru a používají se k instalaci a konfiguraci dalších součástí. Informace o vývoji vlastních akcí skriptů naleznete v tématu [Vývoj akcí skriptů v prostředí HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Jar soubory

Některé technologie Hadoop jsou k dispozici v samostatných souborech jar, které obsahují funkce používané jako součást úlohy MapReduce nebo zevnitř Pig nebo Hive. Často nevyžadují žádné nastavení a mohou být odeslány do clusteru po vytvoření a použity přímo. Pokud se chcete ujistit, že součást přežije reimaging clusteru, můžete uložit jar soubor ve výchozím úložišti pro cluster (WASB nebo ADL).

Pokud například chcete použít nejnovější verzi [Apache DataFu](https://datafu.incubator.apache.org/), můžete si stáhnout nádobu obsahující projekt a nahrát ji do clusteru HDInsight. Poté postupujte podle dokumentace DataFu o tom, jak ji používat z Pig nebo Hive.

> [!IMPORTANT]  
> Některé součásti, které jsou samostatné jar soubory jsou k dispozici s HDInsight, ale nejsou v cestě. Pokud hledáte konkrétní součást, můžete ji v clusteru vyhledat pomocí následujícího pole:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Tento příkaz vrátí cestu všech odpovídajících souborů jar.

Chcete-li použít jinou verzi komponenty, nahrajte ji a použijte ji ve svých úlohách.

> [!IMPORTANT]
> Součásti dodávané s clusterem HDInsight jsou plně podporovány a podpora společnosti Microsoft pomáhá izolovat a vyřešit problémy související s těmito součástmi.
>
> Vlastní součásti obdrží komerčně přiměřenou podporu, která vám pomůže dále řešit problém. To může vést k vyřešení problému nebo s žádostí o zapojení dostupných kanálů pro technologie s otevřeným zdrojovým kódem, kde jsou nalezeny hluboké odborné znalosti pro tuto technologii. Existuje například mnoho komunitních webů, které lze použít, například: [https://stackoverflow.com](https://stackoverflow.com) [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight). Také Apache projekty [https://apache.org](https://apache.org)mají projektové weby na , například: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí rozhraní Apache Ambari REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Použití Apache Hive s HDInsight](hadoop/hdinsight-use-hive.md)
* [Použijte úlohy MapReduce s HDInsight](hadoop/hdinsight-use-mapreduce.md)
