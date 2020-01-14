---
title: Cluster setup for Apache and R Server in Azure HDInsight
description: Set up Hadoop, Kafka, Spark, HBase, R Server, or Storm clusters for HDInsight from a browser, the Azure classic CLI, Azure PowerShell, REST, or SDK.
keywords: hadoop cluster setup, kafka cluster setup, spark cluster setup, what is cluster in hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 382205a958030d2a6d1c199627a591978ef8708a
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934610"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Set up clusters in HDInsight with Apache Hadoop, Apache Spark, Apache Kafka, and more

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Learn how to set up and configure clusters in HDInsight with Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services, or Apache Storm. Also, learn how to customize clusters and add security by joining them to a domain.

A Hadoop cluster consists of several virtual machines (nodes) that are used for distributed processing of tasks. Azure HDInsight handles implementation details of installation and configuration of individual nodes, so you only have to provide general configuration information.

> [!IMPORTANT]  
> Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. Learn how to [delete a cluster.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Cluster setup methods

The following table shows the different methods you can use to set up an HDInsight cluster.

| Clusters created with | Webový prohlížeč | Příkazový řádek | Rozhraní REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="basic-cluster-setup"></a>Basic cluster setup

This article walks you through setup in the [Azure portal](https://portal.azure.com), where you can create an HDInsight cluster using the default view or *Classic*.

![hdinsight create options custom quick create](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

Follow instructions on the screen. Details are provided below for:

* [Resource group name](#resource-group-name)
* [Cluster types and configuration](#cluster-types)
* [Název clusteru](#cluster-name)
* [Cluster login and SSH username](#cluster-login-and-ssh-username)
* [Umístění](#location)

## <a name="resource-group-name"></a>Název skupiny prostředků

[Azure Resource Manager](../azure-resource-manager/management/overview.md) helps you work with the resources in your application as a group, referred to as an Azure resource group. You can deploy, update, monitor, or delete all the resources for your application in a single coordinated operation.

## <a name="cluster-types"></a> Cluster types and configuration

Azure HDInsight currently provides the following cluster types, each with a set of components to provide certain functionalities.

> [!IMPORTANT]  
> HDInsight clusters are available in various types, each for a single workload or technology. There is no supported method to create a cluster that combines multiple types, such as Storm and HBase on one cluster. Pokud vaše řešení vyžaduje technologie, které jsou rozdělené mezi více typů clusterů HDInsight, může [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network) propojit požadované typy clusterů.

| Typ clusteru | Funkce |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batch query and analysis of stored data |
| [HBase](hbase/apache-hbase-overview.md) |Processing for large amounts of schemaless, NoSQL data |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |In-memory caching for interactive and faster Hive queries |
| [Kafka](kafka/apache-kafka-introduction.md) | A distributed streaming platform that can be used to build real-time streaming data pipelines and applications |
| [ML Services](r-server/r-server-overview.md) |Various big data statistics, predictive modeling, and machine learning capabilities |
| [Spark](spark/apache-spark-overview.md) |In-memory processing, interactive queries, micro-batch stream processing |
| [Storm](storm/apache-storm-overview.md) |Zpracování událostí v reálném čase |

### <a name="hdinsight-version"></a>HDInsight version

Choose the version of HDInsight for this cluster. For more information, see [Supported HDInsight versions](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="cluster-name"></a>Název clusteru

HDInsight cluster names have the following restrictions:

* Allowed characters: a-z, 0-9, A-Z
* Max length: 59
* Reserved names: apps
* The cluster naming scope is for all Azure, across all subscriptions. So the cluster name must be unique worldwide.
* First six characters must be unique within a VNET

## <a name="cluster-login-and-ssh-username"></a>Přihlášení ke clusteru a uživatelské jméno SSH

Clustery HDInsight umožňují během vytváření clusteru konfigurovat dva uživatelské účty:

* Uživatel HTTP: výchozí uživatelské jméno je *admin*. Používá základní konfiguraci na Azure Portal. V některých případech se nazývá "uživatel clusteru".
* Uživatel SSH: slouží k připojení ke clusteru přes SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Uživatelské jméno protokolu HTTP má následující omezení:

* Povolené speciální znaky: _ a @
* Nepovolené znaky: #;. ",\/:! *? $ (){}[] < > | &--= +% ~ ^ Space
* Maximální délka: 20

Uživatelské jméno SSH má následující omezení:

* Povolené speciální znaky: _ a @
* Nepovolené znaky: #;. ",\/:! *? $ (){}[] < > | &--= +% ~ ^ Space
* Maximální délka: 64
* Rezervované názvy: Hadoop, uživatelé, Oozie, podregistr, mapred, Ambari-QA, Zookeeper, tez, HDFS, Sqoop, příze, hcat, AMS, HBA, doplňování, správce, správce, uživatel, uživatel1, test, uživatel2, test1, user3, admin1, 1, 123, a, ACTUser, ADM, admin2, ASPNET, Backup, Console, David, Host, Jan, Owner, root, server, SQL, podpora, support_388945a0, sys, test2, test3, user4, user5, Spark

Balíček Enterprise Security umožňuje integrovat HDInsight se službou Active Directory a Apache Ranger. Pomocí balíčku podnikového zabezpečení lze vytvořit více uživatelů.

## <a name="location"></a>Umístění (oblastí) pro clustery a úložiště

Umístění clusteru nemusíte explicitně určovat: cluster je ve stejném umístění jako výchozí úložiště. Seznam podporovaných oblastí získáte tak, že kliknete na rozevírací seznam **oblast** na [ceny služby HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Koncové body úložiště pro clustery

I když místní instalace systému Hadoop používá pro úložiště v clusteru systém souborů DFS (Distributed File System) Hadoop (HDFS), v cloudu použijete koncové body úložiště připojené ke clusteru. Použití cloudového úložiště znamená, že můžete bezpečně odstranit clustery HDInsight používané pro výpočty a přitom zachovat data.

Clustery HDInsight můžou používat následující možnosti úložiště:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage Pro obecné účely v2
* Azure Storage Pro obecné účely v1
* Objekt blob bloku Azure Storage (**podporuje se jenom jako sekundární úložiště**)

Další informace o možnostech úložiště ve službě HDInsight najdete v tématu [porovnání možností úložiště pro použití s clustery Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Použití dalšího účtu úložiště v jiném umístění z clusteru HDInsight se nepodporuje.

Během konfigurace pro výchozí koncový bod úložiště zadáte kontejner objektů blob Azure Storage účtu nebo Data Lake Storage. Výchozí úložiště obsahuje protokoly aplikací a systému. Volitelně můžete zadat další propojené účty Azure Storage a účty Data Lake Storage, ke kterým může cluster přistupovat. Cluster HDInsight a závislé účty úložiště musí být ve stejném umístění Azure.

![Nastavení úložiště clusteru: koncové body úložiště kompatibilní s HDFS](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage-blank.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="optional-metastores"></a>Volitelné metaúložiště

Můžete vytvořit volitelný podregistr nebo Apache Oozie metaúložiště. Ne všechny typy clusterů ale podporují metaúložiště a Azure SQL Data Warehouse nejsou kompatibilní s metaúložiště.

Další informace najdete v tématu [použití externích úložišť metadat ve službě Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Když vytvoříte vlastní metastore, nepoužívejte v názvu databáze pomlčky, spojovníky nebo mezery. To může způsobit selhání procesu vytváření clusteru.

### <a name="use-hiveoozie-metastore"></a>metastore Hive

Pokud chcete uchovat tabulky podregistru po odstranění clusteru HDInsight, použijte vlastní metastore. Pak můžete metastore připojit k jinému clusteru HDInsight.

An HDInsight metastore, která je vytvořená pro jednu verzi clusteru HDInsight, nejde sdílet mezi různými verzemi clusterů HDInsight. Seznam verzí služby HDInsight najdete v tématu [podporované verze služby HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Oozie metastore

Pokud chcete zvýšit výkon při použití Oozie, použijte vlastní metastore. Metastore může také poskytnout přístup k datům úlohy Oozie po odstranění clusteru.

> [!IMPORTANT]  
> Nemůžete znovu použít vlastní Oozie metastore. Pokud chcete použít vlastní metastore Oozie, musíte při vytváření clusteru HDInsight zadat prázdnou Azure SQL Database.

## <a name="enterprise-security-package"></a>Balíček podnikového zabezpečení

Pro typy clusterů Hadoop, Spark, HBA, Kafka a interaktivní dotaz můžete povolit **balíček zabezpečení podniku**. Tento balíček nabízí možnost mít bezpečnější instalaci clusteru pomocí Apache Ranger a integraci s Azure Active Directory. Další informace najdete v tématu [Přehled podnikového zabezpečení ve službě Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

![možnosti vytvoření HDInsight výběr balíčku podnikového zabezpečení](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking-esp.png)

Další informace o vytvoření clusteru HDInsight připojeného k doméně najdete v tématu [vytvoření prostředí sandboxu HDInsight připojeného k doméně](./domain-joined/apache-domain-joined-configure.md).

## <a name="extend-clusters-with-a-virtual-network"></a>Rozšiřování clusterů pomocí virtuální sítě

Pokud vaše řešení vyžaduje technologie, které jsou rozdělené mezi více typů clusterů HDInsight, může [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network) propojit požadované typy clusterů. Tato konfigurace umožňuje clusterům a veškerý kód, který do nich nasazujete, k přímé komunikaci.

Další informace o používání služby Azure Virtual Network s HDInsight najdete v tématu [plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

Příklad použití dvou typů clusterů v rámci virtuální sítě Azure najdete v tématu [použití Apache Spark strukturovaného streamování s Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Další informace o používání služby HDInsight s virtuální sítí, včetně specifických požadavků na konfiguraci pro virtuální síť, najdete v tématu [plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="configure-cluster-size"></a>Konfigurace velikosti clusteru

Účtuje se vám využití uzlu, dokud cluster existuje. Fakturace začne při vytvoření clusteru a zastaví se při jeho odstranění. Clustery nemůžou být nepřidělené nebo se zablokují.

### <a name="number-of-nodes-for-each-cluster-type"></a>Počet uzlů pro každý typ clusteru

Každý typ clusteru má svůj vlastní počet uzlů, terminologii pro uzly a výchozí velikost virtuálního počítače. V následující tabulce je počet uzlů pro každý typ uzlu v závorkách.

| Typ | Uzly | Diagram |
| --- | --- | --- |
| Hadoop |Hlavní uzel (2), pracovní uzel (1 +) |![Uzly clusteru HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Hlavní server (2), server oblasti (1 +), hlavní/ZooKeeper uzel (3) |![Instalace typu clusteru HDInsight HBA](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus uzel (2), server pro řízení (1 +), ZooKeeper uzel (3) |![Nastavení typu clusteru HDInsight v HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Hlavní uzel (2), pracovní uzel (1 +), uzel ZooKeeper (3) (volné pro velikost virtuálního počítače a1 ZooKeeper) |![Nastavení typu clusteru HDInsight Spark](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Další informace najdete v tématu [Konfigurace výchozích uzlů a velikosti virtuálních počítačů pro clustery](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) v tématu Co jsou komponenty Hadoop a verze v HDInsight?.

Náklady na clustery HDInsight se určují podle počtu uzlů a velikosti virtuálních počítačů pro uzly.

Různé typy clusterů mají různé typy uzlů, počty uzlů a velikosti uzlů:
* Výchozí typ clusteru Hadoop:
    * Dva *hlavní uzly*  
    * Čtyři *pracovní uzly*
* Výchozí typ clusteru pro hodnoty:
    * Dva *uzly Nimbus*
    * Tři *uzly Zookeeper*
    * Čtyři *uzly Správce*

Pokud jste právě zkoušeli HDInsight, doporučujeme použít jeden pracovní uzel. Další informace o cenách služby HDInsight najdete v tématu [ceny služby HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Omezení velikosti clusteru se liší v rámci předplatných Azure. Pokud chcete tento limit zvýšit, kontaktujte [podporu fakturace Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) .

Když použijete Azure Portal ke konfiguraci clusteru, velikost uzlu je k dispozici na kartě **Konfigurace + ceny** . Na portálu můžete také zobrazit náklady spojené s různými velikostmi uzlů.

![HDInsight Volba velikosti uzlu](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration-pricing-hadoop.png)

### <a name="virtual-machine-sizes"></a>Velikosti virtuálních počítačů

Při nasazení clusterů vyberte výpočetní prostředky na základě řešení, které plánujete nasadit. Pro clustery HDInsight se používají následující virtuální počítače:

* Virtuální počítače řady a a D1-4: [velikosti virtuálních počítačů se systémem Linux pro obecné účely](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Virtuální počítač řady D11-14: [paměťově optimalizované velikosti virtuálních počítačů Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Pokud chcete zjistit, jakou hodnotu byste měli použít k určení velikosti virtuálního počítače při vytváření clusteru pomocí různých sad SDK nebo při použití Azure PowerShell, přečtěte si téma [velikosti virtuálních počítačů, které se mají použít pro clustery HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). Z tohoto odkazovaného článku použijte hodnotu ve sloupci **Velikost** v tabulkách.

> [!IMPORTANT]  
> Pokud v clusteru potřebujete víc než 32 pracovních uzlů, musíte vybrat velikost hlavního uzlu s aspoň 8 jádry a 14 GB paměti RAM.

Další informace najdete v tématu [velikosti virtuálních počítačů](../virtual-machines/windows/sizes.md). Informace o cenách různých velikostí najdete v tématu ceny služby [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

## <a name="classic-cluster-setup"></a>Instalace klasického clusteru

Instalace klasického clusteru vytvoří výchozí nastavení pro vytvoření a přidá následující možnosti:

* [Aplikace HDInsight](#install-hdinsight-applications-on-clusters)
* [Akce skriptů](#advanced-settings-script-actions)

## <a name="install-hdinsight-applications-on-clusters"></a>Instalace aplikací HDInsight v clusterech

Aplikace HDInsight je aplikace, kterou uživatelé mohou nainstalovat na clusteru HDInsight se systémem Linux. Můžete používat aplikace od Microsoftu, třetích stran nebo si sami vyvíjíte. Další informace najdete v tématu [instalace aplikací Apache Hadoop třetích stran v Azure HDInsight](hdinsight-apps-install-applications.md).

Většina aplikací HDInsight je nainstalovaná na prázdném hraničním uzlu.  Prázdný hraniční uzel je virtuální počítač se systémem Linux se stejnými klientskými nástroji, který je nainstalovaný a nakonfigurovaný jako hlavní uzel. Hraniční uzel můžete použít pro přístup ke clusteru, testování klientských aplikací a hostování klientských aplikací. Další informace najdete v tématu [použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>Rozšířená nastavení: akce skriptu

Během vytváření můžete nainstalovat další součásti nebo přizpůsobit konfiguraci clusteru pomocí skriptů. Tyto skripty jsou vyvolány prostřednictvím **akce skriptu**, což je možnost konfigurace, kterou lze použít z Azure Portal, rutin prostředí Windows PowerShell pro HDInsight nebo sady HDInsight .NET SDK. Další informace najdete v tématu [přizpůsobení clusteru HDInsight pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

Některé nativní komponenty Java, jako je Apache Mahout a Cascading, se dají spouštět v clusteru jako soubory v archivu Java (JAR). These JAR files can be distributed to Azure Storage and submitted to HDInsight clusters with Hadoop job submission mechanisms. For more information, see [Submit Apache Hadoop jobs programmatically](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> If you have issues deploying JAR files to HDInsight clusters, or calling JAR files on HDInsight clusters, contact [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> Cascading is not supported by HDInsight and is not eligible for Microsoft Support. For lists of supported components, see [What's new in the cluster versions provided by HDInsight](hdinsight-component-versioning.md).

Sometimes, you want to configure the following configuration files during the creation process:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

For more information, see [Customize HDInsight clusters using Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Další kroky

* [Troubleshoot cluster creation failures with Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [What are HDInsight, the Apache Hadoop ecosystem, and Hadoop clusters?](hadoop/apache-hadoop-introduction.md)
* [Get started using Apache Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Work in Apache Hadoop on HDInsight from a Windows PC](hdinsight-hadoop-windows-tools.md)
