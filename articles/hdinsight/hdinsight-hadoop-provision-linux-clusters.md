---
title: Instalace clusteru pro Hadoop, Spark, Kafka, HBase nebo R Server - Azure HDInsight | Microsoft Docs
description: Nastavte Hadoop, Kafka, Spark, HBase, R Server nebo clustery Storm pro HDInsight z prohlížeče, rozhraní příkazového řádku Azure, Azure PowerShell, REST nebo sady SDK.
keywords: nastavení clusteru hadoop, kafka clusteru instalační program, nastavení clusteru spark, co je cluster v hadoop
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 23a01938-3fe5-4e2e-8e8b-3368e1bbe2ca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: jgao
ms.openlocfilehash: 54c88b25911ddfa4ed9ecf6ea4b28278c2d9aac2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-clusters-in-hdinsight-with-hadoop-spark-kafka-and-more"></a>Nastavit clusterů v HDInsight Hadoop, Spark, Kafka a dalšími

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Zjistěte, jak nastavit a konfigurovat clusterů v HDInsight Hadoop, Spark, Kafka, interaktivní dotazu, HBase, R Server nebo Storm. Také zjistěte, jak přizpůsobit clustery a přidat zabezpečení tak, že je připojení k doméně.

Hadoop cluster se skládá z několika virtuálních počítačů (uzlů), které se používají pro distribuované zpracování úlohy. Azure HDInsight zpracovává podrobnosti implementace instalace a konfigurace jednotlivých uzlů, takže budete muset zadat informace o obecné konfiguraci. 

> [!IMPORTANT]
>Jakmile clusteru je vytvořen a zastaví, když odstranění clusteru, začne běžet fakturace clusteru HDInsight. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. Zjistěte, jak [odstranit cluster.](hdinsight-delete-cluster.md)
>

## <a name="cluster-setup-methods"></a>Metody instalace clusteru
Následující tabulka uvádí různé metody, které můžete použít k nastavení clusteru HDInsight.

