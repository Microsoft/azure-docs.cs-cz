---
title: Tipy pro použití Hadoop v HDInsight založené na Linux – Azure
description: Získejte tipy k implementaci pro používání clusterů HDInsight se systémem Linux (Hadoop) na známém prostředí Linux běžící v cloudu Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 3d9dec0065bb62821fcedcbc4f6e5b578c061caf
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934546"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informace o používání HDInsightu v Linuxu

Clustery Azure HDInsight poskytují Apache Hadoop v známém prostředí pro Linux běžící v cloudu Azure. Pro většinu věcí by měla fungovat přesně stejně jako všechny ostatní instalace systému Hadoop-Linux. Tento dokument volá konkrétní rozdíly, o kterých byste měli vědět.

## <a name="prerequisites"></a>Požadavky

Mnohé z kroků v tomto dokumentu používají následující nástroje, které může být potřeba nainstalovat do systému.

* [oblý](https://curl.haxx.se/) – slouží ke komunikaci s webovými službami.
* **JQ**, procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) – slouží k vzdálené správě služeb Azure.
* **Klient SSH**. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Uživatelé

Pokud není [připojeno k doméně](./domain-joined/hdinsight-security-overview.md), HDInsight by se mělo považovat za systém s **jedním uživatelem** . V clusteru se vytvoří jeden uživatelský účet SSH s oprávněními na úrovni správce. Je možné vytvořit další účty SSH, ale mají také přístup správce ke clusteru.

