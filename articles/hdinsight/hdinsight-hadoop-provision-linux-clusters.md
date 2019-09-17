---
title: Nastavení clusteru pro Apache Hadoop, Spark, Kafka, HBA nebo R Server – Azure
description: Nastavte clustery Hadoop, Kafka, Spark, HBA, R Server nebo neplní clustery pro HDInsight z prohlížeče, rozhraní příkazového řádku Azure Classic, Azure PowerShell, REST nebo sady SDK.
keywords: instalace clusteru Hadoop, instalace clusteru Kafka, instalace clusteru Spark, co je cluster v Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: 0f29df02e8242872311df3d4cb660d46bbc2cee3
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018789"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Nastavení clusterů v HDInsight pomocí Apache Hadoop, Apache Spark, Apache Kafka a dalších

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Naučte se, jak nastavit a nakonfigurovat clustery v HDInsight pomocí Apache Hadoop, Apache Spark, Apache Kafka, interaktivních dotazů, Apache HBA, služeb ML nebo Apache Storm. Přečtěte si také, jak přizpůsobit clustery a přidat zabezpečení jejich připojením k doméně.

Cluster Hadoop se skládá z několika virtuálních počítačů (uzlů), které se používají pro distribuované zpracování úloh. Azure HDInsight zpracovává podrobnosti o implementaci instalace a konfigurace jednotlivých uzlů, takže stačí zadat obecné informace o konfiguraci.

