---
title: Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími
description: Nastavte clustery Hadoop, Kafka, Spark, HBA, R Server nebo neplní clustery pro HDInsight z prohlížeče, rozhraní příkazového řádku Azure Classic, Azure PowerShell, REST nebo sady SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 08/06/2020
ms.openlocfilehash: b7757b5204c9b3b32145667367a71a9acc42c230
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89434652"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Naučte se, jak nastavit a nakonfigurovat Apache Hadoop, Apache Spark, Apache Kafka, interaktivní dotazy, Apache HBA, služby ML nebo Apache Storm ve službě HDInsight. Přečtěte si také, jak přizpůsobit clustery a přidat zabezpečení jejich připojením k doméně.

Cluster Hadoop se skládá z několika virtuálních počítačů (uzlů), které se používají pro distribuované zpracování úloh. Azure HDInsight zpracovává podrobnosti o implementaci instalace a konfigurace jednotlivých uzlů, takže stačí zadat obecné informace o konfiguraci.

> [!IMPORTANT]  
> Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. Naučte se, jak [Odstranit cluster.](hdinsight-delete-cluster.md)

Pokud používáte více clusterů společně, budete chtít vytvořit virtuální síť a pokud používáte cluster Spark, budete také chtít použít konektor pro skladiště z podregistru. Další informace najdete v tématu [plánování virtuální sítě pro Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md) a [integrace Apache Spark a Apache Hive pomocí konektoru skladu s podregistru](interactive-query/apache-hive-warehouse-connector.md).

## <a name="cluster-setup-methods"></a>Metody instalace clusteru

V následující tabulce jsou uvedeny různé metody, které můžete použít k nastavení clusteru HDInsight.

