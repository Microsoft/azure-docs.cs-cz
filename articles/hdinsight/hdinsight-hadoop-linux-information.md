---
title: Tipy pro používání Hadoop v HDInsight se systémem Linux - Azure | Microsoft Docs
description: Získáte implementace tipy pro použití systémem Linux HDInsight (Hadoop) clusterů ve známém prostředí Linux spuštěné v cloudu Azure.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c41c611c-5798-4c14-81cc-bed1e26b5609
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 3ad7aa01200bf2bf4a63a380b2b883983c8622d6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405387"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informace o používání HDInsightu v Linuxu

Azure clustery HDInsight poskytují Hadoop na známém prostředí Linux, běží v cloudu Azure. Pro většinu věcí by měly fungovat přesně jako jiná instalace Hadoop na Linuxu. Tento dokument volá určité rozdíly, které byste měli vědět.

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Požadavky

Řada kroků v tomto dokumentu použít následující nástroje, které je třeba nainstalovat do systému.

* [cURL](https://curl.haxx.se/) – používá ke komunikaci s webových služeb
* [jq](https://stedolan.github.io/jq/) – používané k analýze dokumentů JSON
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) – používané vzdáleně spravovat služby Azure

## <a name="users"></a>Uživatelé

Pokud [připojený k doméně](./domain-joined/apache-domain-joined-introduction.md), měli byste zvážit HDInsight **jednoho uživatele** systému. Jeden uživatelský účet SSH je vytvořen s clusteru s úrovně oprávnění správce. Můžete vytvořit další účty SSH, ale také mají přístup správce ke clusteru.

Připojené k doméně HDInsight podporuje více uživatelů a podrobnější nastavení oprávnění a role. Další informace najdete v tématu [clustery HDInsight spravovat doméně](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Názvy domén

Plně kvalifikovaný název domény (FQDN) pro použití při připojování ke clusteru z Internetu je  **&lt;clustername >. azurehdinsight.net** nebo (pro SSH pouze)  **&lt;clustername-ssh >. azurehdinsight.net**.

Každý uzel v clusteru interně, má název, který se přiřadí během konfigurace clusteru. Názvy clusterů, najdete v tématu **hostitele** stránky na webové uživatelské rozhraní Ambari. K zobrazení seznamu hostitelů z Ambari REST API můžete použít také následující:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Nahraďte **CLUSTERNAME** názvem vašeho clusteru. Po zobrazení výzvy zadejte heslo pro účet správce. Tento příkaz vrátí dokument JSON, který obsahuje seznam hostitele v clusteru. Jq slouží k extrakci `host_name` hodnota elementu pro každého hostitele.

Pokud potřebujete najít název uzlu pro konkrétní službu, můžete dotazovat Ambari pro danou součást. Například pokud chcete najít hostitele pro název uzlu HDFS, použijte následující příkaz:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Tento příkaz vrátí dokumentu JSON s popisem služby, a potom jq lze posunout pouze `host_name` hodnotu pro hostitele.

## <a name="remote-access-to-services"></a>Vzdálený přístup ke službám

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

    Ověřování pomocí clusteru správce uživatele a heslo a potom se přihlaste k Ambari.

    Ověřování je ve formátu prostého textu – vždycky používají protokol HTTPS k zajištění, že připojení je bezpečné.

    > [!IMPORTANT]
    > Některé z webu uživatelská rozhraní, které jsou k dispozici prostřednictvím Ambari přístup uzly používá název interní domény. Interní doméně názvy nejsou veřejně přístupné přes internet. Při pokusu o přístup k některé funkce přes Internet, může se zobrazit chyby "server nebyl nalezen".
    >
    > Pokud chcete používat všechny funkce webovému uživatelskému rozhraní Ambari, pomocí tunelového propojení SSH pro přenosy webového proxy serveru k hlavnímu uzlu clusteru. V tématu [používání tunelového propojení SSH pro přístup k webovému uživatelskému rozhraní Ambari, ResourceManager, JobHistory, NameNode, Oozie a jiným webovým uživatelská rozhraní](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** -https://&lt;clustername >.azurehdinsight.net/ambari

    > [!NOTE]
    > Ověřování pomocí clusteru správce uživatele a heslo.
    >
    > Ověřování je ve formátu prostého textu – vždycky používají protokol HTTPS k zajištění, že připojení je bezpečné.

* **WebHCat (Templeton)** -https://&lt;clustername >.azurehdinsight.net/templeton

    > [!NOTE]
    > Ověřování pomocí clusteru správce uživatele a heslo.
    >
    > Ověřování je ve formátu prostého textu – vždycky používají protokol HTTPS k zajištění, že připojení je bezpečné.

* **SSH** - &lt;clustername >-ssh.azurehdinsight.net na port 22 a 23. Port 22 se používá pro připojení k primární headnode, přičemž 23 se používá pro připojení k sekundární. Další informace o hlavních uzlech naleznete v tématu [Dostupnost a spolehlivost Hadoop clusterů v HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]
    > Hlavního uzlu clusteru můžete přistupovat z počítače klienta pouze prostřednictvím SSH. Po připojení se pak dostanete pracovní uzly z headnode pomocí SSH.

Další informace najdete v tématu [porty používané služby Hadoop v HDInsight](hdinsight-hadoop-port-settings-for-services.md) dokumentu.

## <a name="file-locations"></a>Umístění souborů

Soubory související s Hadoop naleznete na uzly clusteru na `/usr/hdp`. Tento adresář obsahuje následující podadresáře:

* **2.2.4.9-1**: název adresáře je verze softwaru Hortonworks Data Platform, používá HDInsight. Číslo v clusteru může být jiný než ten, který tady.
* **aktuální**: obsahuje odkazy na podadresáře v tomto adresáři **2.2.4.9-1** adresáře. Tento adresář existuje, tak, aby je nemuseli pamatovat číslo verze.

Příklad dat a souborů JAR naleznete na Hadoop Distributed File System na `/example` a `/HdiSamples`

## <a name="hdfs-azure-storage-and-data-lake-store"></a>HDFS, úložiště Azure a Data Lake Store

Ve většině distribuce Hadoop HDFS je zajištěna podpora místní úložiště na počítačích v clusteru. Používá místní úložiště může být drahé pro cloudové řešení kde budou účtovat každou hodinu nebo minutu pro výpočetní prostředky.

HDInsight používá jako výchozí úložiště buď objektů BLOB v Azure Storage nebo Azure Data Lake Store. Tyto služby poskytovat následující výhody:

* Levných dlouhodobé uložení
* Usnadnění přístupu z externích služeb například weby, nástroje pro odeslání nebo stažení souboru, různých sadách SDK jazyka a webových prohlížečů

Účet úložiště Azure mohou být uloženy až 4.75 TB, i když jednotlivé objekty BLOB (nebo soubory z hlediska HDInsight) může použít pouze až 195 GB. Azure Data Lake Store dynamicky růst, aby udržení bilión soubory s jednotlivé soubory větší než petabajty. Další informace najdete v tématu [vysvětlení objektů blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) a [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Pokud používáte Azure Storage nebo Data Lake Store, nemusíte provádět žádné zvláštní z prostředí HDInsight k přístupu k datům. Například následující příkaz seznam souborů `/example/data` složky bez ohledu na to, jestli je uložené v Azure Storage nebo Data Lake Store:

    hdfs dfs -ls /example/data

### <a name="uri-and-scheme"></a>Identifikátor URI a schéma

Některé příkazy mohou vyžadovat zadejte schéma jako součást identifikátoru URI při přístupu k souboru. Například komponentu Storm HDFS vyžaduje, abyste zadejte schéma. Pokud používáte jiné než výchozí úložiště (úložiště přidat jako "Další" úložiště do clusteru), musíte vždycky použít schéma jako součást identifikátoru URI.

Při použití __Azure Storage__, použijte jednu z následujících schémat URI:

* `wasb:///`: Přístup k úložišti výchozí pomocí nešifrovaná komunikace.

* `wasbs:///`: Přístup k úložišti výchozí pomocí šifrovanou komunikaci.  Schéma wasbs je podporováno pouze z HDInsight verze 3.6 a vyšší.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Použít při komunikaci s účtem storage jiné než výchozí. Například pokud máte účet další úložiště nebo pokud přístup k datům uložený v účtu úložiště veřejně přístupná.

Při použití __Data Lake Store__, použijte jednu z následujících schémat URI:

* `adl:///`: Přístup k výchozí Data Lake Store pro cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Použít při komunikaci s jiné než výchozí Data Lake Store. Také se používá pro přístup k datům mimo kořenový adresář clusteru HDInsight.

> [!IMPORTANT]
> Při použití Data Lake Store jako výchozí úložiště pro HDInsight, je nutné zadat cestu v rámci úložiště, které chcete použít jako kořen úložiště HDInsight. Výchozí cesta je `/clusters/<cluster-name>/`.
>
> Při použití `/` nebo `adl:///` přístup k datům přístupné pouze data uložená v kořenovém adresáři (například `/clusters/<cluster-name>/`) clusteru. Pro přístup k datům libovolné místo v úložišti, použijte `adl://<storage-name>.azuredatalakestore.net/` formátu.

### <a name="what-storage-is-the-cluster-using"></a>Jaké úložiště clusteru používá

Ambari slouží k načtení výchozí konfiguraci úložiště pro cluster. Použijte následující příkaz k načtení pomocí curl HDFS informace o konfiguraci a filtrovat pomocí [jq](https://stedolan.github.io/jq/):

```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]
> Tento příkaz vrátí první konfigurace platí na serveru (`service_config_version=1`), která obsahuje tyto informace. Budete muset všechny verze konfigurace vyhledat nejnovější seznam.

Tento příkaz vrátí hodnotu podobná následující identifikátory URI:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` Pokud používáte účet úložiště Azure.

    Název účtu je název účtu úložiště Azure. Název kontejneru je kontejneru blobů, který je kořenem úložiště clusteru.

* `adl://home` Pokud používáte Azure Data Lake Store. Název Data Lake Store, použijte následující volání REST:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    Tento příkaz vrátí následující název hostitele: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Adresář v rámci úložiště, který je kořenem pro HDInsight, použijte následující volání REST:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    Tento příkaz vrátí cestu podobná následující cestu: `/clusters/<hdinsight-cluster-name>/`.

Můžete také najít informace o úložiště pomocí portálu Azure pomocí následujících kroků:

1. V [portál Azure](https://portal.azure.com/), vyberte clusteru HDInsight.

2. Z **vlastnosti** vyberte **účty úložiště**. Zobrazí se informace úložiště pro cluster.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Přístupu soubory z mimo HDInsight

Existují různé způsoby pro přístup k datům z mimo HDInsight cluster. Následuje několik odkazů na nástroje a sady SDK, které lze použít pro práci s daty:

Pokud používáte __Azure Storage__, najdete v následujících tématech pro způsoby, můžete přístup k datům:

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2): příkazy rozhraní příkazového řádku pro práci s Azure. Po instalaci, pomocí `az storage` příkaz nápovědu k použití úložiště, nebo `az storage blob` pro objekt blob specifické příkazy.
* [blobxfer.PY](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): python script pro práci s objekty BLOB ve službě Azure Storage.
* Různých sadách SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Rozhraní REST API pro Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Pokud používáte __Azure Data Lake Store__, najdete v následujících tématech pro způsoby, můžete přístup k datům:

* [Webový prohlížeč](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI 2.0](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [Rozhraní REST API WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Nástroje data Lake pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Škálování clusteru

Funkce škálování clusteru umožňuje dynamicky měnit počet uzlů dat používá cluster. Můžete provádět operace škálování při dalších úloh nebo procesů běží na clusteru.

Typy jiného clusteru se týká škálování následujícím způsobem:

* **Hadoop**: při škálování se počet uzlů v clusteru, jsou restartovány některé služby v clusteru. Operace škálování může způsobit úlohy spuštěná nebo čeká na dokončení selhání po dokončení operace škálování. Úlohy můžete znovu odeslat, po dokončení operace.
* **HBase**: místní servery jsou automaticky vyváženy během několika minut, po dokončení operace škálování. Chcete-li ručně vyvážit místní servery, použijte následující kroky:

    1. Připojte ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Ke spuštění prostředí HBase, použijte následující:

            hbase shell

    3. Jakmile se načetl prostředí HBase, použijte následující postupy ručně vyvážit místní servery:

            balancer

* **Storm**: musíte znovu vyvážit všechny spuštěné topologie Storm po nebyla provedena škálování operace. Vyrovnává umožňuje topologii paralelismus nastavení na základě nové počtu uzlů v clusteru se znovu. Znovu vyvážit spuštěné topologie, použijte jednu z následujících možností:

    * **SSH**: připojení k serveru a znovu vyvážit topologii pomocí následujícího příkazu:

            storm rebalance TOPOLOGYNAME

        Můžete také zadat parametry k přepsání pomocné parametry paralelismus původně poskytované topologii. Například `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` změní konfiguraci topologie 5 pracovních procesů, 3 vykonavatelů pro komponentu blue spout a 10 vykonavatelů pro komponentu žlutý bolt.

    * **Uživatelské rozhraní Storm**: Chcete-li znovu vyvážit topologii pomocí uživatelského rozhraní Storm použijte následující postup.

        1. Otevřete **https://CLUSTERNAME.azurehdinsight.net/stormui** ve webovém prohlížeči, kde CLUSTERNAME představuje název clusteru Storm. Pokud se zobrazí výzva, zadejte název správce (správce) clusteru HDInsight a heslo, které jste zadali při vytváření clusteru.
        2. Vyberte topologii chcete znovu vyvážit a pak vyberte **znovu vyvážit** tlačítko. Zadejte zpoždění před provedením operace obnovte rovnováhu.

* **Kafka**: musíte znovu vyvážit repliky oddílu po operace škálování. Další informace najdete v tématu [vysokou dostupnost dat s Kafka v HDInsight](./kafka/apache-kafka-high-availability.md) dokumentu.

Konkrétní informace o škálování clusteru HDInsight naleznete v tématu:

* [Správa clusterů systému Hadoop v HDInsight pomocí portálu Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Správa clusterů systému Hadoop v HDInsight pomocí prostředí Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Jak nainstalovat Hue (nebo jiné součásti Hadoop)?

HDInsight je spravované služby. Pokud Azure zjistí problém s clusterem, může uzel selhání odstranit a vytvořit uzel ho nahradit. Pokud nainstalujete ručně věcí v clusteru, nejsou trvalé v případě této operace. Místo toho použijte [akce skriptu HDInsight](hdinsight-hadoop-customize-cluster.md). Akce skriptu lze provést následující změny:

* Nainstalujte a nakonfigurujte službu nebo webu.
* Instalace a konfigurace komponenty, která vyžaduje změny konfigurace ve více uzlech v clusteru.

Akce skriptů jsou skripty Bash. Tyto skripty spustit při vytváření clusteru a slouží k instalaci a konfiguraci dalších součástí. Příklad skriptů jsou k dispozici pro instalaci následující součásti:

* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Informace o vývoji vlastních akcí skriptů naleznete v tématu [Vývoj akcí skriptů v prostředí HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Souborů JAR

Některé technologie Hadoop jsou uvedeny v nezávislý jar souborů, které obsahují funkce, které jsou použity jako součást úlohy MapReduce, nebo z uvnitř Pig nebo Hive. Často nemáte vyžadují žádné nastavení a mohou být nahrán do clusteru po vytvoření a použít přímo. Pokud chcete, aby zajistil, že součást odolává obnovování clusteru, můžete uložit na soubor jar ve výchozím nastavení úložiště pro váš cluster (WASB nebo ADL).

Například, pokud chcete použít nejnovější verzi [DataFu](http://datafu.incubator.apache.org/), můžete stáhnout jar obsahující projekt a nahrajte ho do clusteru HDInsight. Potom postupujte podle dokumentace DataFu o tom, jak používat z Pig nebo Hive.

> [!IMPORTANT]
> Některé součásti, které jsou samostatné jar soubory jsou k dispozici s HDInsight, ale nejsou v cestě. Pokud hledáte konkrétní součást, můžete ji najít v clusteru těchto kroků:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Tento příkaz vrátí cestu odpovídající jar soubory.

Použití jiné verze součásti, nahrajte verze potřebujete a použít v úlohách.

> [!WARNING]
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované a Microsoft Support pomáhá izolovat a vyřešení problémů týkajících se těchto součástí.
>
> Vlastní komponenty získat vyvineme podporu k pomoci při další řešení problému. To může způsobit řešení problému nebo s žádostí o zapojení dostupné kanály pro technologie s otevřeným zdrojem, kterých se nachází hluboké znalosti pro tuto technologii. Například existuje mnoho komunity webů, které lze použít jako: [fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Také Apache projekty mají na projektu serverů [ http://apache.org ](http://apache.org), například: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

## <a name="next-steps"></a>Další postup

* [Migrace z HDInsight se systémem Windows do systémem Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Použití Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití úloh MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)
