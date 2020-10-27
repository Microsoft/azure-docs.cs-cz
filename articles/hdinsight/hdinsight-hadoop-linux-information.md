---
title: Tipy pro použití Hadoop v HDInsight založené na Linux – Azure
description: Získejte tipy k implementaci pro používání clusterů HDInsight se systémem Linux (Hadoop) na známém prostředí Linux běžící v cloudu Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 0f0073c72c28395d89cec74a489cbc36a8f3ffe7
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546105"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informace o používání HDInsightu v Linuxu

Clustery Azure HDInsight poskytují Apache Hadoop v známém prostředí pro Linux běžící v cloudu Azure. Pro většinu věcí by měla fungovat přesně stejně jako všechny ostatní instalace systému Hadoop-Linux. Tento dokument volá konkrétní rozdíly, o kterých byste měli vědět.

## <a name="prerequisites"></a>Předpoklady

Mnohé z kroků v tomto dokumentu používají následující nástroje, které může být potřeba nainstalovat do systému.

* [oblý](https://curl.haxx.se/) – slouží ke komunikaci s webovými službami.
* **JQ** , procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .
* [Azure CLI](/cli/azure/install-azure-cli) – slouží k vzdálené správě služeb Azure.
* **Klient SSH** . Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Uživatelé

Pokud není [připojeno k doméně](./domain-joined/hdinsight-security-overview.md), HDInsight by se mělo považovat za systém s **jedním uživatelem** . V clusteru se vytvoří jeden uživatelský účet SSH s oprávněními na úrovni správce. Je možné vytvořit další účty SSH, ale mají také přístup správce ke clusteru.

Prostředí HDInsight připojené k doméně podporuje více uživatelů a přesnější nastavení oprávnění a rolí. Další informace najdete v tématu [Správa clusterů HDInsight připojených k doméně](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Názvy domén

Plně kvalifikovaný název domény (FQDN), který se má použít při připojování ke clusteru z Internetu, je `CLUSTERNAME.azurehdinsight.net` nebo `CLUSTERNAME-ssh.azurehdinsight.net` (jenom pro SSH).

Interně má každý uzel v clusteru název, který je přiřazený během konfigurace clusteru. Názvy clusterů najdete na stránce **hostitelé** ve webovém uživatelském rozhraní Ambari. K vrácení seznamu hostitelů z Ambari REST API můžete použít také následující:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'
```

Nahraďte `CLUSTERNAME` názvem svého clusteru. Po zobrazení výzvy zadejte heslo pro účet správce. Tento příkaz vrátí dokument JSON, který obsahuje seznam hostitelů v clusteru. [JQ](https://stedolan.github.io/jq/) se používá k extrakci `host_name` hodnoty prvku pro každého hostitele.

Pokud potřebujete najít název uzlu pro konkrétní službu, můžete pro danou součást zadat dotaz na Ambari. Chcete-li například vyhledat hostitele pro uzel HDFS Name, použijte následující příkaz:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'
```

Tento příkaz vrátí dokument JSON popisující službu a pak [JQ](https://stedolan.github.io/jq/) vyžádá pouze `host_name` hodnotu pro hostitele.

## <a name="remote-access-to-services"></a>Vzdálený přístup ke službám

* **Ambari (Web)** - `https://CLUSTERNAME.azurehdinsight.net`

    Proveďte ověření pomocí uživatele a hesla správce clusteru a pak se přihlaste k Ambari.

    Ověřování je prostého textu – pomocí protokolu HTTPS vždy pomůžete zajistit, aby připojení bylo zabezpečené.

    > [!IMPORTANT]  
    > Některé z webových uživatelská rozhraní jsou dostupné prostřednictvím uzlů pro přístup k Ambari pomocí interního názvu domény. Interní názvy domén nejsou veřejně přístupné prostřednictvím Internetu. Při pokusu o přístup k některým funkcím přes Internet se může zobrazit chyba "Server nebyl nalezen".
    >
    > Pokud chcete používat všechny funkce webového uživatelského rozhraní Ambari, použijte tunel SSH pro proxy webový provoz do hlavního uzlu clusteru. Viz [použití tunelového propojení SSH pro přístup k webovému uživatelskému rozhraní Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie a dalším webovým uživatelská rozhraní](hdinsight-linux-ambari-ssh-tunnel.md) .

* **Ambari (REST)** - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Ověřování pomocí uživatele a hesla správce clusteru.
    >
    > Ověřování je prostého textu – pomocí protokolu HTTPS vždy pomůžete zajistit, aby připojení bylo zabezpečené.

* **WebHCat (Templeton)** - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Ověřování pomocí uživatele a hesla správce clusteru.
    >
    > Ověřování je prostého textu – pomocí protokolu HTTPS vždy pomůžete zajistit, aby připojení bylo zabezpečené.

* **SSH** -CLUSTERNAME-SSH.azurehdinsight.NET na portu 22 nebo 23. Port 22 se používá pro připojení k primárnímu hlavnímu uzlu, zatímco 23 se používá pro připojení k sekundárnímu. Další informace o hlavních uzlech najdete v tématu [dostupnost a spolehlivost Apache Hadoopch clusterů ve službě HDInsight](./hdinsight-business-continuity.md).

    > [!NOTE]  
    > K hlavním uzlům clusteru můžete přistupovat jenom přes SSH z klientského počítače. Po připojení můžete k pracovním uzlům přistupovat pomocí SSH z hlavnímu uzlu.

Další informace najdete v tématu [porty používané službou Apache Hadoop Services na dokumentu HDInsight](hdinsight-hadoop-port-settings-for-services.md) .

## <a name="file-locations"></a>Umístění souborů

Soubory související se systémem Hadoop lze nalézt na uzlech clusteru na adrese `/usr/hdp` . Tento adresář obsahuje následující podadresáře:

* **2.6.5.3009-43** : název adresáře je verze platformy Hadoop, kterou používá HDInsight. Číslo v clusteru se může lišit od výše uvedeného.
* **Current** : Tento adresář obsahuje odkazy na podadresáře v adresáři **2.6.5.3009-43** . Tento adresář existuje, takže si nemusíte pamatovat číslo verze.

Příklady dat a souborů JAR najdete v systém souborů DFS (Distributed File System) Hadoop v `/example` a `/HdiSamples` .

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage a Data Lake Storage

Ve většině distribucí Hadoop jsou data uložená v HDFS. HDFS je zajištěno místním úložištěm v počítačích v clusteru. Použití místního úložiště může být nákladné pro cloudové řešení, ve kterém se za výpočetní prostředky účtují každou hodinu nebo za minutu.

Při použití HDInsight se datové soubory ukládají v cloudu s možností adaptivního a odolného v cloudu pomocí Azure Blob Storage a volitelně Azure Data Lake Storage Gen1/Gen2. Tyto služby poskytují následující výhody:

* Levné dlouhodobé úložiště.
* Přístupnost z externích služeb, jako jsou weby, odesílání souborů/stahování nástrojů, různé jazykové sady SDK a webové prohlížeče.
* Velká kapacita souborů a velké adaptivní úložiště.

Další informace najdete v tématu [úložiště objektů BLOB v Azure](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)nebo [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Pokud používáte úložiště objektů BLOB v Azure nebo Data Lake Storage Gen1/Gen2, nemusíte pro přístup k datům dělat nic jiného, než HDInsight. Například následující příkaz vypíše soubory ve `/example/data` složce, ať jsou uloženy v Azure Storage nebo data Lake Storage:

```console
hdfs dfs -ls /example/data
```

V HDInsight se prostředky úložiště dat (Azure Blob Storage a Azure Data Lake Storage) odpojí od výpočetních prostředků. Clustery HDInsight můžete vytvořit pro výpočty podle potřeby a později odstranit cluster po dokončení práce. Průběžně udržujeme vaše datové soubory bezpečně v cloudovém úložišti, pokud potřebujete.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>Identifikátor URI a schéma

Některé příkazy mohou vyžadovat, abyste při přístupu k souboru určili schéma jako součást identifikátoru URI. Například komponenta pro zaplavování vyžaduje, abyste určili schéma. Pokud používáte jiné než výchozí úložiště (úložiště přidáno do clusteru jako "Další" úložiště), musíte toto schéma vždy použít jako součást identifikátoru URI.

Při použití [**Azure Storage**](./hdinsight-hadoop-use-blob-storage.md)použijte jedno z následujících schémat identifikátorů URI:

* `wasb:///`: Přístup k výchozímu úložišti pomocí nešifrované komunikace.

* `wasbs:///`: Přístup k výchozímu úložišti pomocí šifrované komunikace.  Schéma wasbs se podporuje jenom ve verzi HDInsight 3,6 a vyšší.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Používá se při komunikaci s jiným než výchozím účtem úložiště. Například když máte další účet úložiště nebo když přistupujete k datům uloženým v veřejně přístupném účtu úložiště.

Při použití [**Azure Data Lake Storage Gen2**](./hdinsight-hadoop-use-data-lake-storage-gen2.md)použijte následující schéma identifikátoru URI:

* `abfs://`: Přístup k výchozímu úložišti pomocí šifrované komunikace.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Používá se při komunikaci s jiným než výchozím účtem úložiště. Například když máte další účet úložiště nebo když přistupujete k datům uloženým v veřejně přístupném účtu úložiště.

Při použití [**Azure Data Lake Storage Gen1**](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)použijte jedno z následujících schémat identifikátorů URI:

* `adl:///`: Přístup k výchozímu Data Lake Storage clusteru.

* `adl://<storage-name>.azuredatalakestore.net/`: Používá se při komunikaci s jiným než výchozím Data Lake Storage. Používá se také pro přístup k datům mimo kořenový adresář clusteru HDInsight.

> [!IMPORTANT]  
> Při použití Data Lake Storage jako výchozího úložiště pro HDInsight musíte zadat cestu v úložišti, která se má použít jako kořenový adresář úložiště HDInsight. Výchozí cesta je `/clusters/<cluster-name>/` .
>
> Při použití `/` nebo `adl:///` pro přístup k datům můžete přistupovat pouze k datům uloženým v kořenovém adresáři (například `/clusters/<cluster-name>/` ) clusteru. Chcete-li získat přístup k datům kdekoli v úložišti, použijte `adl://<storage-name>.azuredatalakestore.net/` formát.

### <a name="what-storage-is-the-cluster-using"></a>Jaké úložiště používá cluster

Pomocí Ambari můžete načíst výchozí konfiguraci úložiště pro cluster. Pomocí následujícího příkazu můžete načíst informace o konfiguraci HDFS pomocí formátu kudrlinkou a filtrovat je pomocí [JQ](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Tento příkaz vrátí první konfiguraci použitou pro server ( `service_config_version=1` ), který obsahuje tyto informace. Možná budete muset zobrazit seznam všech verzí konfigurace, abyste našli nejnovější verzi.

Tento příkaz vrátí hodnotu podobnou následujícím identifikátorům URI:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` Pokud používáte účet Azure Storage.

    Název účtu je název účtu Azure Storage. Název kontejneru je kontejner objektů blob, který je kořenovým adresářem úložiště clusteru.

* `adl://home` Pokud používáte Azure Data Lake Storage. Chcete-li získat název Data Lake Storage, použijte následující volání REST:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Tento příkaz vrátí následující název hostitele: `<data-lake-store-account-name>.azuredatalakestore.net` .

    Pokud chcete získat adresář v rámci úložiště, který je kořenem pro HDInsight, použijte následující volání REST:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Tento příkaz vrátí cestu podobnou následující cestě: `/clusters/<hdinsight-cluster-name>/` .

Informace o úložišti můžete také najít pomocí Azure Portal pomocí následujících kroků:

1. Z [Azure Portal](https://portal.azure.com/)vyberte svůj cluster HDInsight.

2. V části **vlastnosti** vyberte **účty úložiště** . Zobrazí se informace o úložišti pro cluster.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Návody přístup k souborům mimo HDInsight

Existují různé způsoby, jak získat přístup k datům mimo cluster HDInsight. Následuje několik odkazů na nástroje a sady SDK, které lze použít pro práci s daty:

Pokud používáte __úložiště objektů BLOB v Azure__ , přečtěte si následující odkazy, které vám umožní získat přístup k datům:

* [Azure CLI](/cli/azure/install-az-cli2): příkazy rozhraní Command-Line pro práci s Azure. Po instalaci použijte `az storage` příkaz pro nápovědu k používání úložiště nebo `az storage blob` pro příkazy pro objekty blob.
* [blobxfer.py](https://github.com/Azure/blobxfer): skript Pythonu pro práci s objekty blob v Azure Storage.
* Různé sady SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Rozhraní REST API pro službu Storage](/rest/api/storageservices/Blob-Service-REST-API)

Pokud používáte __Azure Data Lake Storage Gen1__ , přečtěte si následující odkazy, které vám umožní získat přístup k datům:

* [Webový prohlížeč](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake nástroje pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>Škálování clusteru

Funkce škálování clusteru umožňuje dynamicky měnit počet datových uzlů používaných clusterem. Operace škálování můžete provádět i v případě, že jsou spuštěné jiné úlohy nebo procesy v clusteru.  Viz [škálování clusterů HDInsight](./hdinsight-scaling-best-practices.md)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Návody nainstalovat odstín (nebo jinou komponentu Hadoop)?

HDInsight je spravovaná služba. Pokud Azure zjistí problém s clusterem, může odstranit uzel, který selhal, a vytvořit uzel, který ho nahradí. Když ručně nainstalujete na cluster nějaké věci, nebudou při této operaci trvalé. Místo toho použijte [akce skriptu HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Akci skriptu lze použít k provedení následujících změn:

* Instalace a konfigurace služby nebo webu.
* Nainstalujte a nakonfigurujte komponentu, která vyžaduje změny konfigurace na více uzlech v clusteru.

Akce skriptů jsou skripty bash. Skripty se spouští během vytváření clusteru a používají se k instalaci a konfiguraci dalších součástí. Informace o vývoji vlastních akcí skriptů naleznete v tématu [Vývoj akcí skriptů v prostředí HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Soubory jar

Některé technologie Hadoop poskytují samostatně obsažené soubory jar. Tyto soubory obsahují funkce používané jako součást úlohy MapReduce nebo z prasete nebo z podregistru. Často nevyžadují žádné nastavení a dají se do clusteru nahrát po vytvoření a použití přímo. Pokud chcete zajistit, aby součást předržela přebitovou kopii clusteru, uložte soubor JAR do výchozího úložiště clusteru.

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
> Vlastní komponenty získají komerčně přiměřenou podporu, která vám může pomoct s dalším řešením tohoto problému. To může vést k vyřešení problému nebo požádá vás o zapojení dostupných kanálů pro technologie Open Source, ve kterých se najde hlubokou odbornost pro danou technologii. Například existuje mnoho webů komunity, které lze použít, například: [Microsoft Q&stránku s otázkou pro HDInsight](/answers/topics/azure-hdinsight.html), [https://stackoverflow.com](https://stackoverflow.com) . Projekty Apache také obsahují projektové weby [https://apache.org](https://apache.org) , například: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí REST API Apache Ambari](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použijte úlohy MapReduce s HDInsight](hadoop/hdinsight-use-mapreduce.md)