| Clustery vytvořené pomocí | Webový prohlížeč | Příkazový řádek | REST API | Sada SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Šablony Azure Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Tento článek vás provede instalací v [Azure Portal](https://portal.azure.com), kde můžete vytvořit cluster HDInsight.

## <a name="basics"></a>Základy

![vlastní rychlé možnosti vytvoření HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Podrobnosti o projektu

[Azure Resource Manager](../azure-resource-manager/management/overview.md) vám pomůže pracovat s prostředky v aplikaci jako se skupinou, která se označuje jako [Skupina prostředků](../azure-resource-manager/management/overview.md#resource-groups)Azure. Všechny prostředky pro aplikaci můžete nasadit, aktualizovat, monitorovat nebo odstranit v rámci jediné koordinované operace.

### <a name="cluster-details"></a>Podrobnosti o clusteru

#### <a name="cluster-name"></a>Název clusteru

Názvy clusterů HDInsight mají následující omezení:

* Povolené znaky: a-z, 0-9, A až Z
* Maximální délka: 59
* Rezervované názvy: aplikace
* Obor názvů clusteru je pro všechny Azure v rámci všech předplatných. Proto musí být název clusteru jedinečný po celém světě.
* Prvních šest znaků musí být jedinečný v rámci virtuální sítě.

#### <a name="region"></a>Oblast

Umístění clusteru nemusíte explicitně určovat: cluster je ve stejném umístění jako výchozí úložiště. Seznam podporovaných oblastí získáte tak, že vyberete rozevírací seznam **oblast** na [ceny služby HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

#### <a name="cluster-type"></a>Typ clusteru

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
| [Bouře](storm/apache-storm-overview.md) |Zpracování událostí v reálném čase |

#### <a name="version"></a>Verze

Vyberte verzi HDInsight pro tento cluster. Další informace najdete v tématu [podporované verze HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Pověření clusteru

Clustery HDInsight umožňují během vytváření clusteru konfigurovat dva uživatelské účty:

* Uživatelské jméno přihlášení clusteru: výchozí uživatelské jméno je *admin*. Používá základní konfiguraci na Azure Portal. Někdy se nazývá "uživatel clusteru" nebo "HTTP uživatel".
* Uživatelské jméno Secure Shell (SSH): slouží k připojení ke clusteru přes SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Uživatelské jméno protokolu HTTP má následující omezení:

* Povolené speciální znaky: `_` a `@`
* Nepovolené znaky: #;. ", \/ : '! *? $ () {} [] <>|&--= +% ~ ^ Space
* Maximální délka: 20

Uživatelské jméno SSH má následující omezení:

* Povolené speciální znaky: `_` a `@`
* Nepovolené znaky: #;. ", \/ : '! *? $ () {} [] <>|&--= +% ~ ^ Space
* Maximální délka: 64
* Rezervované názvy: Hadoop, uživatelé, Oozie, podregistr, mapred, Ambari-QA, Zookeeper, tez, HDFS, Sqoop, příze, hcat, AMS, HBA, test1, Administrator, admin, User, uživatel1, test, uživatel2,, user3, admin1, 1, 123, a, ACTUser, ADM, admin2, ASPNET, Backup, Console, David, Host, Jan, Owner, test2, test3, user4, user5, Spark Support_388945a0

## <a name="storage"></a>Storage

![Nastavení úložiště clusteru: koncové body kompatibilní se HDFS](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

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

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> Povolení přenosu zabezpečeného úložiště po vytvoření clusteru může způsobit chyby v účtu úložiště a nedoporučuje se ho použít. Je lepší vytvořit nový cluster pomocí účtu úložiště s povoleným zabezpečeným přenosem.

> [!Note]  
> Azure HDInsight automaticky nepřenáší, nepřesouvá ani nekopíruje vaše data uložená v Azure Storage z jedné oblasti do druhé.

### <a name="metastore-settings"></a>Nastavení metastore

Můžete vytvořit volitelný podregistr nebo Apache Oozie metaúložiště. Ne všechny typy clusterů ale podporují metaúložiště a služba Azure synapse Analytics není kompatibilní s metaúložiště.

Další informace najdete v tématu [použití externích úložišť metadat ve službě Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Když vytvoříte vlastní metastore, nepoužívejte v názvu databáze pomlčky, spojovníky nebo mezery. To může způsobit selhání procesu vytváření clusteru.

#### <a name="sql-database-for-hive"></a>SQL Database pro podregistr

Pokud chcete uchovat tabulky podregistru po odstranění clusteru HDInsight, použijte vlastní metastore. Pak můžete metastore připojit k jinému clusteru HDInsight.

An HDInsight metastore, která je vytvořená pro jednu verzi clusteru HDInsight, nejde sdílet mezi různými verzemi clusterů HDInsight. Seznam verzí služby HDInsight najdete v tématu [podporované verze služby HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Výchozí metastore poskytuje Azure SQL Database s **limitem DTU úrovně Basic 5 (nelze aktualizovat)**! Vhodné pro účely základního testování. U rozsáhlých nebo produkčních úloh doporučujeme migrovat na externí metastore.

#### <a name="sql-database-for-oozie"></a>SQL Database pro Oozie

Pokud chcete zvýšit výkon při použití Oozie, použijte vlastní metastore. Metastore může také poskytnout přístup k datům úlohy Oozie po odstranění clusteru.

#### <a name="sql-database-for-ambari"></a>SQL Database pro Ambari

Ambari se používá k monitorování clusterů HDInsight, provádění změn konfigurace a ukládání informací o správě clusteru a také k historii úloh. Funkce vlastní Ambari DB umožňuje nasadit nový cluster a nastavit Ambari v externí databázi, kterou spravujete. Další informace najdete v tématu [vlastní AMBARI DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Nemůžete znovu použít vlastní Oozie metastore. Pokud chcete použít vlastní metastore Oozie, musíte při vytváření clusteru HDInsight zadat prázdnou Azure SQL Database.

## <a name="security--networking"></a>Zabezpečení a sítě

![možnosti vytvoření HDInsight výběr balíčku podnikového zabezpečení](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Balíček podnikového zabezpečení

Pro typy clusterů Hadoop, Spark, HBA, Kafka a interaktivní dotaz můžete povolit **balíček zabezpečení podniku**. Tento balíček nabízí možnost mít bezpečnější instalaci clusteru pomocí Apache Ranger a integraci s Azure Active Directory. Další informace najdete v tématu [Přehled podnikového zabezpečení ve službě Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

Balíček Enterprise Security umožňuje integrovat HDInsight se službou Active Directory a Apache Ranger. Pomocí balíčku podnikového zabezpečení lze vytvořit více uživatelů.

Další informace o vytvoření clusteru HDInsight připojeného k doméně najdete v tématu [vytvoření prostředí sandboxu HDInsight připojeného k doméně](./domain-joined/apache-domain-joined-configure.md).

### <a name="tls"></a>TLS

Další informace najdete v tématu [zabezpečení transportní vrstvy](./transport-layer-security.md) .

### <a name="virtual-network"></a>Virtuální síť

Pokud vaše řešení vyžaduje technologie, které jsou rozdělené mezi více typů clusterů HDInsight, může [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network) propojit požadované typy clusterů. Tato konfigurace umožňuje clusterům a veškerý kód, který do nich nasazujete, k přímé komunikaci.

Další informace o používání služby Azure Virtual Network s HDInsight najdete v tématu [plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

Příklad použití dvou typů clusterů v rámci virtuální sítě Azure najdete v tématu [použití Apache Spark strukturovaného streamování s Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Další informace o používání služby HDInsight s virtuální sítí, včetně specifických požadavků na konfiguraci pro virtuální síť, najdete v tématu [plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Nastavení šifrování disku

Další informace najdete v tématu [šifrování klíčového disku spravovaného zákazníkem](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Proxy REST Kafka

Toto nastavení je k dispozici pouze pro typ clusteru Kafka. Další informace najdete v tématu [použití proxy REST](./kafka/rest-proxy.md).

### <a name="identity"></a>Identita

Další informace najdete v tématu [spravované identity ve službě Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Konfigurace a ceny

![HDInsight Volba velikosti uzlu](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

Účtuje se vám využití uzlu, dokud cluster existuje. Fakturace začne při vytvoření clusteru a zastaví se při jeho odstranění. Clustery nemůžou být nepřidělené nebo se zablokují.

### <a name="node-configuration"></a>Konfigurace uzlů

Každý typ clusteru má svůj vlastní počet uzlů, terminologii pro uzly a výchozí velikost virtuálního počítače. V následující tabulce je počet uzlů pro každý typ uzlu v závorkách.

| Typ | Uzly | Diagram |
| --- | --- | --- |
| Hadoop |Hlavní uzel (2), pracovní uzel (1 +) |![Uzly clusteru HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Hlavní server (2), server oblasti (1 +), hlavní/ZooKeeper uzel (3) |![Instalace typu clusteru HDInsight HBA](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Bouře |Nimbus uzel (2), server pro řízení (1 +), ZooKeeper uzel (3) |![Nastavení typu clusteru HDInsight v HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
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

### <a name="virtual-machine-sizes"></a>Velikosti virtuálních počítačů

Při nasazení clusterů vyberte výpočetní prostředky na základě řešení, které plánujete nasadit. Pro clustery HDInsight se používají následující virtuální počítače:

* Virtuální počítače řady a a D1-4: [velikosti virtuálních počítačů se systémem Linux pro obecné účely](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Virtuální počítač řady D11-14: [paměťově optimalizované velikosti virtuálních počítačů Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Pokud chcete zjistit, jakou hodnotu byste měli použít k určení velikosti virtuálního počítače při vytváření clusteru pomocí různých sad SDK nebo při použití Azure PowerShell, přečtěte si téma [velikosti virtuálních počítačů, které se mají použít pro clustery HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). Z tohoto odkazovaného článku použijte hodnotu ve sloupci **Velikost** v tabulkách.

> [!IMPORTANT]  
> Pokud v clusteru potřebujete víc než 32 pracovních uzlů, musíte vybrat velikost hlavního uzlu s aspoň 8 jádry a 14 GB paměti RAM.

Další informace najdete v tématu [velikosti pro virtuální počítače](../virtual-machines/windows/sizes.md). Informace o cenách různých velikostí najdete v tématu ceny služby [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Přidání aplikace

Aplikace HDInsight je aplikace, kterou uživatelé mohou nainstalovat na clusteru HDInsight se systémem Linux. Můžete používat aplikace od Microsoftu, třetích stran nebo si sami vyvíjíte. Další informace najdete v tématu [instalace aplikací Apache Hadoop třetích stran v Azure HDInsight](hdinsight-apps-install-applications.md).

Většina aplikací HDInsight je nainstalovaná na prázdném hraničním uzlu.  Prázdný hraniční uzel je virtuální počítač se systémem Linux se stejnými klientskými nástroji, který je nainstalovaný a nakonfigurovaný jako hlavní uzel. Hraniční uzel můžete použít pro přístup ke clusteru, testování klientských aplikací a hostování klientských aplikací. Další informace najdete v tématu [použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Akce skriptů

Během vytváření můžete nainstalovat další součásti nebo přizpůsobit konfiguraci clusteru pomocí skriptů. Tyto skripty jsou vyvolány prostřednictvím **akce skriptu**, což je možnost konfigurace, kterou lze použít z Azure Portal, rutin prostředí Windows PowerShell pro HDInsight nebo sady HDInsight .NET SDK. Další informace najdete v tématu [přizpůsobení clusteru HDInsight pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

Některé nativní komponenty Java, jako je Apache Mahout a Cascading, se dají spouštět v clusteru jako soubory v archivu Java (JAR). Tyto soubory JAR je možné distribuovat, aby Azure Storage a odeslaly do clusterů HDInsight pomocí mechanismů pro odesílání úloh Hadoop. Další informace najdete v tématu [odeslání Apache Hadoop úloh programově](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Pokud máte problémy s nasazováním souborů JAR do clusterů HDInsight nebo voláním souborů JAR v clusterech HDInsight, kontaktujte [Podpora Microsoftu](https://azure.microsoft.com/support/options/).
>
> Kaskáding není HDInsight podporován a nemá nárok na podpora Microsoftu. Seznam podporovaných součástí najdete v tématu [co je nového ve verzích clusterů poskytovaných službou HDInsight](hdinsight-component-versioning.md).

V některých případech budete chtít během procesu vytváření nakonfigurovat následující konfigurační soubory:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred – lokalita
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Další informace najdete v tématu [Přizpůsobení clusterů HDInsight pomocí nástroje Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Další kroky

* [Řešení chyb při vytváření clusteru s Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Co jsou HDInsight, Apache Hadoop ekosystém a clustery Hadoop?](hadoop/apache-hadoop-introduction.md)
* [Začínáme používat Apache Hadoop ve službě HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Práce v Apache Hadoop ve službě HDInsight z počítače s Windows](hdinsight-hadoop-windows-tools.md)
