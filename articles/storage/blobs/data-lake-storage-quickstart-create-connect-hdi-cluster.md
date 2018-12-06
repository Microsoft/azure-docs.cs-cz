---
title: Nastavení clusteru pro Hadoop, Spark, Kafka, HBase nebo R Server – Azure HDInsight
description: Nastavení systému Hadoop, Kafka, Spark, HBase, R Server nebo clustery Storm pro HDInsight z prohlížeče, rozhraní příkazového řádku Azure, Azure PowerShell, REST nebo sady SDK.
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: f1c42a3e091bc4b3804edfe835e521a99fcee880
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975064"
---
# <a name="quickstart-set-up-clusters-in-hdinsight"></a>Rychlý start: Nastavení clusterů v HDInsight

V tomto rychlém startu se dozvíte, jak nastavit a nakonfigurovat clusterů v HDInsight pomocí Hadoop, Spark, Kafka, Interactive Query, HBase, R Server nebo Storm. Také se naučíte přizpůsobení clusterů, připojení k doméně a připojit k účtu úložiště pomocí [ve verzi preview Azure Data Lake Storage Gen2](data-lake-storage-introduction.md) povolena.

Hadoop cluster se skládá z několika virtuálních počítačů (uzlů), které se používají pro distribuované zpracování úloh. Azure HDInsight zpracovává podrobnosti implementace, instalace a konfigurace jednotlivých uzlů, takže budete muset zadat informace o obecné konfiguraci.

> [!IMPORTANT]
>Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. Zjistěte, jak [odstranění clusteru.](../../hdinsight/hdinsight-delete-cluster.md)

Účet úložiště s Data Lake Storage Gen2 funkce se používá jako datové vrstvě v tomto rychlém startu. S jeho hierarchického oboru názvů služby a [Hadoop ovladač](data-lake-storage-abfs-driver.md), Data Lake Storage Gen2 je optimalizovaná pro distribuované zpracování a analýzu. Data uložená v účtu úložiště, který má povolené Data Lake Storage Gen2 přetrvává i po odstranění clusteru služby HDInsight.

## <a name="cluster-setup-methods"></a>Metody instalace clusteru

V následující tabulce jsou uvedeny různé metody, které slouží k nastavení clusteru HDInsight.