Prostředí HDInsight připojené k doméně podporuje více uživatelů a přesnější nastavení oprávnění a rolí. Další informace najdete v tématu [Správa clusterů HDInsight připojených k doméně](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Názvy domén

Plně kvalifikovaný název domény (FQDN), který se má použít při připojování ke clusteru z Internetu, je `CLUSTERNAME.azurehdinsight.net` nebo `CLUSTERNAME-ssh.azurehdinsight.net` (jenom pro SSH).

Interně má každý uzel v clusteru název, který je přiřazený během konfigurace clusteru. Názvy clusterů najdete na stránce **hostitelé** ve webovém uživatelském rozhraní Ambari. K vrácení seznamu hostitelů z Ambari REST API můžete použít také následující:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Nahraďte `CLUSTERNAME` názvem svého clusteru. Po zobrazení výzvy zadejte heslo pro účet správce. Tento příkaz vrátí dokument JSON, který obsahuje seznam hostitelů v clusteru. [JQ](https://stedolan.github.io/jq/) se používá k extrakci hodnoty prvku `host_name` pro každého hostitele.

Pokud potřebujete najít název uzlu pro konkrétní službu, můžete pro danou součást zadat dotaz na Ambari. Chcete-li například vyhledat hostitele pro uzel HDFS Name, použijte následující příkaz:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Tento příkaz vrátí dokument JSON popisující službu a pak [JQ](https://stedolan.github.io/jq/) vyžádá jenom `host_name`ou hodnotu pro hostitele.

## <a name="remote-access-to-services"></a>Vzdálený přístup ke službám

* **Ambari (Web)**  - `https://CLUSTERNAME.azurehdinsight.net`

    Proveďte ověření pomocí uživatele a hesla správce clusteru a pak se přihlaste k Ambari.

    Ověřování je prostého textu – pomocí protokolu HTTPS vždy pomůžete zajistit, aby připojení bylo zabezpečené.

    > [!IMPORTANT]  
    > Některé z webových uživatelská rozhraní jsou dostupné prostřednictvím uzlů pro přístup k Ambari pomocí interního názvu domény. Interní názvy domén nejsou veřejně přístupné prostřednictvím Internetu. Při pokusu o přístup k některým funkcím přes Internet se může zobrazit chyba "Server nebyl nalezen".
    >
    > Pokud chcete používat všechny funkce webového uživatelského rozhraní Ambari, použijte tunel SSH pro proxy webový provoz do hlavního uzlu clusteru. Viz [použití tunelového propojení SSH pro přístup k webovému uživatelskému rozhraní Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie a dalším webovým uživatelská rozhraní](hdinsight-linux-ambari-ssh-tunnel.md) .

* **Ambari (REST)**  - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Ověřování pomocí uživatele a hesla správce clusteru.
    >
    > Ověřování je prostého textu – pomocí protokolu HTTPS vždy pomůžete zajistit, aby připojení bylo zabezpečené.

* **WebHCat (Templeton)**  - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Ověřování pomocí uživatele a hesla správce clusteru.
    >
    > Ověřování je prostého textu – pomocí protokolu HTTPS vždy pomůžete zajistit, aby připojení bylo zabezpečené.

* **SSH** -CLUSTERNAME-SSH.azurehdinsight.NET na portu 22 nebo 23. Port 22 se používá pro připojení k primárnímu hlavnímu uzlu, zatímco 23 se používá pro připojení k sekundárnímu. Další informace o hlavních uzlech najdete v tématu [dostupnost a spolehlivost Apache Hadoopch clusterů ve službě HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > K hlavním uzlům clusteru můžete přistupovat jenom přes SSH z klientského počítače. Po připojení můžete k pracovním uzlům přistupovat pomocí SSH z hlavnímu uzlu.

Další informace najdete v tématu [porty používané službou Apache Hadoop Services na dokumentu HDInsight](hdinsight-hadoop-port-settings-for-services.md) .

## <a name="file-locations"></a>Umístění souborů

Soubory související se systémem Hadoop najdete na uzlech clusteru na `/usr/hdp`. Tento adresář obsahuje následující podadresáře:

* **2.6.5.3009-43**: název adresáře je verze platformy Hadoop, kterou používá HDInsight. Číslo v clusteru se může lišit od výše uvedeného.
* **Current**: Tento adresář obsahuje odkazy na podadresáře v adresáři **2.6.5.3009-43** . Tento adresář existuje, takže si nemusíte pamatovat číslo verze.

Příklady dat a souborů JAR najdete na systém souborů DFS (Distributed File System) Hadoop v `/example` a `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage a Data Lake Storage

Ve většině distribucí Hadoop jsou data uložená v HDFS, která se na počítačích v clusteru zálohuje na základě místního úložiště. Použití místního úložiště může být nákladné pro cloudové řešení, ve kterém se za výpočetní prostředky účtují každou hodinu nebo za minutu.

Při použití HDInsight se datové soubory ukládají v cloudu v cloudu s využitím Azure Blob Storage a případně Azure Data Lake Storage. Tyto služby poskytují následující výhody:

* Levné dlouhodobé úložiště.
* Přístupnost z externích služeb, jako jsou weby, odesílání souborů/stahování nástrojů, různé jazykové sady SDK a webové prohlížeče.
* Velká kapacita souborů a velké škálovatelné úložiště.

Další informace najdete v tématu [Principy objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) a [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

Pokud používáte Azure Storage nebo Data Lake Storage, nemusíte pro přístup k datům dělat nic zvláštního. Například následující příkaz vypíše soubory ve složce `/example/data` bez ohledu na to, zda jsou uloženy v Azure Storage nebo Data Lake Storage:

    hdfs dfs -ls /example/data

V HDInsight se prostředky úložiště dat (Azure Blob Storage a Azure Data Lake Storage) odpojí od výpočetních prostředků. Proto můžete vytvořit clustery HDInsight, abyste mohli provádět výpočty podle potřeby, a později odstranit cluster, až se práce dokončí, a přitom trvale uchovávat datové soubory v cloudovém úložišti, dokud budete potřebovat.

### <a name="URI-and-scheme"></a>Identifikátor URI a schéma

Některé příkazy mohou vyžadovat, abyste při přístupu k souboru určili schéma jako součást identifikátoru URI. Například komponenta pro zaplavování vyžaduje, abyste určili schéma. Pokud používáte jiné než výchozí úložiště (úložiště přidáno do clusteru jako "Další" úložiště), musíte toto schéma vždy použít jako součást identifikátoru URI.

Při použití [**Azure Storage**](./hdinsight-hadoop-use-blob-storage.md)použijte jedno z následujících schémat identifikátorů URI:

* `wasb:///`: přístup k výchozímu úložišti pomocí nešifrované komunikace.

* `wasbs:///`: přístup k výchozímu úložišti pomocí šifrované komunikace.  Schéma wasbs se podporuje jenom ve verzi HDInsight 3,6 a vyšší.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: používá se při komunikaci s jiným než výchozím účtem úložiště. Například když máte další účet úložiště nebo když přistupujete k datům uloženým v veřejně přístupném účtu úložiště.

Při použití [**Azure Data Lake Storage Gen2**](./hdinsight-hadoop-use-data-lake-storage-gen2.md)použijte následující schéma identifikátoru URI:

* `abfs://`: přístup k výchozímu úložišti pomocí šifrované komunikace.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: používá se při komunikaci s jiným než výchozím účtem úložiště. Například když máte další účet úložiště nebo když přistupujete k datům uloženým v veřejně přístupném účtu úložiště.

Při použití [**Azure Data Lake Storage Gen1**](./hdinsight-hadoop-use-data-lake-store.md)použijte jedno z následujících schémat identifikátorů URI:

* `adl:///`: přístup k výchozímu Data Lake Storage clusteru.

* `adl://<storage-name>.azuredatalakestore.net/`: používá se při komunikaci s jiným než výchozím Data Lake Storage. Používá se také pro přístup k datům mimo kořenový adresář clusteru HDInsight.

> [!IMPORTANT]  
> Při použití Data Lake Storage jako výchozího úložiště pro HDInsight musíte zadat cestu v úložišti, která se má použít jako kořenový adresář úložiště HDInsight. Výchozí cesta je `/clusters/<cluster-name>/`.
>
> Při použití `/` nebo `adl:///` k přístupu k datům můžete přistupovat pouze k datům uloženým v kořenovém adresáři (například `/clusters/<cluster-name>/`) clusteru. Chcete-li získat přístup k datům kdekoli v úložišti, použijte formát `adl://<storage-name>.azuredatalakestore.net/`.

### <a name="what-storage-is-the-cluster-using"></a>Jaké úložiště používá cluster

Pomocí Ambari můžete načíst výchozí konfiguraci úložiště pro cluster. Pomocí následujícího příkazu můžete načíst informace o konfiguraci HDFS pomocí formátu kudrlinkou a filtrovat je pomocí [JQ](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Tento příkaz vrátí první konfiguraci použitou pro server (`service_config_version=1`), která obsahuje tyto informace. Možná budete muset zobrazit seznam všech verzí konfigurace, abyste našli nejnovější verzi.

Tento příkaz vrátí hodnotu podobnou následujícím identifikátorům URI:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`, pokud se používá účet Azure Storage

    Název účtu je název účtu Azure Storage. Název kontejneru je kontejner objektů blob, který je kořenovým adresářem úložiště clusteru.

* `adl://home`, pokud se používá Azure Data Lake Storage Chcete-li získat název Data Lake Storage, použijte následující volání REST:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Tento příkaz vrátí následující název hostitele: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Pokud chcete získat adresář v rámci úložiště, který je kořenem pro HDInsight, použijte následující volání REST:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Tento příkaz vrátí cestu podobnou následující cestě: `/clusters/<hdinsight-cluster-name>/`.

Informace o úložišti můžete také najít pomocí Azure Portal pomocí následujících kroků:

1. Z [Azure Portal](https://portal.azure.com/)vyberte svůj cluster HDInsight.

2. V části **vlastnosti** vyberte **účty úložiště**. Zobrazí se informace o úložišti pro cluster.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Návody přístup k souborům mimo HDInsight

Existují různé způsoby, jak získat přístup k datům mimo cluster HDInsight. Následuje několik odkazů na nástroje a sady SDK, které lze použít pro práci s daty:

Pokud používáte __Azure Storage__, přečtěte si následující odkazy, které vám umožní získat přístup k datům:

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2): příkazy rozhraní příkazového řádku pro práci s Azure. Po instalaci použijte příkaz `az storage`, který vám může pomoci s používáním úložiště, nebo `az storage blob` pro příkazy specifické pro objekty blob.
* [blobxfer.py](https://github.com/Azure/blobxfer): skript Pythonu pro práci s objekty blob v Azure Storage.
* Různé sady SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Rozhraní REST API pro Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Pokud používáte __Azure Data Lake Storage__, přečtěte si následující odkazy, které vám umožní získat přístup k datům:

* [Webový prohlížeč](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake Tools pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Škálování clusteru

Funkce škálování clusteru umožňuje dynamicky měnit počet datových uzlů používaných clusterem. V případě, že se v clusteru spouštějí jiné úlohy nebo procesy, můžete provádět operace škálování.  Viz také [škálování clusterů HDInsight](./hdinsight-scaling-best-practices.md)

Různé typy clusterů jsou ovlivněny škálováním následujícím způsobem:

* **Hadoop**: při horizontálním navýšení kapacity počtu uzlů v clusteru se některé služby v clusteru restartují. Operace škálování můžou způsobit, že úlohy spuštěné nebo čekají na selhání při dokončení operace škálování. Po dokončení operace můžete úlohy znovu odeslat.
* **HBA**: regionální servery se po dokončení operace škálování automaticky vyrovnávají během několika minut. K ručnímu vyrovnávání regionálních serverů použijte následující postup:

    1. Připojte se ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. K zahájení prostředí HBA použijte následující:

            hbase shell

    3. Po načtení prostředí HBA použijte následující příkaz k ručnímu vyvážení místních serverů:

            balancer

* Zatížení **: po**provedení operace škálování byste měli znovu vyrovnávat všechny běžící topologie. Nové vyrovnávání umožňuje, aby topologie znovu nastavila nastavení paralelismu na základě nového počtu uzlů v clusteru. Chcete-li znovu vyrovnávat spuštěné topologie, použijte jednu z následujících možností:

    * **SSH**: Připojte se k serveru a použijte následující příkaz k opětovnému vyrovnávání topologie:

            storm rebalance TOPOLOGYNAME

        Můžete také zadat parametry pro přepsání pomocného parametru paralelismus původně poskytnuté topologie. `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` například změní konfiguraci topologie na 5 pracovních procesů, 3 prováděcí moduly pro komponentu Blue-Spout a 10 prováděcích modulů pro komponentu žlutého šroubu.

    * **Uživatelské rozhraní**pro vyrovnávání zatížení: pomocí následujících kroků můžete znovu vyvážit topologii pomocí uživatelského rozhraní.

        1. Ve webovém prohlížeči otevřete `https://CLUSTERNAME.azurehdinsight.net/stormui`, kde `CLUSTERNAME` je název vašeho clusteru s více podsítěmi. Po zobrazení výzvy zadejte název správce clusteru HDInsight (správce) a heslo, které jste zadali při vytváření clusteru.
        2. Vyberte topologii, kterou chcete znovu vyvážit, a pak vyberte tlačítko pro **vyvážení** . Zadejte zpoždění před provedením operace obnovení rovnováhy.

* **Kafka**: po provedení operace škálování byste měli znovu vyrovnávat repliky oddílů. Další informace najdete v článku o [vysoké dostupnosti dat s Apache Kafka v dokumentu HDInsight](./kafka/apache-kafka-high-availability.md) .

Konkrétní informace o škálování clusteru HDInsight najdete v těchto tématech:

* [Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure Portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Správa clusterů Apache Hadoop ve službě HDInsight pomocí rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Návody nainstalovat odstín (nebo jinou komponentu Hadoop)?

HDInsight je spravovaná služba. Pokud Azure zjistí problém s clusterem, může odstranit uzel, který selhal, a vytvořit uzel, který ho nahradí. Pokud ručně nainstalujete na cluster nějaké věci, nebudou při této operaci trvalé. Místo toho použijte [akce skriptu HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Akci skriptu lze použít k provedení následujících změn:

* Instalace a konfigurace služby nebo webu.
* Nainstalujte a nakonfigurujte komponentu, která vyžaduje změny konfigurace na více uzlech v clusteru.

Akce skriptů jsou skripty bash. Skripty se spouští během vytváření clusteru a používají se k instalaci a konfiguraci dalších součástí. Informace o vývoji vlastních akcí skriptů naleznete v tématu [Vývoj akcí skriptů v prostředí HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Soubory jar

Některé technologie Hadoop jsou k dispozici v samostatných souborech jar, které obsahují funkce používané jako součást MapReduce úlohy, nebo z prasete nebo podregistru. Často nevyžadují žádné nastavení a dají se do clusteru nahrát po vytvoření a použití přímo. Pokud chcete zajistit, aby součást předržela přebitovou kopii clusteru, můžete uložit soubor JAR do výchozího úložiště pro váš cluster (WASB nebo ADL).

Například pokud chcete použít nejnovější verzi [Apache DataFu](https://datafu.incubator.apache.org/), můžete si stáhnout jar obsahující projekt a nahrát ho do clusteru HDInsight. Pak postupujte podle pokynů v dokumentaci k DataFu, jak ji použít z prasete nebo z podregistru.

> [!IMPORTANT]  
> Některé součásti, které jsou samostatnými soubory jar, jsou součástí HDInsight, ale nejsou v cestě. Pokud hledáte určitou součást, můžete ji vyhledat na svém clusteru pomocí následujícího postupu:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Tento příkaz vrátí cestu všech souborů JAR, které odpovídají.

Pokud chcete použít jinou verzi komponenty, nahrajte potřebnou verzi a použijte ji ve svých úlohách.

> [!IMPORTANT]
> Komponenty dodávané s clusterem HDInsight jsou plně podporované a podpora Microsoftu pomáhají izolovat a řešit problémy související s těmito součástmi.
>
> Vlastní komponenty získají komerčně přiměřenou podporu, která vám může pomoct s dalším řešením tohoto problému. To může vést k vyřešení problému nebo požádá vás o zapojení dostupných kanálů pro technologie Open Source, ve kterých se najde hlubokou odbornost pro danou technologii. Například existuje mnoho webů komunity, které lze použít, například [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Projekty Apache také obsahují projektové weby na [https://apache.org](https://apache.org), například: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí REST API Apache Ambari](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití úloh MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)
