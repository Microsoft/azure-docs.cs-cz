---
title: Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími
description: Nastavte clustery Hadoop, Kafka, Spark, HBase, R Server nebo Storm pro HDInsight z prohlížeče, klasického rozhraní PŘÍKAZOVÉHO NASTAVENÍ Azure, Azure PowerShellu, REST nebo Sady SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 246ec08e9b4edb33fa49318b68cc4364534282b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064650"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Naučte se nastavit a nakonfigurovat Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services nebo Apache Storm v HDInsightu. Přečtěte si také, jak přizpůsobit clustery a přidat zabezpečení jejich připojením k doméně.

Cluster Hadoop se skládá z několika virtuálních počítačů (uzlů), které se používají pro distribuované zpracování úloh. Azure HDInsight zpracovává podrobnosti implementace instalace a konfigurace jednotlivých uzlů, takže stačí zadat obecné informace o konfiguraci.

> [!IMPORTANT]  
> Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. Přečtěte si, jak [odstranit cluster.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Metody nastavení clusteru

V následující tabulce jsou uvedeny různé metody, které můžete použít k nastavení clusteru HDInsight.

| Clustery vytvořené pomocí | Webový prohlížeč | Příkazový řádek | REST API | Sada SDK |
| --- |:---:|:---:|:---:|:---:|
| [Portál Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Šablony Azure Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Tento článek vás provede nastavením na [webu Azure Portal](https://portal.azure.com), kde můžete vytvořit cluster HDInsight.

## <a name="basics"></a>Základy

![hdinsight vytvořit možnosti vlastní rychlé](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Podrobnosti o projektu

[Azure Resource Manager](../azure-resource-manager/management/overview.md) vám pomůže pracovat s prostředky ve vaší aplikaci jako skupina, označovaná jako [skupina prostředků](../azure-resource-manager/management/overview.md#resource-groups)Azure . Můžete nasadit, aktualizovat, sledovat nebo odstranit všechny prostředky pro vaši aplikaci v jedné koordinované operace.

### <a name="cluster-details"></a>Podrobnosti o clusteru

#### <a name="cluster-name"></a>Název clusteru

Názvy clusterů HDInsight mají následující omezení:

* Povolené znaky: a-z, 0-9, A-Z
* Maximální délka: 59
* Rezervované názvy: aplikace
* Obor pojmenování clusteru je pro všechny Azure, napříč všemi předplatnými. Název clusteru tedy musí být jedinečný po celém světě.
* Prvních šest znaků musí být jedinečných v rámci virtuální sítě.

#### <a name="region"></a>Region (Oblast)

Není nutné explicitně zadávat umístění clusteru: Cluster je ve stejném umístění jako výchozí úložiště. Seznam podporovaných oblastí zobrazíte v rozevíracím seznamu **Oblast** v [cenách HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

#### <a name="cluster-type"></a>Typ clusteru

Azure HDInsight aktuálně poskytuje následující typy clusterů, každý se sadou součástí poskytovat určité funkce.

> [!IMPORTANT]  
> Clustery HDInsight jsou k dispozici v různých typech, každý pro jednu úlohu nebo technologii. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů, jako je storm a HBase v jednom clusteru. Pokud vaše řešení vyžaduje technologie, které jsou rozloženy mezi více typů clusterů HDInsight, [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network) může připojit požadované typy clusterů.

| Typ clusteru | Funkce |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Dávkový dotaz a analýza uložených dat |
| [HBase](hbase/apache-hbase-overview.md) |Zpracování pro velké množství bezschémat, NoSQL data |
| [Interaktivní dotaz](./interactive-query/apache-interactive-query-get-started.md) |Ukládání do mezipaměti v paměti pro interaktivní a rychlejší dotazy Hive |
| [Kafka](kafka/apache-kafka-introduction.md) | Distribuovaná platforma pro streamování, kterou lze použít k vytváření datových kanálů a aplikací datových proudů datových kanálů a aplikací v reálném čase |
| [Služby ML](r-server/r-server-overview.md) |Různé statistiky velkých objemů dat, prediktivní modelování a možnosti strojového učení |
| [Jiskra](spark/apache-spark-overview.md) |Zpracování v paměti, interaktivní dotazy, zpracování mikrodávkového datového proudu |
| [Bouře](storm/apache-storm-overview.md) |Zpracování událostí v reálném čase |

#### <a name="version"></a>Version

Zvolte verzi HDInsight u tohoto clusteru. Další informace naleznete v [tématu Podporované verze HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Pověření clusteru

Pomocí clusterů HDInsight můžete během vytváření clusteru nakonfigurovat dva uživatelské účty:

* Uživatelské jméno přihlášení k clusteru: Výchozí uživatelské jméno je *admin*. Používá základní konfiguraci na webu Azure Portal. Někdy se nazývá "Uživatel clusteru" nebo "Uživatel HTTP".
* Zabezpečené shellové (SSH) uživatelské jméno: Používá se pro připojení ke clusteru prostřednictvím SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Uživatelské jméno HTTP má následující omezení:

* Povolené speciální `_` znaky: a`@`
* Znaky nejsou povoleny: #;."',\/:'!*?$(){}[]<>|&--=+%~^mezera
* Maximální délka: 20

Uživatelské jméno SSH má následující omezení:

* Povolené speciální`_` znaky: a`@`
* Znaky nejsou povoleny: #;."',\/:'!*?$(){}[]<>|&--=+%~^mezera
* Maximální délka: 64
* Vyhrazená jména: hadoop, uživatelé, oozie, úl, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, příze, hcat, ams, hbase, storm, správce, admin, uživatel, user1, test, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, konzole, David, David, David, host, John, majitel, kořen, server, SQL, podpora, support_388945a0, sys, test2, test3, user4, user5, jiskra

## <a name="storage"></a>Úložiště

![Nastavení úložiště clusteru: koncové body kompatibilní s HDFS](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Přestože místní instalace Hadoopu používá hadoopský distribuovaný systém souborů (HDFS) pro úložiště v clusteru, v cloudu používáte koncové body úložiště připojené ke clusteru. Použití cloudového úložiště znamená, že můžete bezpečně odstranit clustery HDInsight používané pro výpočty při zachování dat.

Clustery HDInsight mohou používat následující možnosti úložiště:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage Pro obecné účely v2
* Azure Storage Pro obecné účely v1
* Objekt blob bloku úložiště Azure **(podporovaný jenom jako sekundární úložiště)**

Další informace o možnostech úložiště s HDInsight najdete [v tématu Porovnání možností úložiště pro použití s clustery Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Použití dalšího účtu úložiště v jiném umístění než cluster HDInsight není podporováno.

Během konfigurace pro výchozí koncový bod úložiště zadáte kontejner objektů blob účtu účtu Azure Storage nebo úložiště datového jezera. Výchozí úložiště obsahuje protokoly aplikací a systému. Volitelně můžete zadat další propojené účty Azure Storage a účty úložiště datových jezer, ke kterým má cluster přístup. Cluster HDInsight a účty závislého úložiště musí být ve stejném umístění Azure.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="metastore-settings"></a>Nastavení metaúložiště

Můžete vytvořit volitelné Hive nebo Apache Oozie metastores. Však ne všechny typy clusterů podporují metastores a Azure SQL Data Warehouse není kompatibilní s metastores.

Další informace najdete [v tématu Použití externích úložišť metadat v Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Při vytváření vlastního metaúložiště nepoužívejte pomlčky, pomlčky nebo mezery v názvu databáze. To může způsobit selhání procesu vytváření clusteru.

#### <a name="sql-database-for-hive"></a>Databáze SQL pro Hive

Pokud chcete zachovat tabulky Hive po odstranění clusteru HDInsight, použijte vlastní metastore. Potom můžete připojit metastore do jiného clusteru HDInsight.

Metastore HDInsight vytvořený pro jednu verzi clusteru HDInsight nelze sdílet v různých verzích clusteru HDInsight. Seznam verzí HDInsight najdete v [tématu Podporované verze HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

#### <a name="sql-database-for-oozie"></a>SQL databáze pro Oozie

Chcete-li zvýšit výkon při použití Oozie, použijte vlastní metastore. Metastore může také poskytnout přístup k datům úlohovzie Oozie po odstranění clusteru.

#### <a name="sql-database-for-ambari"></a>SQL databáze pro Ambari

Ambari se používá ke sledování clusterů HDInsight, provádění změn konfigurace a ukládání informací o správě clusteru a také k historii úloh. Vlastní funkce Ambari DB umožňuje nasadit nový cluster a nastavit Ambari v externí databázi, kterou spravujete. Další informace naleznete [v tématu Custom Ambari DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Nelze znovu použít vlastní Metastore Oozie. Chcete-li použít vlastní metastore Oozie, musíte při vytváření clusteru HDInsight poskytnout prázdnou databázi Azure SQL Database.

## <a name="security--networking"></a>Bezpečnost + vytváření sítí

![hdinsight vytvořit možnosti zvolit balíček podnikového zabezpečení](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Balíček zabezpečení organizace

U typů clusterů Hadoop, Spark, HBase, Kafka a Interactive Query můžete povolit **balíček enterprise security**. Tento balíček obsahuje možnost mít bezpečnější nastavení clusteru pomocí Apache Ranger a integrace s Azure Active Directory. Další informace najdete [v tématu Přehled podnikového zabezpečení v Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

Balíček enterprise zabezpečení umožňuje integrovat HDInsight se službou Active Directory a Apache Ranger. Pomocí balíčku zabezpečení Enterprise lze vytvořit více uživatelů.

Další informace o vytváření clusteru HDInsight přilehlého k doméně najdete v [tématu Vytvoření prostředí izolovaného prostoru HDInsight přilehlém k doméně](./domain-joined/apache-domain-joined-configure.md).

### <a name="tls"></a>TLS

Další informace naleznete v tématu [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security)

### <a name="virtual-network"></a>Virtuální síť

Pokud vaše řešení vyžaduje technologie, které jsou rozloženy mezi více typů clusterů HDInsight, [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network) může připojit požadované typy clusterů. Tato konfigurace umožňuje clustery a jakýkoli kód, který nasadíte do nich, přímo komunikovat mezi sebou.

Další informace o používání virtuální sítě Azure s HDInsight [emituje, že najdete informace o plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

Příklad použití dvou typů clusterů v rámci virtuální sítě Azure najdete v tématu [Použití strukturovaného streamování Apache Spark s Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Další informace o používání HDInsightu s virtuální sítí, včetně konkrétních požadavků na konfiguraci pro virtuální síť, najdete v [tématu Plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Nastavení šifrování disku

Další informace naleznete v [tématu Customer-managed key disk encryption](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Proxy REST Kafka

Toto nastavení je k dispozici pouze pro cluster typu Kafka. Další informace naleznete [v tématu Použití serveru REST](./kafka/rest-proxy.md).

### <a name="identity"></a>Identita

Další informace najdete [v tématu Spravované identity v Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Konfigurace + ceny

![HDInsight zvolit velikost uzlu](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

Je vám účtováno použití uzlu tak dlouho, dokud cluster existuje. Fakturace se spustí při vytvoření clusteru a zastaví se po odstranění clusteru. Clustery nelze oddělit nebo pozdržet.

### <a name="node-configuration"></a>Konfigurace uzlů

Každý typ clusteru má svůj vlastní počet uzlů, terminologii pro uzly a výchozí velikost virtuálního počítače. V následující tabulce je počet uzlů pro každý typ uzlu v závorce.

| Typ | Uzly | Diagram |
| --- | --- | --- |
| Hadoop |Hlavní uzel (2), Uzel pracovního procesu (1+) |![Uzly clusteru HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Hlavní server (2), regionální server (1+), hlavní/zoologický uzel (3) |![Nastavení typu clusteru HDInsight HBase](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Uzel Nimbus (2), nadřízený server (1+), uzel ZooKeeper (3) |![Nastavení typu bouřkového clusteru HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Hlavní uzel (2), Uzel pracovníka (1+), uzel ZooKeeper (3) (zdarma pro velikost virtuálního počítače A1 ZooKeeper) |![Nastavení typu clusteru jiskry HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Další informace naleznete [v tématu Konfigurace výchozího uzlu a velikosti virtuálních počítačů pro clustery](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) v části "Jaké jsou součásti hadoopu a verze v HDInsight?"

Náklady na clustery HDInsight je určena počtem uzlů a velikosti virtuálních počítačů pro uzly.

Různé typy clusterů mají různé typy uzlů, počet uzlů a velikosti uzlů:
* Výchozí typ clusteru Hadoop:
    * Dva *hlavní uzly*  
    * Čtyři *pracovní uzly*
* Výchozí typ bouřkového clusteru:
    * Dva *uzly Nimbus*
    * Tři *uzly ZooKeeper*
    * Čtyři *nadřízené uzly*

Pokud právě zkoušíte HDInsight, doporučujeme použít jeden pracovní uzel. Další informace o cenách HDInsight najdete v [tématu ceny HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Limit velikosti clusteru se liší mezi předplatnými Azure. Chcete-li zvýšit limit, obraťte se na [podporu fakturace Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)

Když ke konfiguraci clusteru použijete portál Azure, velikost uzlu je dostupná na kartě **Konfigurace + ceny.** Na portálu můžete také zobrazit náklady spojené s různými velikostmi uzlů.

### <a name="virtual-machine-sizes"></a>Velikosti virtuálních počítačů

Při nasazování clusterů zvolte výpočetní prostředky na základě řešení, které plánujete nasadit. Následující virtuální virtuální mích se používají pro clustery HDInsight:

* Virtuální počítače řady A a D1-4: Univerzální [velikosti virtuálních počítačů s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Virtuální počítač řady D11-14: [Velikosti virtuálních zařízení Linux optimalizované pro paměť](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Pokud chcete zjistit, jakou hodnotu byste měli použít k určení velikosti virtuálního počítače při vytváření clusteru pomocí různých sad SDK nebo při používání Azure PowerShellu, přečtěte [si informace o velikostech virtuálních počítače, které se mají použít pro clustery HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). Z tohoto propojeného článku použijte hodnotu ve sloupci **Velikost** tabulek.

> [!IMPORTANT]  
> Pokud potřebujete více než 32 pracovních uzlů v clusteru, musíte vybrat velikost hlavního uzlu s alespoň 8 jádry a 14 GB paměti RAM.

Další informace najdete [v tématu Velikosti pro virtuální počítače](../virtual-machines/windows/sizes.md). Informace o cenách různých velikostí naleznete v tématu [HDInsight pricing](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Přidání aplikace

Aplikace HDInsight je aplikace, kterou uživatelé mohou nainstalovat na clusteru HDInsight se systémem Linux. Můžete použít aplikace poskytované společností Microsoft, třetími stranami nebo které vyvíjíte sami. Další informace najdete [v tématu Instalace aplikací Apache Hadoop od jiných výrobců v Azure HDInsight](hdinsight-apps-install-applications.md).

Většina aplikací HDInsight je nainstalována na prázdném hraničním uzlu.  Prázdný hraniční uzel je virtuální počítač Linuxu se stejnými klientskými nástroji nainstalovanými a nakonfigurovanými jako v hlavním uzlu. Hraniční uzel můžete použít pro přístup ke clusteru, testování klientských aplikací a hostování klientských aplikací. Další informace naleznete [v tématu Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Akce skriptu

Můžete nainstalovat další součásti nebo přizpůsobit konfiguraci clusteru pomocí skriptů během vytváření. Tyto skripty jsou vyvolány pomocí **akce skriptu**, což je možnost konfigurace, kterou lze použít z portálu Azure, rutin prostředí HDInsight Windows PowerShell nebo sady HDInsight .NET SDK. Další informace naleznete [v tématu Customize HDInsight cluster using Script Action](hdinsight-hadoop-customize-cluster-linux.md).

Některé nativní java komponenty, jako je Apache Mahout a Cascading, lze spustit v clusteru jako soubory Java Archive (JAR). Tyto soubory JAR lze distribuovat do služby Azure Storage a odeslat do clusterů HDInsight s mechanismy odesílání úloh Hadoop. Další informace naleznete [v tématu Odeslání úloh Apache Hadoop programově](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Pokud máte problémy s nasazením souborů JAR do clusterů HDInsight nebo s voláním souborů JAR v clusterech HDInsight, obraťte se na [podporu společnosti Microsoft](https://azure.microsoft.com/support/options/).
>
> Kaskádování není podporováno službou HDInsight a nemá nárok na podporu společnosti Microsoft. Seznamy podporovaných součástí najdete [v tématu Co je nového ve verzích clusteru poskytovaných službou HDInsight](hdinsight-component-versioning.md).

Někdy chcete během procesu vytváření nakonfigurovat následující konfigurační soubory:

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

Další informace naleznete v [tématu Customize HDInsight clusters using Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Další kroky

* [Poradce při potížích s chybami při vytváření clusteru pomocí Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Co jsou HDInsight, ekosystém Apache Hadoop a hadoopské klastry?](hadoop/apache-hadoop-introduction.md)
* [Začínáme používat Apache Hadoop v HDInsightu](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Práce v Apache Hadoop na HDInsight z počítače s Windows](hdinsight-hadoop-windows-tools.md)
