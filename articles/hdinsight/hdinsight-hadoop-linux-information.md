---
title: Tipy pro používání systému Hadoop v HDInsight se systémem Linux – Azure
description: Získejte implementace tipy pro používání Linuxovým systémem HDInsight (Hadoop) clustery ve známém prostředí Linux spuštěné v cloudu Azure.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/09/2018
ms.openlocfilehash: abaf69136fbed577095b3efba2ec6d4383907255
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385206"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informace o používání HDInsightu v Linuxu

Azure clustery HDInsight poskytují Apache Hadoop ve známém prostředí Linux spuštěné v cloudu Azure. Pro většinu toho, co by měl pracovat přesně jako jakékoliv jiné instalace Hadoop na Linuxu. Tento dokument, volá konkrétní rozdíly, které byste měli vědět.

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Požadavky

Celá řada kroků v tomto dokumentu pomocí následujících nástrojů, které může být nutné nainstalovat ve vašem systému.

* [cURL](https://curl.haxx.se/) – slouží ke komunikaci s webové služby.
* [jq](https://stedolan.github.io/jq/) – slouží k analýze dokumentů JSON.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) – používá se ke vzdálené správě služeb Azure.

## <a name="users"></a>Uživatelé

Není-li [připojených k doméně](./domain-joined/apache-domain-joined-introduction.md), by měly být považovány za HDInsight **jednoho uživatele** systému. Jeden uživatelský účet SSH se vytvoří s clusterem se oprávnění na úrovni správce. Další účty SSH je možné vytvářet, ale také mají přístup správce ke clusteru.

HDInsight připojený k doméně podporuje více uživatelů a podrobnější nastavení oprávnění a rolí. Další informace najdete v tématu [clusterů HDInsight připojených k doméně spravovat](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Názvy domén

Plně kvalifikovaný název domény (FQDN) pro použití při připojování ke clusteru z Internetu je  **&lt;Název_clusteru >. azurehdinsight.net** nebo (pro SSH pouze)  **&lt;název_clusteru-ssh >. azurehdinsight.NET**.

Interně každý uzel v clusteru má název, který se přiřadí během konfigurace clusteru. Názvy clusterů najdete v tématu **hostitele** stránce webové uživatelské rozhraní Ambari. Také vám pomůže následující seznam hostitelů z rozhraní Ambari REST API:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Nahraďte **CLUSTERNAME** názvem vašeho clusteru. Po zobrazení výzvy zadejte heslo pro účet správce. Tento příkaz vrátí dokument JSON, který obsahuje seznam hostitelů v clusteru. Jq slouží k extrakci `host_name` hodnota elementu pro každého hostitele.

Pokud je potřeba najít název uzlu pro konkrétní službu, můžete zadat dotaz na danou součást Ambari. Například pokud chcete najít hostitele pro název uzlu HDFS, použijte následující příkaz:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Tento příkaz vrátí dokument JSON popisující službu a pak jq si vyžádá pouze `host_name` hodnotu pro hostitele.

## <a name="remote-access-to-services"></a>Vzdálený přístup ke službám

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

    Ověřování pomocí Správce clusteru a heslo a pak se přihlaste k Ambari.

    Ověřování je ve formátu prostého textu – používejte vždy HTTPS, abyste zajistili, že připojení je zabezpečené.

    > [!IMPORTANT]
    > Některé z webovým uživatelským rozhraním, které jsou k dispozici prostřednictvím Ambari přístup k uzlům pomocí názvu interní domény. Interní doméně názvy nejsou veřejně přístupná přes internet. Můžete obdržet chyby "serveru nebyla nalezena" při pokusu o přístup k některé funkce přes Internet.
    >
    > Pokud chcete používat všechny funkce webové uživatelské rozhraní Ambari, použijte tunelového propojení SSH pro proxy webový provoz k hlavnímu uzlu clusteru. Zobrazit [používání tunelového propojení SSH pro přístup k webové uživatelské rozhraní Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie a dalším webovým uživatelským rozhraním](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;Název_clusteru >.azurehdinsight.net/ambari

    > [!NOTE]
    > Ověřování pomocí Správce clusteru a heslo.
    >
    > Ověřování je ve formátu prostého textu – používejte vždy HTTPS, abyste zajistili, že připojení je zabezpečené.

* **WebHCat (Templeton)** - https://&lt;Název_clusteru >.azurehdinsight.net/templeton

    > [!NOTE]
    > Ověřování pomocí Správce clusteru a heslo.
    >
    > Ověřování je ve formátu prostého textu – používejte vždy HTTPS, abyste zajistili, že připojení je zabezpečené.

* **SSH** - &lt;Název_clusteru >-ssh.azurehdinsight.net na portu 22 a 23. Port 22 se používá pro připojení k primárnímu hlavnímu uzlu, zatímco 23 se používá pro připojení k sekundární. Další informace o hlavních uzlech najdete v tématu [dostupnost a spolehlivost systému Apache Hadoop clusterů v HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]
    > Hlavní uzly clusteru prostřednictvím SSH můžete přistupovat pouze z klientského počítače. Po připojení se pak dostanete pracovní uzly pomocí protokolu SSH z hlavního uzlu.

Další informace najdete v tématu [portů používaných služeb Apache Hadoop v HDInsight](hdinsight-hadoop-port-settings-for-services.md) dokumentu.

## <a name="file-locations"></a>Umístění souborů

Soubory související s Hadoop můžete najít na uzly clusteru na `/usr/hdp`. Tento adresář obsahuje následující podadresáře:

* **2.2.4.9-1**: Název adresáře je verze datovou platformou Hortonworks používá HDInsight. Číslo ve vašem clusteru může být jiný než ten, který zde uvedené.
* **aktuální**: Tento adresář obsahuje odkazy na podadresářů **2.2.4.9-1** adresáře. Tento adresář existuje, takže nebudou muset pamatovat číslo verze.

Ukázková data a soubory JAR můžete najít na Hadoop Distributed File System na `/example` a `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-store"></a>HDFS, Azure Storage a Data Lake Store

Ve většině distribucí Hadoop jsou data uložená v HDFS, která je založená na místní úložiště na počítačích v clusteru. Pomocí místního úložiště může být nákladná řešení založené na cloudu ve kterém se účtují po hodinách nebo minutách za výpočetní prostředky.

Při použití HDInsight, se ukládají datové soubory způsobem škálovatelná a odolná proti selháním v cloudu s využitím úložiště objektů Blob v Azure a volitelně Azure Data Lake Store. Tyto služby poskytují následující výhody:

* Levné dlouhodobé uložení.
* Usnadnění přístupu z externích služeb, jako je například webů, nástrojů pro nahrávání a stahování souborů, různých sadách SDK pro jazyk a webové prohlížeče.
* Velký soubor kapacita a velké škálovatelné úložiště.

Další informace najdete v tématu [vysvětlení objektů blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) a [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Při použití služby Azure Storage nebo Data Lake Store, nemusíte dělat nic zvláštního z HDInsight pro přístup k datům. Například následující příkaz zobrazí seznam souborů v `/example/data` složku bez ohledu na to, zda je uložená v Azure Storage nebo Azure Data Lake Store:

    hdfs dfs -ls /example/data

V HDInsight jsou prostředky úložiště dat (Azure Blob Storage a Azure Data Lake Store) oddělené od výpočetních prostředků. Proto můžete vytvářet clustery HDInsight provést výpočet podle potřeby a po dokončení práce později odstranit cluster, mezitím udržování vašich datových souborů bezpečně zachována v cloudovém úložišti za předpokladu, budete potřebovat.

### <a name="uri-and-scheme"></a>Identifikátor URI a schéma

Některé příkazy mohou vyžadovat zadejte schéma jako součást identifikátoru URI při přístupu k souboru. Například komponenty Storm HDFS vyžaduje, abyste zadejte schéma. Pokud používáte jiné než výchozí storage (úložiště do clusteru přidat jako "Další" úložiště), musíte vždycky použít schéma jako součást identifikátoru URI.

Při použití __služby Azure Storage__, použijte jednu z následujících schémata identifikátoru URI:

* `wasb:///`: Výchozí úložiště s přístupem pomocí nešifrovaná komunikace.

* `wasbs:///`: Výchozí úložiště s přístupem pomocí šifrovanou komunikaci.  Schéma wasbs je podporováno pouze z HDInsight verze 3.6 a vyšší.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Použít při komunikaci s účtem služby storage jiné než výchozí. Například pokud máte ještě účet úložiště nebo při přístupu k datům uloženým v účtu úložiště veřejně přístupná.

Při použití __Data Lake Store__, použijte jednu z následujících schémata identifikátoru URI:

* `adl:///`: Přístup k výchozí Data Lake Store pro cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Použít při komunikaci s jiné než výchozí Data Lake Store. Také používá pro přístup k datům mimo kořenový adresář vašeho clusteru HDInsight.

> [!IMPORTANT]
> Při použití Data Lake Store jako výchozího úložiště pro HDInsight, musíte zadat cestu v rámci úložiště, které chcete použít jako kořen úložiště HDInsight. Výchozí cesta je `/clusters/<cluster-name>/`.
>
> Při použití `/` nebo `adl:///` pro přístup k datům, máte přístup jenom data uložená v kořenovém adresáři (například `/clusters/<cluster-name>/`) clusteru. Chcete-li získat přístup k datům kdekoli v úložišti, použijte `adl://<storage-name>.azuredatalakestore.net/` formátu.

### <a name="what-storage-is-the-cluster-using"></a>Úložiště používá cluster

Ambari slouží k načtení výchozí konfiguraci úložiště pro cluster. Použijte následující příkaz pro načtení pomocí příkazu curl informace o konfiguraci HDFS a filtrovat pomocí [jq](https://stedolan.github.io/jq/):

```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]  
> Tento příkaz vrátí první konfigurace pro server (`service_config_version=1`), která obsahuje tyto informace. Budete muset zobrazí seznam všech verzí konfigurace, abyste našli ten poslední.

Tento příkaz vrátí hodnotu podobně jako následující identifikátory URI:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` Pokud používáte účet služby Azure Storage.

    Název účtu je název účtu služby Azure Storage. Název kontejneru je kontejner objektů blob, který je kořenový adresář úložiště clusteru.

* `adl://home` Pokud používáte Azure Data Lake Store. Pokud chcete získat název Data Lake Store, použijte následující volání REST:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    Tento příkaz vrátí následující název hostitele: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Adresář v rámci úložiště, které je kořenový adresář pro HDInsight získáte pomocí následujícího volání REST:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    Tento příkaz vrátí cestu, podobně jako na následující cestu: `/clusters/<hdinsight-cluster-name>/`.

Můžete také najít informace o úložiště pomocí webu Azure portal pomocí následujících kroků:

1. V [webu Azure portal](https://portal.azure.com/), vyberte svůj cluster HDInsight.

2. Z **vlastnosti** vyberte **účty úložiště**. Zobrazí se informace o úložiště pro cluster.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Jak získám přístup k soubory z mimo HDInsight

Existují různé způsoby, jak získat přístup k datům z mimo HDInsight cluster. Následuje několik odkazů na nástroje a sady SDK, které lze použít pro práci s daty:

Pokud používáte __služby Azure Storage__, naleznete v tématu Možnosti, můžete přistupovat k datům prostřednictvím následujících odkazů:

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2): Příkazy rozhraní příkazového řádku pro práci s Azure. Po instalaci, použijte `az storage` příkaz nápovědu k používání úložiště, nebo `az storage blob` pro konkrétní objekt blob příkazy.
* [blobxfer.PY](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): Skript pythonu pro práci s objekty BLOB ve službě Azure Storage.
* Různých sadách SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Rozhraní REST API pro Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Pokud používáte __Azure Data Lake Store__, naleznete v tématu Možnosti, můžete přistupovat k datům prostřednictvím následujících odkazů:

* [Webový prohlížeč](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake Tools pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Škálování clusteru

Funkce škálování clusteru můžete dynamicky měnit počet datových uzlů používané clusterem. Můžete provádět operace škálování při dalším úlohám nebo procesy jsou spuštěny v clusteru.

Typy jiného clusteru jsou ovlivněny škálování následujícím způsobem:

* **Hadoop**: Během vertikálního snižování počtu uzlů v clusteru se restartují některé ze služeb v clusteru. Operace škálování může způsobit úlohy spuštěné nebo čeká na dokončení selhání po dokončení operace škálování. Neúspěšné úlohy po dokončení operace.
* **HBase**: Oblastní servery jsou automaticky rovnoměrně rozdělen během několika minut, po dokončení operace škálování. Ručně vyvážit místní servery, postupujte následovně:

    1. Připojte se ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Pomocí následujících spusťte prostředí HBase:

            hbase shell

    3. Po načtení prostředí HBase pomocí následujícího postupu ručně vyvážit místní servery:

            balancer

* **Storm**: Po provedení operace, která škálování byste měli obnovit rovnováhu všechny spuštěné topologie Storm. Opětovné vyvážení umožňuje přizpůsobit podle počtu uzlů v clusteru nové nastavení paralelismus topologii. Znovu vyvážit spuštěné topologie, použijte jednu z následujících možností:

    * **SSH**: Připojení k serveru a použijte následující příkaz obnovit rovnováhu topologie:

            storm rebalance TOPOLOGYNAME

        Můžete také zadat parametry přepsat pomocné parametry paralelismu původně poskytované topologie. Například `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` změní konfiguraci topologie 5 pracovních procesů, 3 vykonavatele pro součásti spout modrá a 10 vykonavatele pro součásti bolt žlutou barvou.

    * **Uživatelské rozhraní Storm**: Následujícím postupem obnovit rovnováhu topologie pomocí uživatelského rozhraní Storm.

        1. Otevřít **https://CLUSTERNAME.azurehdinsight.net/stormui** ve webovém prohlížeči, kde CLUSTERNAME představuje název clusteru Storm. Pokud se zobrazí výzva, zadejte název správce (správce) clusteru HDInsight a heslo, které jste zadali při vytváření clusteru.
        2. Vyberte topologii, kterou chcete obnovit rovnováhu a pak vyberte **obnovit rovnováhu** tlačítko. Zadejte zpoždění před provedením operace obnovení rovnováhy.

* **Kafka**: Po operacích škálování měli obnovit rovnováhu replik oddílů. Další informace najdete v tématu [vysoké dostupnosti dat s využitím Apache Kafka v HDInsight](./kafka/apache-kafka-high-availability.md) dokumentu.

Podrobnější informace o škálování clusteru HDInsight naleznete v tématu:

* [Spravovat clustery systému Apache Hadoop v HDInsight pomocí webu Azure portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Spravovat clustery systému Apache Hadoop v HDInsight pomocí Azure Powershellu](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Jak nainstalovat Hue (nebo jiné součásti systému Hadoop)?

HDInsight je spravovaná služba. Když Azure zjistí problém s clusterem, může odstranit uzel selhání a vytvořit uzel ji nahradit. Pokud nainstalujete ručně věci v clusteru, nejsou zachované při výskytu této operace. Místo toho použijte [akce skriptu HDInsight](hdinsight-hadoop-customize-cluster.md). Akce skriptu umožňuje provést následující změny:

* Instalace a konfigurace služby nebo webové stránky.
* Instalace a konfigurace komponenty, která vyžaduje změny konfigurace na několika uzlech v clusteru.

Akce skriptů jsou skripty Bash. Skripty spustit během vytváření clusteru a slouží k instalaci a konfiguraci dalších součástí. Příklady skriptů jsou k dispozici pro instalaci následující komponenty:

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Apache Solr](hdinsight-hadoop-solr-install-linux.md)

Informace o vývoji vlastních akcí skriptů naleznete v tématu [Vývoj akcí skriptů v prostředí HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Soubory JAR

Některé technologie Hadoop jsou k dispozici v souborech samostatný soubor jar, které obsahují funkce, které jsou použity jako součást úlohy MapReduce nebo z uvnitř Pigu a Hivu. Často není vyžadují žádné nastavení a mohou být odeslán do clusteru po vytvoření a používat přímo. Pokud chcete zajistit, aby že komponenta odolává obnovování z Image z clusteru, můžete uložit soubor jar do výchozího úložiště pro váš cluster (WASB nebo ADL).

Například, pokud chcete používat nejnovější verzi [DataFu](http://datafu.incubator.apache.org/), si můžete stáhnout soubor jar obsahující projekt a nahrajte ho do clusteru HDInsight. Potom postupujte podle dokumentace DataFu o tom, jak pomocí Pigu a Hivu.

> [!IMPORTANT]
> Některé součásti, které jsou samostatné soubory jar jsou k dispozici s HDInsight, ale nejsou v cestě. Pokud hledáte konkrétní součást, můžete ho vyhledat na svém clusteru těchto kroků:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Tento příkaz vrátí cestu odpovídající soubory jar.

Chcete-li použít různé verze komponenty, nahrajte verze a použít je ve svých úlohách.

> [!WARNING]
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované a Microsoft Support pomáhá izolovat a vyřešit problémy týkající se těchto součástí.
>
> Vlastní komponenty získat obchodně přiměřenou podporu můžete-li dále řešit tento problém. To může vést řeší problém nebo s výzvou k zapojení dostupné kanály pro open source technologie, ve kterých se nachází rozsáhlé znalosti pro tuto technologii. Existuje například mnoho komunitním webům, které lze použít jako: [Fórum na webu MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Také projektů Apache mít projektovým webům na [ http://apache.org ](http://apache.org), například: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

## <a name="next-steps"></a>Další postup

* [Migrace z HDInsight se systémem Windows do založených na Linuxu](hdinsight-migrate-from-windows-to-linux.md)
* [Použití Apache Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Apache Pig s HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití úloh MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)