| Clustery jsou vytvořené pomocí | Webový prohlížeč | Příkazový řádek | REST API | Sada SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Rozhraní příkazového řádku Azure (ver 1.0)](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Šablony Azure Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Rychlé vytvoření: nastavení základní clusteru
Tento článek vás provede procesem instalace v [portál Azure](https://portal.azure.com), kde můžete vytvořit cluster HDInsight pomocí *rychle vytvořit* nebo *vlastní*. 

![hdinsight vytvořit možnosti vlastní rychle vytvořit](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-options.png)

Postupujte podle pokynů na obrazovce proveďte nastavení základního clusteru. Podrobnosti jsou uvedené pro:

* [Název skupiny prostředků](#resource-group-name)
* [Typy clusterů a konfigurace](#cluster-types) 
* [Přihlášení clusteru a uživatelské jméno SSH](#cluster-login-and-ssh-username)
* [Umístění](#location)

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [HDInsight 3.3 vyřazení](hdinsight-component-versioning.md#hdinsight-windows-retirement).
>

## <a name="resource-group-name"></a>Název skupiny prostředků 

[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pomáhá při práci s prostředky v aplikaci jako se skupinou, označuje jako skupinu prostředků Azure. Můžete nasadit, aktualizovat, sledovat nebo odstranit všechny prostředky pro aplikaci v rámci jediné koordinované operace.

## <a name="cluster-types"></a> Typy clusterů a konfigurace
Azure HDInsight aktuálně poskytuje následující typy clusteru, každý s sada součástí k poskytování určité funkce.

> [!IMPORTANT]
> Clustery HDInsight jsou k dispozici v různých typů, které pro jedna úloha nebo technologii. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů, jako je například Storm a HBase v jednom clusteru. Pokud vaše řešení vyžaduje technologie, které jsou rozloženy více typy clusterů HDInsight [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network) můžete připojit typy požadované clusteru. 
>
>

| Typ clusteru | Funkce |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batch dotazu a analýzy uložených dat |
| [HBase](hbase/apache-hbase-overview.md) |Zpracování velkých objemů dat schemaless, NoSQL |
| [Interaktivní dotazu](./interactive-query/apache-interactive-query-get-started.md) |Ukládání do mezipaměti v paměti pro interaktivní a rychlejší dotazů Hive |
| [Kafka](kafka/apache-kafka-introduction.md) | Distribuované streamování platformu, která můžete použít k sestavení v reálném čase kanálů streamování dat a aplikací |
| [R Server](r-server/r-server-overview.md) |Různé statistiky velkých objemů dat, prediktivního modelování a strojového učení možnosti |
| [Spark](spark/apache-spark-overview.md) |Zpracování v paměti, interaktivních dotazů datového proudu micro dávkové zpracování |
| [Storm](storm/apache-storm-overview.md) |Zpracování událostí v reálném čase |


### <a name="hdinsight-version"></a>HDInsight verze
Zvolte verzi HDInsight pro tento cluster. Další informace najdete v tématu [podporované HDInsight verze](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="enterprise-security-package"></a>Balíček zabezpečení Enterprise

Pro typy clusteru Hadoop, Spark a interaktivní dotazu, můžete povolit **balíček zabezpečení Enterprise**. Tento balíček poskytuje možnost bezpečnější nastavení clusteru pomocí Apache škálu a integrace s Azure Active Directory. Další informace najdete v tématu [Enterprise balíček zabezpečení v Azure HDInsight](./domain-joined/apache-domain-joined-introduction.md).

![hdinsight vytvořit možnosti vybrat balíček zabezpečení enterprise](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-enterprise-security-package.png)

Další informace o vytváření HDInsight připojený k doméně clusteru najdete v tématu [vytvořit připojený k doméně prostředí HDInsight izolovaného prostoru](./domain-joined/apache-domain-joined-configure.md).


## <a name="cluster-login-and-ssh-user-name"></a>Přihlášení a uživatelské jméno SSH clusteru
S clustery HDInsight můžete nakonfigurovat dva účty uživatele při vytváření clusteru:

* Uživatel HTTP: výchozí uživatelské jméno je *správce*. Používá základní konfiguraci na portálu Azure. Někdy se označuje jako "Clusteru uživatele."
* Uživatel SSH (Linux clustery): umožňuje připojení ke clusteru prostřednictvím SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Balíček zabezpečení Enterprise umožňuje integraci se službou Active Directory a Apache škálu HDInsight. Několik uživatelů lze vytvořit pomocí balíčku zabezpečení rozlehlé sítě.

## <a name="location"></a>Umístění (oblastí) a úložiště

Nemusíte explicitně zadejte umístění v clusteru: clusteru je ve stejném umístění jako výchozí úložiště. Seznam podporovaných oblastí, klikněte na tlačítko **oblast** rozevíracího seznamu na [HDInsight ceny](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Koncové body úložiště pro clustery

I když místní instalace systému Hadoop používá Hadoop Distributed File System (HDFS) pro úložiště na clusteru, v cloudu použijete koncové body úložiště připojené ke clusteru. Clustery HDInsight použijte buď [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) nebo [objektů BLOB v Azure Storage](hdinsight-hadoop-use-blob-storage.md). Pomocí Azure Storage nebo Data Lake Store znamená, že můžete bezpečně odstranit clusterů HDInsight, které jsou používány pro výpočty a zároveň zachovat data. 

> [!WARNING]
> Pomocí účtu další úložiště v jiném umístění z clusteru HDInsight se nepodporuje.

Během konfigurace zadáte pro koncový bod úložiště výchozí kontejner objektů blob z účtu Azure Storage nebo Data Lake Store. Výchozí úložiště obsahující aplikační a systémové protokoly. Volitelně můžete zadat další propojené účty Azure Storage a účty Data Lake Store, kteří mohou přistupovat ke clusteru. HDInsight cluster a účty závislého úložiště musí být ve stejné oblasti Azure.

![Nastavení úložiště clusteru: koncové body HDFS kompatibilní úložiště](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


### <a name="optional-metastores"></a>Volitelné metaúložiště
Můžete vytvořit volitelné metaúložiště Hive nebo Oozie. Ale ne všechny typy clusteru podporují metaúložiště a Azure SQL Data Warehouse není kompatibilní s metaúložiště. 

Další informace najdete v tématu [používal externí metadata úložiště v Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]
> Když vytvoříte vlastní metaúložiště, nepoužívejte pomlčky, pomlčky ani mezery v názvu databáze. To může způsobit selhání procesu vytváření clusteru.

### <a name="use-hiveoozie-metastore"></a>Metaúložiště Hive

Pokud chcete zachovat vaše tabulek Hive po odstranění clusteru služby HDInsight, použijte vlastní metaúložiště. Pak můžete připojit metaúložiště do jiného clusteru HDInsight.

Metaúložiště HDInsight, který se vytvoří pro jednu verzi clusteru HDInsight se nedají sdílet mezi různými verzemi clusteru HDInsight. Seznam verzí HDInsight, naleznete v části [podporované HDInsight verze](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Metaúložiště Oozie

Chcete-li zvýšit výkon při použití Oozie, použijte vlastní metaúložiště. Metaúložiště můžete zadat taky přístup k datům úlohy Oozie po odstranění clusteru. 

> [!IMPORTANT]
> Vlastní metaúložiště Oozie nelze znovu použít. Pokud chcete používat vlastní metaúložiště Oozie, je nutné zadat prázdnou databázi SQL Azure při vytváření clusteru HDInsight.

## <a name="configure-cluster-size"></a>Nakonfigurujte velikost clusteru

Fakturuje se využívání uzlu, dokud clusteru existuje. Pokud cluster je vytvořen a zastaví, když odstranění clusteru, začne běžet fakturace. Clustery nelze zrušte přidělené nebo blokovat.


### <a name="number-of-nodes-for-each-cluster-type"></a>Počet uzlů pro každý typ clusteru
Každý typ clusteru má svou vlastní počet uzlů, terminologie pro uzly a výchozí velikost virtuálního počítače. V následující tabulce je počet uzlů pro každý typ uzlu v závorkách.

| Typ | Uzly | Diagram |
| --- | --- | --- |
| Hadoop |Hlavní uzel (2), datový uzel (1 +) |![Uzly clusteru HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Hlavního serveru (2), server oblast (1 +), hlavní/ZooKeeper uzlu (3) |![Uzly clusteru HDInsight HBase](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Uzel nimbus (2), nadřízeného serveru (1 +), ZooKeeper uzlu (3) |![Uzly clusteru HDInsight Storm](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Hlavní uzel (2), pracovního uzlu (1 +), ZooKeeper uzlu (3) (zdarma pro virtuální počítač A1 ZooKeeper velikost) |![Uzly clusteru HDInsight Spark](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Další informace najdete v tématu [výchozí velikosti virtuálního počítače a konfigurace uzlu pro clustery](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) v "Jaké jsou komponent systému Hadoop a verzí v prostředí HDInsight?"

Náklady na clustery HDInsight je určen podle počtu uzlů a velikosti virtuálních počítačů pro uzly. 

Různými typy clusteru mají různé typy uzlů, počtu uzlů a velikosti uzlu:
* Výchozí typ clusteru Hadoop: 
    * Dva *hlavní uzly*  
    * Čtyři *datové uzly*
* Výchozí typ clusteru Storm: 
    * Dva *uzly Nimbus*
    * Tři *uzly ZooKeeper*
    * Čtyři *nadřízeného uzly* 

Pokud jsou právě vyzkoušení HDInsight, doporučujeme, že abyste použili jeden datový uzel. Další informace o cenách prostředí HDInsight najdete v tématu [HDInsight ceny](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> Limit velikosti clusteru se liší podle předplatných Azure. Obraťte se na [Azure podporu fakturace](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) o zvýšení limitu.
>

Při použití portálu Azure ke konfiguraci clusteru, velikost uzlu je k dispozici prostřednictvím **cenové úrovně uzlu** okno. Na portálu uvidíte také náklady na jiný uzel velikosti. 

![Virtuální počítač HDInsight velikostí uzlu](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Velikosti virtuálních počítačů 
Při nasazení clusterů, zvolte výpočetní prostředky v závislosti na řešení, které chcete nasadit. Následující virtuální počítače se používají pro clustery služby HDInsight:
* A a virtuální počítače řady D1 – 4: [velikosti virtuálního počítače s Linuxem General-purpose](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11-14 řady virtuálních počítačů: [velikosti paměťově optimalizované virtuálních počítačů Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Chcete zjistit, co hodnotou, kterou jste měli použít k zadání velikost virtuálního počítače při vytváření clusteru pomocí různé sady SDK nebo při použití Azure PowerShell, najdete v části [velikosti virtuálních počítačů, které chcete použít pro clustery HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). Z této propojené článku, použijte hodnotu v **velikost** sloupec tabulky.

> [!IMPORTANT]
> Pokud potřebujete více než 32 uzlů pracovního procesu v clusteru, je třeba vybrat velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti RAM.
>
>

Další informace najdete v tématu [velikosti virtuálních počítačů](../virtual-machines/windows/sizes.md). Informace o cenách různých velikostí najdete v tématu [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="custom-cluster-setup"></a>Nastavení vlastní clusteru
Instalace sestavení vlastní clusteru na rychlého vytvoření nastavení a přidá následující možnosti:
- [Aplikace HDInsight](#hdinsight-applications)
- [Velikost clusteru](#cluster-size)
- Upřesnit nastavení
  - [Akce skriptu](#customize-clusters-using-script-action)
  - [Virtuální síť](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>Instalace aplikací HDInsight v clusterech

Aplikace HDInsight je aplikace, kterou uživatelé mohou nainstalovat na clusteru HDInsight se systémem Linux. Aplikace může používat, pokud se společností Microsoft, třetí strany nebo které vyvíjíte, sami. Další informace najdete v tématu [instalovat aplikace jiných výrobců Hadoop v Azure HDInsight](hdinsight-apps-install-applications.md).

Většina aplikací HDInsight jsou nainstalované na prázdný hraniční uzel.  Prázdný hraniční uzel je virtuální počítač s Linuxem pomocí stejných nástrojů klient nainstalován a nakonfigurován jako hlavního uzlu. Hraničního uzlu můžete použít pro přístup ke clusteru, testování vaší klientské aplikace a hostování vaší klientské aplikace. Další informace najdete v tématu [použít prázdný edge uzly v HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>Upřesňující nastavení: skript akce

Můžete nainstalovat další součásti nebo si přizpůsobit pomocí skriptů během vytváření konfigurace clusteru. Tyto skripty jsou volány prostřednictvím **akce skriptu**, což je možnost konfigurace, které je možné z portálu Azure, rutiny prostředí Windows PowerShell HDInsight nebo sady SDK rozhraní .NET HDInsight. Další informace najdete v tématu [clusteru HDInsight přizpůsobit pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

Některé nativní součásti Java, jako je Mahout a s možností, můžete spustit v clusteru jako soubory archivu Java (JAR). Tyto soubory JAR může distribuovat do služby Azure Storage a odeslána ke clusterům HDInsight s mechanismy odesílání úloh Hadoop. Další informace najdete v tématu [Hadoop odeslání úlohy prostřednictvím kódu programu](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]
> Pokud máte problémy s nasazením souborů JAR ke clusterům HDInsight, nebo volání souborů JAR na clusterech HDInsight, obraťte se na [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> Kaskádových není podporována v prostředí HDInsight a nejsou vhodné pro systém Microsoft Support. Seznam podporovaných součásti, najdete v části [co je nového ve verzích clusterů poskytovaných v HDInsight](hdinsight-component-versioning.md).
>
>

V některých případech je vhodné nakonfigurovat následující konfigurační soubory během procesu vytváření:

* clusterIdentity.xml
* Core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* Storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Další informace najdete v tématu [clusterů HDInsight přizpůsobit pomocí Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Upřesňující nastavení: rozšířit clustery s virtuální sítí
Pokud vaše řešení vyžaduje technologie, které jsou rozloženy více typy clusterů HDInsight [virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network) můžete připojit typy požadované clusteru. Tato konfigurace umožňuje clustery a žádný kód, který nasazujete, a to k přímé komunikaci mezi sebou.

Další informace o používání virtuální sítě Azure s HDInsight naleznete v tématu [rozšíření prostředí HDInsight pomocí virtuální sítě Azure](hdinsight-extend-hadoop-virtual-network.md).

Příklad použití dva typy clusteru v rámci virtuální sítě Azure, naleznete v části [použití Spark strukturovaných streamování s Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Další informace o používání HDInsight s virtuální sítí, včetně určité požadavky na konfiguraci pro virtuální síť, v tématu [HDInsight rozšířit možnosti pomocí Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

## <a name="troubleshoot-access-control-issues"></a>Odstraňování potíží s přístupem ovládací prvek

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další postup

- [Co jsou HDInsight, ekosystému Hadoop a clusterů systému Hadoop?](hadoop/apache-hadoop-introduction.md)
- [Začínáme používat Hadoop ve službě HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Práce v Hadoop v HDInsight ze systému Windows PC](hdinsight-hadoop-windows-tools.md)