| Clustery vytvořené pomocí | Webový prohlížeč | Příkazový řádek | REST API | Sada SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](../../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](../../hdinsight/hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Rozhraní příkazového řádku Azure (verze 1.0)](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](../../hdinsight/hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Šablony Azure Resource Manageru](../../hdinsight/hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Rychlé vytvoření: nastavení základní clusteru

Tento článek vás provede instalací v [webu Azure portal](https://portal.azure.com), kde můžete vytvořit cluster HDInsight pomocí *rychlé vytvoření* nebo *vlastní*.

![hdinsight vytvořit možnosti Vlastní rychlé vytvoření](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-creation-options.png)

Postupujte podle pokynů na obrazovce a proveďte nastavení základního clusteru. Níže jsou uvedené podrobnosti pro:

* [Název skupiny prostředků](#resource-group-name)
* [Typy clusterů a konfigurace](#cluster-types) 
* [Přihlášení ke clusteru a uživatelské jméno SSH](#cluster-login-and-ssh-user-name)
* [Umístění](#location)

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [HDInsight 3.3 vyřazení](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="resource-group-name"></a>Název skupiny prostředků

[Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md) pomáhá při práci s prostředky v aplikaci jako se skupinou, označuje jako skupina prostředků Azure. Můžete nasadit, aktualizovat, monitorovat nebo odstranit všechny prostředky pro vaši aplikaci v rámci jediné koordinované operace.

## <a name="cluster-types"></a> Typy clusterů a konfigurace

Azure HDInsight aktuálně poskytuje následující typy clusteru, každý s sada součástí k poskytování určité funkce.

> [!IMPORTANT]
> Clustery HDInsight jsou k dispozici v různých typů, které pro jedné úlohy nebo technologii. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů, jako je Storm a HBase na jednom clusteru. Pokud vaše řešení nevyžaduje technologie, které jsou rozděleny mezi několika typy clusterů HDInsight, [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network) typy požadovaných clusterových se můžete připojit.

| Typ clusteru | Funkce |
| --- | --- |
| [Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md) |Dávkové dotazy a analýzy uložených dat |
| [HBase](../../hdinsight/hbase/apache-hbase-overview.md) |Zpracování velkých objemů dat bez schématu, NoSQL |
| [Interactive Query](../../hdinsight/interactive-query/apache-interactive-query-get-started.md) |Ukládání v mezipaměti pro interaktivnější a rychlejší dotazy Hive |
| [Kafka](../../hdinsight/kafka/apache-kafka-introduction.md) | Distribuovaná streamovací platforma, která slouží k vytváření aplikací a datových proudů v reálném čase datové kanály |
| [R Server](../../hdinsight/r-server/r-server-overview.md) |Různé statistiky pro velké objemy dat, prediktivní modelování a možnosti strojového učení |
| [Spark](../../hdinsight/spark/apache-spark-overview.md) |Zpracování v paměti, interaktivní dotazy, zpracování datových proudů mikrodávek. |
| [Storm](../../hdinsight/storm/apache-storm-overview.md) |Zpracování událostí v reálném čase |

### <a name="hdinsight-version"></a>Verze HDInsight

Zvolte verzi HDInsight pro tento cluster. Další informace najdete v tématu [podporované HDInsight verze](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

Další informace o vytváření HDInsight připojených k doméně clusteru, naleznete v tématu [prostředí sandboxu připojeného k doméně HDInsight vytvořit](../../hdinsight/domain-joined/apache-domain-joined-configure.md).

## <a name="cluster-login-and-ssh-user-name"></a>Přihlášení ke clusteru a uživatelské jméno SSH

S clustery HDInsight můžete nakonfigurovat dva uživatelské účty při vytváření clusteru:

* Uživatel HTTP: výchozí uživatelské jméno je *správce*. Využívá základní konfigurace na portálu Azure portal. Někdy se označuje jako "Clusteru uživatele."
* Uživatele SSH (Linuxové clustery): umožňuje připojení ke clusteru přes SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="location"></a>Umístění (oblastí) pro clustery a úložiště

Není nutné explicitně; zadejte umístění v clusteru cluster je ve stejném umístění jako výchozí úložiště. Seznam podporovaných oblastí, klikněte na tlačítko **oblasti** rozevíracího seznamu na [ceny HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Koncové body úložiště pro clustery

I když instalace v místním systému Hadoop používá souboru systému HDFS (Hadoop Distributed) pro úložiště na clusteru, v cloudu pomocí koncových bodů úložiště připojené ke clusteru. Clustery HDInsight použít buď [Data Lake Storage Gen2](data-lake-storage-abfs-driver.md) nebo [objekty BLOB ve službě Azure Storage](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). Pomocí Azure Storage nebo Azure Data Lake Storage znamená, že se že můžete bezpečně odstranit clusterů HDInsight, které jsou používány pro výpočty a stále uchovejte vaše data.

> [!WARNING]
> Použití dalšího účtu úložiště do jiného umístění v clusteru HDInsight se nepodporuje.

Během konfigurace zadáte pro výchozí koncový bod úložiště Data Lake Storage. Výchozí úložiště obsahuje aplikaci a systémové protokoly. Volitelně můžete zadat další propojené účty, které mají Data Lake Storage Gen2 povoleno, aby mohli získat přístup ke clusteru. HDInsight cluster a závislé úložiště účty musí být ve stejném umístění Azure.

![Nastavení funkce úložiště: koncové body HDFS kompatibilního úložiště](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-cluster-creation-storage2.png)

> [!IMPORTANT]
> Nezapomeňte **zakázat přístup k Data Lake Store**. Tato nastavení odkazuje na starý *Data Lake Store* funkce a třeba zakázat, aby *Data Lake Storage* funkce správně fungovat.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="optional-metastores"></a>Volitelné metaúložiště

Můžete vytvořit volitelné metaúložiště Hive a Oozie. Ale ne všechny typy clusteru podporují metaúložiště, a Azure SQL Data Warehouse není kompatibilní s metaúložiště.

Další informace najdete v tématu [použití externích úložišť metadat v Azure HDInsight](../../hdinsight/hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]
> Když vytvoříte vlastní úložiště metadat, nepoužívejte pomlčky, pomlčky ani mezery v názvu databáze. To může způsobit selhání procesu vytváření clusteru.

### <a name="use-hiveoozie-metastore"></a>Hive metastore

Pokud chcete zachovat tabulek Hive, po odstranění clusteru služby HDInsight, použijte vlastní úložiště metadat. Pak můžete připojit metastore do jiného clusteru HDInsight.

HDInsight metastore, který je vytvořen pro jednu verzi clusteru HDInsight se nedají sdílet mezi různými verzemi clusteru HDInsight. Seznam verzí HDInsight najdete v tématu [podporované HDInsight verze](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Úložiště metadat Oozie

Pokud chcete zvýšit výkon při použití Oozie, použijte vlastní úložiště metadat. Metastoru také poskytuje přístup k datům úlohy Oozie po odstranění clusteru.

> [!IMPORTANT]
> Vlastní úložiště metadat Oozie nemůže znovu použít. Pokud chcete používat vlastní úložiště metadat Oozie, musíte zadat prázdnou databázi SQL Azure při vytváření clusteru HDInsight.

## <a name="configure-cluster-size"></a>Konfigurovat velikost clusteru

Pro uzel využití se účtují, za předpokladu, cluster existuje. Účtování začne při vytvoření clusteru a skončí jeho odstraněním. Clusterů nejde zrušit přidělení nebo pozastavit.

### <a name="number-of-nodes-for-each-cluster-type"></a>Počet uzlů pro každý typ clusteru

Každý typ clusteru má svůj vlastní počet uzlů, terminologie pro uzly a výchozí velikosti virtuálního počítače. V následující tabulce počet uzlů pro každý typ uzlu je v závorkách.

| Typ | Uzly | Diagram |
| --- | --- | --- |
| Hadoop |Hlavní uzel (2), datový uzel (1 +) |![Uzly clusteru HDInsight Hadoop](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Hlavní server (2), oblast serveru (1 +), uzlu master/ZooKeeper (3) |![Uzly clusteru HDInsight HBase](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Uzel nimbus (2), správce serveru (1 +), uzly ZooKeeper (3) |![Uzly clusteru HDInsight Storm](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-storm-cluster-type-setup.png) |
| Spark |Hlavní uzel (2), pracovní uzel (1 +), uzly ZooKeeper (3) (zdarma pro virtuální počítač A1 ZooKeeper velikost) |![Uzly clusteru HDInsight Spark](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-spark-cluster-type-setup.png) |

Další informace najdete v tématu [výchozí velikosti virtuálního počítače a konfigurace uzlů pro clustery](../../hdinsight/hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) v "Jaké jsou komponenty a verze v HDInsight?"

Náklady na clusterech HDInsight se určuje podle počtu uzlů a velikosti virtuálního počítače pro uzly.

Různých typů clusterů mají různé typy uzlů, počet uzlů a velikosti uzlů:
* Výchozí typ clusteru Hadoop:
    * Dvě *hlavním uzlům*  
    * Čtyři *datové uzly*
* Výchozí typ clusteru Storm:
    * Dvě *uzly Nimbus*
    * Tři *uzly ZooKeeper*
    * Čtyři *dohledové uzly*

Pokud se právě pokoušíte mimo HDInsight, doporučujeme že použít jeden datový uzel. Další informace o cenách služby HDInsight najdete v tématu [ceny HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> Limit velikost clusteru se liší mezi předplatným Azure. Kontakt [podporu fakturace Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) o zvýšení limitu.

Když použijete na webu Azure portal ke konfiguraci clusteru, velikost uzlu je k dispozici prostřednictvím **cenové úrovně uzlů** okno. Na portálu uvidíte také náklady spojené s velikostí jiný uzel.

![Velikosti uzlů HDInsight virtuálního počítače](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Velikosti virtuálních počítačů

Při nasazování clusterů, zvolte výpočetní prostředky, které jsou založené na řešení, které chcete nasadit. Následující virtuální počítače se používají pro clustery HDInsight:

* A a virtuálních počítačů řady D1-4: [velikosti virtuálního počítače s Linuxem General-purpose](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Řady virtuálních počítačů D11-14: [velikostí optimalizovaných pro paměť virtuálního počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

A zjistěte, co hodnotou, kterou jste používali k určení velikosti virtuálního počítače při vytváření clusteru pomocí různých sad SDK nebo při použití prostředí Azure PowerShell, najdete v článku [velikosti virtuálních počítačů pro clustery HDInsight](../../cloud-services/cloud-services-sizes-specs.md#size-tables). V tomto článku propojené použije hodnotu v **velikost** sloupec tabulky.

> [!IMPORTANT]
> Pokud potřebujete více než 32 uzlů pracovního procesu v clusteru, musíte vybrat velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti RAM.

Další informace najdete v tématu [velikosti virtuálních počítačů](../../virtual-machines/windows/sizes.md). Informace o různých velikostí cenách najdete v tématu [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

## <a name="custom-cluster-setup"></a>Nastavení vlastního clusteru

Sestavení vlastního clusteru instalační program na rychlé vytvoření nastavení a přidá následující možnosti:

- [Aplikace HDInsight](#hdinsight-applications)
- [Velikost clusteru](#cluster-size)
- Upřesnit nastavení
  - [Akce skriptů](#customize-clusters-using-script-action)
  - [Virtuální síť](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>Instalace aplikací HDInsight v clusterech

Aplikace HDInsight je aplikace, kterou uživatelé mohou nainstalovat na clusteru HDInsight se systémem Linux. Aplikace může používat, pokud se společností Microsoft, třetími stranami nebo které jste sami vývoj. Další informace najdete v tématu [instalace aplikací Hadoop jiných výrobců v Azure HDInsight](../../hdinsight/hdinsight-apps-install-applications.md).

Většina aplikací HDInsight jsou nainstalované na prázdných hraničních uzlů.  Prázdných hraničních uzlů je virtuální počítač s Linuxem pomocí stejných nástrojů klient nainstalovaný a nakonfigurovaný jako hlavní uzel. Hraniční uzel můžete použít pro přístup ke clusteru, testování vaší klientské aplikace a který je hostitelem klientských aplikací. Další informace najdete v tématu [použití prázdných hraničních uzlů v HDInsight](../../hdinsight/hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>Upřesňující nastavení: akcí skriptů

Můžete nainstalovat další komponenty nebo přizpůsobení konfigurace clusteru pomocí skriptů během vytváření. Tyto skripty jsou volány prostřednictvím **akce skriptu**, což je možnost konfigurace, které můžete používat z webu Azure portal, rutin Windows Powershellu HDInsight nebo sady HDInsight .NET SDK. Další informace najdete v tématu [clusteru HDInsight přizpůsobení pomocí akce skriptu](../../hdinsight/hdinsight-hadoop-customize-cluster-linux.md).

Některé nativní Java komponent, jako jsou Mahout a možností, může běžet v clusteru jako soubory archivu (soubor JAR pro Javu). Tyto soubory JAR můžete distribuovat do služby Azure Storage nebo Azure Data Lake Storage a odešle do clusterů HDInsight s mechanismy odesílání úloh Hadoop. Další informace najdete v tématu [Hadoop odeslání úlohy prostřednictvím kódu programu](../../hdinsight/hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]
> Pokud máte problémy s nasazením soubory JAR do clusterů HDInsight, nebo se obraťte volání soubory JAR na clusterech HDInsight [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> CSS nepodporuje HDInsight a nesplňuje podmínky pro Microsoft Support. Seznam podporovaných součásti, naleznete v tématu [co je nového ve verzích clusterů HDInsight poskytuje](../../hdinsight/hdinsight-component-versioning.md).

V některých případech budete chtít nakonfigurovat následující konfigurační soubory během procesu vytváření:

* clusterIdentity.xml
* Core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie site.xml
* oozie-env.xml
* Storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn site.xml

Další informace najdete v tématu [HDInsight přizpůsobení clusterů pomocí Bootstrap](../../hdinsight/hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Upřesňující nastavení: rozšíření clusterů s virtuální sítí

Pokud vaše řešení nevyžaduje technologie, které jsou rozděleny mezi několika typy clusterů HDInsight, [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network) typy požadovaných clusterových se můžete připojit. Tato konfigurace umožňuje clusterů a jakýkoli kód, který nasazujete na ně přímo komunikovat mezi sebou.

Další informace o používání služby Azure virtual network se službou HDInsight najdete v tématu [rozšířit HDInsight s Azure virtual networks](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

Příklad použití dva typy clusterů ve službě Azure virtual network, naleznete v tématu [použití strukturovaného streamování Sparku se systémem Kafka](../../hdinsight/hdinsight-apache-kafka-spark-structured-streaming.md). Další informace o používání HDInsight s virtuální sítí, včetně požadavků na konkrétní konfiguraci virtuální sítě, naleznete v tématu [možnosti rozšíření HDInsight pomocí Azure Virtual Network](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

## <a name="troubleshoot-access-control-issues"></a>Řešení potíží s řízení přístupu

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](../../hdinsight/hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další postup

- [Ovladač systému souborů Hadoop ABFS pro Azure Data Lake Storage Gen2](data-lake-storage-abfs-driver.md)
- [Kurz: Extrakce, transformace a načítání dat pomocí Apache Hivu ve službě Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
- [Co jsou HDInsight, ekosystém Hadoop a clustery Hadoop?](../../hdinsight/hadoop/apache-hadoop-introduction.md)
- [Začínáme používat Hadoop ve službě HDInsight](../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Práce v Hadoop v HDInsight z Windows PC](../../hdinsight/hdinsight-hadoop-windows-tools.md)