> [!IMPORTANT]  
> Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. Naučte se, jak [Odstranit cluster.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Metody instalace clusteru
V následující tabulce jsou uvedeny různé metody, které můžete použít k nastavení clusteru HDInsight.

| Clustery vytvořené pomocí | Webový prohlížeč | Příkazový řádek | REST API | Sada SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Šablony Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Rychlé vytvoření: Základní nastavení clusteru
Tento článek vás provede instalačním programem v [Azure Portal](https://portal.azure.com), kde můžete vytvořit cluster HDInsight pomocí funkce *rychle vytvořit* nebo *vlastní*. 

![možnosti vytvoření vlastního rychlého vytvoření v HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-options.png)

Postupujte podle pokynů na obrazovce a proveďte základní instalaci clusteru. Níže jsou uvedeny podrobnosti pro:

* [Název skupiny prostředků](#resource-group-name)
* [Typy a konfigurace clusteru](#cluster-types) 
* [Název clusteru](#cluster-name)
* [Přihlášení ke clusteru a uživatelské jméno SSH](#cluster-login-and-ssh-username)
* [Location](#location)

## <a name="resource-group-name"></a>Název skupiny prostředků

[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vám pomůže pracovat s prostředky v aplikaci jako se skupinou, která se označuje jako skupina prostředků Azure. Všechny prostředky pro aplikaci můžete nasadit, aktualizovat, monitorovat nebo odstranit v rámci jediné koordinované operace.

## <a name="cluster-types"></a>Typy a konfigurace clusteru
Azure HDInsight v současné době poskytuje následující typy clusterů, z nichž každá obsahuje sadu součástí, které poskytují určité funkce.

> [!IMPORTANT]  
> Clustery HDInsight jsou k dispozici v různých typech, každou pro jednu úlohu nebo technologii. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů, jako je například zaplavení a HBA v jednom clusteru. Pokud vaše řešení vyžaduje technologie, které jsou rozdělené mezi více typů clusterů HDInsight, může [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network) propojit požadované typy clusterů. 

| Typ clusteru | Funkce |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Dávkové dotazování a analýza uložených dat |
| [HBase](hbase/apache-hbase-overview.md) |Zpracování velkých objemů NoSQL dat bez schématu |
| [Interaktivní dotaz](./interactive-query/apache-interactive-query-get-started.md) |Ukládání do mezipaměti v paměti pro interaktivní a rychlejší dotazy na podregistr |
| [Kafka](kafka/apache-kafka-introduction.md) | Platforma pro distribuované streamování, která se dá použít k sestavení datových kanálů a aplikací streamování v reálném čase |
| [Služby ML](r-server/r-server-overview.md) |Různé statistiky s velkými objemy dat, prediktivní modelování a možnosti strojového učení |
| [Spark](spark/apache-spark-overview.md) |Zpracování v paměti, interaktivní dotazy, zpracování streamu mikrodávkování |
| [Chráněn](storm/apache-storm-overview.md) |Zpracování událostí v reálném čase |


### <a name="hdinsight-version"></a>Verze HDInsight
Vyberte verzi HDInsight pro tento cluster. Další informace najdete v tématu [podporované verze HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="cluster-name"></a>Název clusteru

Názvy clusterů HDInsight mají následující omezení:
- Povolené znaky: a-z, 0-9, A až Z 
- Maximální délka: 59
- Rezervované názvy: aplikace
- Obor názvů clusteru je pro všechny Azure v rámci všech předplatných. Proto musí být název clusteru jedinečný po celém světě.
- Prvních 6 znaků musí být v rámci virtuální sítě jedinečné.

## <a name="cluster-login-and-ssh-username"></a>Přihlášení ke clusteru a uživatelské jméno SSH
Clustery HDInsight umožňují během vytváření clusteru konfigurovat dva uživatelské účty:

* Uživatel HTTP: Výchozí uživatelské jméno *admin*. Používá základní konfiguraci na Azure Portal. V některých případech se nazývá "uživatel clusteru".
* Uživatel SSH: Slouží k připojení ke clusteru přes SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Uživatelské jméno protokolu HTTP má následující omezení:
- Povolené speciální znaky: _ a @ 
- Nepovolené znaky: #;. ",\/: '! *? $ (){}[] < > | &--= +% ~ ^ Space
- Maximální délka: 20

Uživatelské jméno SSH má následující omezení:
- Povolené speciální znaky: _ a @ 
- Nepovolené znaky: #;. ",\/: '! *? $ (){}[] < > | &--= +% ~ ^ Space
- Maximální délka: 64
- Rezervované názvy: Hadoop, uživatelé, Oozie, podregistr, mapred, Ambari-QA, Zookeeper, tez, HDFS, Sqoop, příze, hcat, AMS, HBA, doplňování, správce, správce, uživatel, uživatel1, test, uživatel2, test1, user3, admin1, 1, 123, a, ACTUser, ADM, admin2, ASPNET, Backup, Console, David, Host, Jan, vlastník, root, server, SQL, podpora, Support_388945a0, sys, test2, test3, user4, user5, Spark

Balíček Enterprise Security umožňuje integrovat HDInsight se službou Active Directory a Apache Ranger. Pomocí balíčku podnikového zabezpečení lze vytvořit více uživatelů.

## <a name="location"></a>Umístění (oblastí) pro clustery a úložiště

Nemusíte explicitně určovat umístění clusteru: Cluster je ve stejném umístění jako výchozí úložiště. Seznam podporovaných oblastí získáte tak, že kliknete na rozevírací seznam **oblast** na [ceny služby HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

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

![Nastavení úložiště clusteru: HDFS – kompatibilní koncové body úložiště](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

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


## <a name="custom-cluster-setup"></a>Vlastní nastavení clusteru
Vlastní nastavení clusteru se vytváří v nastavení rychlého vytvoření a přidává tyto možnosti:
- [Balíček podnikového zabezpečení](#enterprise-security-package)
- [Aplikace HDInsight](#install-hdinsight-applications-on-clusters)
- [Velikost clusteru](#configure-cluster-size)
- [Akce skriptů](#advanced-settings-script-actions)
- [Virtuální síť](#advanced-settings-extend-clusters-with-a-virtual-network)
 
## <a name="enterprise-security-package"></a>Balíček podnikového zabezpečení

Pro typy clusterů Hadoop, Spark, HBA, Kafka a interaktivní dotaz můžete povolit **balíček zabezpečení podniku**. Tento balíček nabízí možnost mít bezpečnější instalaci clusteru pomocí Apache Ranger a integraci s Azure Active Directory. Další informace najdete v tématu [Přehled podnikového zabezpečení ve službě Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

![možnosti vytvoření HDInsight výběr balíčku podnikového zabezpečení](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-enterprise-security-package.png)

Další informace o vytvoření clusteru HDInsight připojeného k doméně najdete v tématu [vytvoření prostředí sandboxu HDInsight připojeného k doméně](./domain-joined/apache-domain-joined-configure.md). 

## <a name="install-hdinsight-applications-on-clusters"></a>Instalace aplikací HDInsight v clusterech

Aplikace HDInsight je aplikace, kterou uživatelé mohou nainstalovat na clusteru HDInsight se systémem Linux. Můžete používat aplikace od Microsoftu, třetích stran nebo si sami vyvíjíte. Další informace najdete v tématu [instalace aplikací Apache Hadoop třetích stran v Azure HDInsight](hdinsight-apps-install-applications.md).

Většina aplikací HDInsight je nainstalovaná na prázdném hraničním uzlu.  Prázdný hraniční uzel je virtuální počítač se systémem Linux se stejnými klientskými nástroji, který je nainstalovaný a nakonfigurovaný jako hlavní uzel. Hraniční uzel můžete použít pro přístup ke clusteru, testování klientských aplikací a hostování klientských aplikací. Další informace najdete v tématu [použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="configure-cluster-size"></a>Konfigurace velikosti clusteru

Účtuje se vám využití uzlu, dokud cluster existuje. Fakturace začne při vytvoření clusteru a zastaví se při jeho odstranění. Clustery nemůžou být nepřidělené nebo se zablokují.

### <a name="number-of-nodes-for-each-cluster-type"></a>Počet uzlů pro každý typ clusteru
Každý typ clusteru má svůj vlastní počet uzlů, terminologii pro uzly a výchozí velikost virtuálního počítače. V následující tabulce je počet uzlů pro každý typ uzlu v závorkách.

| type | Uzly | Diagram |
| --- | --- | --- |
| Hadoop |Hlavní uzel (2), pracovní uzel (1 +) |![Uzly clusteru HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Hlavní server (2), server oblasti (1 +), hlavní/ZooKeeper uzel (3) |![Clustery HDInsight HBA uzly clusteru](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus uzel (2), server pro řízení (1 +), ZooKeeper uzel (3) |![Uzly clusteru v HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Hlavní uzel (2), pracovní uzel (1 +), uzel ZooKeeper (3) (volné pro velikost virtuálního počítače a1 ZooKeeper) |![Uzly clusteru HDInsight Spark](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

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
> Omezení velikosti clusteru se liší v rámci předplatných Azure. Pokud chcete tento limit zvýšit, kontaktujte [podporu fakturace Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) .

Když použijete Azure Portal ke konfiguraci clusteru, velikost uzlu je k dispozici v okně **cenové úrovně uzlu** . Na portálu můžete také zobrazit náklady spojené s různými velikostmi uzlů. 

![Velikosti uzlů virtuálních počítačů HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Velikosti virtuálních počítačů 
Při nasazení clusterů vyberte výpočetní prostředky na základě řešení, které plánujete nasadit. Pro clustery HDInsight se používají následující virtuální počítače:
* Virtuální počítače řady a a D1-4: [Velikosti virtuálních počítačů se systémem Linux pro obecné účely](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Virtuální počítač řady D11-14: [Velikosti virtuálních počítačů se systémem Linux optimalizované pro paměť](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Pokud chcete zjistit, jakou hodnotu byste měli použít k určení velikosti virtuálního počítače při vytváření clusteru pomocí různých sad SDK nebo při použití Azure PowerShell, přečtěte si téma [velikosti virtuálních počítačů, které se mají použít pro clustery HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). Z tohoto odkazovaného článku použijte hodnotu ve sloupci **Velikost** v tabulkách.

> [!IMPORTANT]  
> Pokud v clusteru potřebujete víc než 32 pracovních uzlů, musíte vybrat velikost hlavního uzlu s aspoň 8 jádry a 14 GB paměti RAM.

Další informace najdete v tématu [velikosti virtuálních počítačů](../virtual-machines/windows/sizes.md). Informace o cenách různých velikostí najdete v tématu ceny služby [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="advanced-settings-script-actions"></a>Pokročilá nastavení: Akce skriptů

Během vytváření můžete nainstalovat další součásti nebo přizpůsobit konfiguraci clusteru pomocí skriptů. Tyto skripty jsou vyvolány prostřednictvím **akce skriptu**, což je možnost konfigurace, kterou lze použít z Azure Portal, rutin prostředí Windows PowerShell pro HDInsight nebo sady HDInsight .NET SDK. Další informace najdete v tématu [přizpůsobení clusteru HDInsight pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

Některé nativní komponenty Java, jako je Apache Mahout a Cascading, se dají spouštět v clusteru jako soubory v archivu Java (JAR). Tyto soubory JAR je možné distribuovat, aby Azure Storage a odeslaly do clusterů HDInsight pomocí mechanismů pro odesílání úloh Hadoop. Další informace najdete v tématu [odeslání Apache Hadoop úloh programově](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Pokud máte problémy s nasazováním souborů JAR do clusterů HDInsight nebo voláním souborů JAR v clusterech HDInsight, kontaktujte [Podpora Microsoftu](https://azure.microsoft.com/support/options/).
>
> Kaskáding není HDInsight podporován a nemá nárok na podpora Microsoftu. Seznam podporovaných součástí najdete v tématu [co je nového ve verzích clusterů poskytovaných službou HDInsight](hdinsight-component-versioning.md).

V některých případech budete chtít během procesu vytváření nakonfigurovat následující konfigurační soubory:

* clusterIdentity.xml
* Core-site. XML
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred – lokalita
* Oozie-site. XML
* oozie-env.xml
* Storm-site. XML
* tez-site.xml
* webhcat-site.xml
* YARN-site. XML

Další informace najdete v tématu [Přizpůsobení clusterů HDInsight pomocí nástroje Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Pokročilá nastavení: Rozšiřování clusterů pomocí virtuální sítě
Pokud vaše řešení vyžaduje technologie, které jsou rozdělené mezi více typů clusterů HDInsight, může [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network) propojit požadované typy clusterů. Tato konfigurace umožňuje clusterům a veškerý kód, který do nich nasazujete, k přímé komunikaci.

Další informace o používání služby Azure Virtual Network s HDInsight najdete v tématu [plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

Příklad použití dvou typů clusterů v rámci virtuální sítě Azure najdete v tématu [použití Apache Spark strukturovaného streamování s Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Další informace o používání služby HDInsight s virtuální sítí, včetně specifických požadavků na konfiguraci pro virtuální síť, najdete v tématu [plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).


## <a name="next-steps"></a>Další postup

- [Řešení chyb při vytváření clusteru s Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
- [Co jsou HDInsight, Apache Hadoop ekosystém a clustery Hadoop?](hadoop/apache-hadoop-introduction.md)
- [Začínáme používat Apache Hadoop ve službě HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Práce v Apache Hadoop ve službě HDInsight z počítače s Windows](hdinsight-hadoop-windows-tools.md)
