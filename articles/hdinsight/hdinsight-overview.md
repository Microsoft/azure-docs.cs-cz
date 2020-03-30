---
title: Co je Azure HDInsight
description: Úvod do HDInsightu a zásobníku a komponent technologií Apache Hadoop a Apache Spark, včetně Kafka, Hive, Storm a HBase pro analýzu velkých objemů dat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 31e8506067133304144614ff58974ee21e9680be
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241217"
---
# <a name="what-is-azure-hdinsight"></a>Co je Azure HDInsight?

Azure HDInsight je spravovaná celospektrální analytická služba s otevřeným zdrojovým kódem v cloudu pro podniky. Můžete použít open-source architektury jako Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, Apache Storm, R a další.

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Co je HDInsight a technologie Hadoop?

Azure HDInsight je cloudová distribuce komponent Hadoop. Azure HDInsight umožňuje snadné, rychlé a nákladově efektivní zpracování obrovského množství dat. Můžete využít nejoblíbenější opensourcové architektury, jako jsou Hadoop, Spark, Hive, LLAP, Kafka, Storm, R a další. Tyto architektury podporují širokou škálu scénářů, jako jsou ETL (extrakce, transformace a načítání), datové sklady, strojové učení a IoT.

Komponenty technologie Hadoop dostupné ve službě HDInsight najdete v tématu [Dostupné komponenty a verze ve službě HDInsight](./hdinsight-component-versioning.md). Další informace o platformě Hadoop v prostředí HDInsight najdete v tématu [Funkce Azure pro HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-big-data"></a>Co jsou velké objemy dat?

Velké objemy dat se shromažďují v narůstajícím množství, s vyšší rychlostí a stále větší pestrostí formátů. Může se to týkat historických dat (tj. uložených) nebo dat v reálném čase (tj. streamovaných ze zdroje). Informace o nejběžnějších případech použití velkých objemů dat najdete v části [Scénáře použití služby HDInsight](#scenarios-for-using-hdinsight).

## <a name="why-should-i-use-azure-hdinsight"></a>Proč bych měl používat Azure HDInsight?

Tato část uvádí schopnosti služby Azure HDInsight.

|Schopnost  |Popis  |
|---------|---------|
|Nativní pro cloud     |     Azure HDInsight umožňuje vytvářet optimalizované clustery pro [Hadoop](./hadoop/apache-hadoop-linux-tutorial-get-started.md),  [Spark](./spark/apache-spark-jupyter-spark-sql.md),  [Interaktivní dotazy (LLAP)](./interactive-query/apache-interactive-query-get-started.md),  [Kafka](./kafka/apache-kafka-get-started.md),  [Storm](./storm/apache-storm-tutorial-get-started-linux.md),  [HBase](./hbase/apache-hbase-tutorial-get-started-linux.md) a  [ML Services](./r-server/r-server-overview.md) v Azure. HDInsight poskytuje také komplexní smlouvu SLA pro všechny vaše produkční úlohy.  |
|Škálovatelnost a nízké náklady     | HDInsight umožňuje [škálovat](./hdinsight-administer-use-portal-linux.md#scale-clusters) úlohy nahoru nebo dolů.Můžete snížit náklady [vytvořením clusterů na vyžádání](./hdinsight-hadoop-create-linux-clusters-adf.md) a platit pouze za to, co používáte. Můžete také sestavovat datové kanály pro zprovoznění úloh. Oddělený výpočetní výkon a úložiště poskytují lepší výkon a flexibilitu. |
|Bezpečnost a dodržování předpisů    | HDInsight umožňuje chránit datové prostředky vašeho podniku pomocí služby [Azure Virtual Network](./hdinsight-plan-virtual-network-deployment.md), [šifrování](./hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) a integrace se službou [Azure Active Directory](./domain-joined/hdinsight-security-overview.md). HDInsight také splňuje nejoblíbenější oborové a vládní [standardy dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud).        |
|Monitorování    | Azure HDInsight se integruje s [protokoly Azure Monitor,](./hdinsight-hadoop-oms-log-analytics-tutorial.md) aby poskytoval jediné rozhraní, pomocí kterého můžete monitorovat všechny vaše clustery.        |
|Globální dostupnost | HDInsight je k dispozici ve více [oblastech](https://azure.microsoft.com/regions/services/) než jakákoli jiná nabídka analýzy velkých objemů dat. Služba Azure HDInsight je dostupná také pro Azure Government, Čínu a Německo a umožňuje tak splnit požadavky vašeho podniku v klíčových suverénních oblastech. |  
|Produktivita     |  Azure HDInsight umožňuje používat bohaté nástroje zvyšující produktivitu pro Hadoop a Spark s oblíbeným vývojovým prostředím. Mezi tato vývojová prostředí patří [Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md), [VSCode](./hdinsight-for-vscode.md), [Eclipse](./spark/apache-spark-eclipse-tool-plugin.md) a [IntelliJ](./spark/apache-spark-intellij-tool-plugin.md) pro zajištění podpory jazyků Scala, Python, R, Java a .NET. Odborníci přes data můžou také spolupracovat s využitím oblíbených poznámkových bloků, jako jsou [Jupyter](./spark/apache-spark-jupyter-notebook-kernels.md) a [Zeppelin](./spark/apache-spark-zeppelin-notebook.md).    |
|Rozšiřitelnost     |  Clustery HDInsight můžete rozšířit instalací komponent (Hue, Presto atd.) pomocí [akcí skriptů](./hdinsight-hadoop-customize-cluster-linux.md), [přidáním hraničních uzlů](./hdinsight-apps-use-edge-node.md) nebo [integrací s dalšími aplikacemi certifikovanými pro práci s velkými objemy dat](./hdinsight-apps-install-applications.md). HDInsight umožňuje bezproblémovou integraci s nejoblíbenějšími řešeními pro velké objemy dat prostřednictvím nasazení [jedním kliknutím](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Scénáře použití služby HDInsight

Azure HDInsight je možné použít v nejrůznějších scénářích velkých objemů dat. Může se jednat o historická data (data, která jsou už shromážděná a uložená) nebo o data v reálném čase (data proudící přímo ze zdroje). Scénáře zpracování těchto dat můžeme shrnout do následujících kategorií:

### <a name="batch-processing-etl"></a>Dávkové zpracování (ETL)

Extrakce, transformace a načítání (ETL) je proces, při kterém se nestrukturovaná nebo strukturovaná data extrahují z heterogenních zdrojů dat. Potom se transformují do strukturovaného formátu a načítají do úložiště dat. Transformovaná data je možné použít pro datové vědy nebo datové sklady.

### <a name="data-warehousing"></a>Datové sklady

Pomocí služby HDInsight můžete provádět interaktivní dotazy v petabajtovém měřítku nad strukturovanými i nestrukturovanými daty v jakémkoli měřítku. Můžete také sestavovat modely, které je propojí s nástroji BI. Další informace najdete v [tomto příběhu zákazníka](https://customers.microsoft.com/story/milliman).

![Architektura HDInsight: Datové sklady](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png "Architektura datových skladů HDInsight")

### <a name="internet-of-things-iot"></a>Internet věcí (IoT)

HdInsight můžete použít ke zpracování streamovaných dat, která jsou přijata v reálném čase z různých druhů zařízení. Pokud chcete získat další informace, [přečtěte si tento blogový příspěvek z Azure, který oznamuje verzi Public Preview pro Apache Kafka v HDInsightu se Spravovanými disky Azure](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![Architektura HDInsight: Internet věcí](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-iot.png "Architektura HDInsight IoT")

### <a name="data-science"></a>Datové vědy

Pomocí služby HDInsight můžete sestavovat aplikace, které z dat extrahují důležité informace. Navíc můžete pomocí služby Azure Machine Learning předvídat budoucí trendy pro vaši firmu. Další informace najdete v [tomto příběhu zákazníka](https://customers.microsoft.com/story/pros).

![Architektura HDInsight: Datové vědy](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-science.png "Architektura datových věd HDInsight")

### <a name="hybrid"></a>Hybridní

Pomocí služby HDInsight můžete rozšířit svou stávající místní infrastrukturu velkých dat do Azure a využívat možnosti pokročilých analýz v cloudu.

![Architektura HDInsight: Hybridní](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png "Hybridní architektura HDInsight")

## <a name="cluster-types-in-hdinsight"></a>Typy clusterů ve službě HDInsight

HDInsight zahrnuje specifické typy clusterů a možnosti přizpůsobení clusterů, jako je například možnost přidávání komponent, nástrojů a jazyků. HDInsight nabízí následující typy clusteru:

|Typ clusteru | Popis |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|Rámec, který používá HDFS, YARN správu prostředků a jednoduchý MapReduce programovací model pro paralelní zpracování a analýzu dávkových dat.|
|[Apache Spark](./spark/apache-spark-overview.md)|Rozhraní s otevřeným zdrojovým kódem pro paralelní zpracování, které podporuje zpracování v paměti pro zvýšení výkonu aplikací pro analýzu velkých objemů dat. Podívejte [se, co je Apache Spark v HDInsight?](./spark/apache-spark-overview.md).|
|[Apache HBase](./hbase/apache-hbase-overview.md)|Databáze NoSQL postavená na Hadoopu, která poskytuje náhodný přístup a silnou konzistenci pro velké množství nestrukturovaných a částečně strukturovaných dat -- potenciálně miliardy řádků krát miliony sloupců. Přečtěte si téma [Co je HBase v HDInsight?](./hbase/apache-hbase-overview.md)|
|[Služby ML](./r-server/r-server-overview.md)|Server pro hostování a správu paralelních distribuovaných procesů R. Poskytuje datovým vědcům, statistikům a programátorům v R přístup ke škálovatelným, distribuovaným analytickým metodám služby HDInsight na vyžádání. Viz [Přehled ML Services ve službě HDInsight](./r-server/r-server-overview.md).|
|[Apache Storm](./storm/apache-storm-overview.md)|Distribuovaný výpočetní systém v reálném čase pro rychlé zpracování velkých datových toků. Storm je poskytován jako spravovaný cluster v prostředí HDInsight. Viz [Analýza dat snímačů v reálném čase pomocí nástrojů Storm a Hadoop](./storm/apache-storm-overview.md).|
|[Interaktivní dotaz Apache](./interactive-query/apache-interactive-query-get-started.md)|Ukládání do mezipaměti v paměti pro interaktivní a rychlejší dotazy Hive. Viz [Použití Interactive Query ve službě HDInsight](./interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|Open source platforma, která se používá pro vytváření datových kanálů datových proudů a aplikací. Kafka také poskytuje funkce propojující fronty zpráv, pomocí kterých můžete publikovat datové streamy a přihlašovat se k jejich odběru. Viz [Úvod k Apache Kafka ve službě HDInsight](./kafka/apache-kafka-introduction.md).|

## <a name="open-source-components-in-hdinsight"></a>Opensourcové komponenty ve službě HDInsight

Azure HDInsight umožňuje vytvářet clustery s open source architektury, jako je Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase a R. Tyto clustery jsou ve výchozím nastavení dodávány s dalšími open-source komponentami, které jsou součástí clusteru, jako jsou [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](https://avro.apache.org/docs/current/spec.html), [Apache Hive](https://hive.apache.org), [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Apache Mahout](https://mahout.apache.org/), [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Phoenix](https://phoenix.apache.org/), [Apache Pig](https://pig.apache.org/), [Apache Sqoop](https://sqoop.apache.org/), [Apache Tez](https://tez.apache.org/), [Apache Oozie](https://oozie.apache.org/), [Apache ZooKeeper](https://zookeeper.apache.org/).  

## <a name="programming-languages-in-hdinsight"></a>Programovací jazyky v prostředí HDInsight

Clustery HDInsight, včetně clusterů Spark, HBase, Kafka, Hadoop a dalších, podporují celou řadu programovacích jazyků. Některé z nich ale nejsou ve výchozím nastavení nainstalované. Pro knihovny, moduly nebo balíčky, které nejsou nainstalovány ve výchozím nastavení, [použijte akci skriptu k instalaci komponenty](./hdinsight-hadoop-script-actions-linux.md).

|Programovací jazyk  |Informace  |
|---------|---------|
|Výchozí podpora programovacích jazyků     | Ve výchozím nastavení podporují clustery prostředí HDInsight tyto jazyky:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Přejít</li></ul>  |
|Jazyky Java virtual machine (JVM)     | V prostředí Java Virtual Machine (JVM) je možné spouštět celou řadu jiných jazyků, než je Java. Pokud ale některé z těchto jazyků spustíte, může být v clusteru potřeba nainstalovat další součásti. Clustery prostředí HDInsight podporují následující jazyky založené na JVM: <ul><li>Clojure</li><li>Jython (Python pro jazyk Java)</li><li>Scala</li></ul>     |
|Jazyky pro Hadoop     | Clustery HDInsight podporují následující jazyky, které jsou určené konkrétně pro technologii Hadoop: <ul><li>Pig Latin pro úlohy Pig</li><li>HiveQL pro úlohy Hive a SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>Vývojářské nástroje pro HDInsight

Vývojářské nástroje pro HDInsight, včetně nástrojů IntelliJ, Eclipse, Visual Studio Code a Visual Studio, můžete díky bezproblémové integraci s Azure použít k vytváření a odesílání úloh a dotazů na data HDInsight.

* [Sada nástrojů Azure pro IntelliJ](./spark/apache-spark-intellij-tool-plugin.md)

* [Sada nástrojů Azure pro eclipse](./spark/apache-spark-eclipse-tool-plugin.md)

* [Azure HDInsight Tools pro VS Code](./hdinsight-for-vscode.md)

* [Nástroje Azure Data Lake pro Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="business-intelligence-on-hdinsight"></a>Business intelligence ve službě HDInsight

Známé nástroje business intelligence (BI) načítají, analyzují a vykazují data integrovaná v prostředí HDInsight buď pomocí doplňku Power Query, nebo ovladače Microsoft Hive ODBC Driver:

* [Apache Spark BI s využitím nástrojů pro vizualizaci dat ve službě Azure HDInsight](./spark/apache-spark-use-bi-tools.md)

* [Vizualizace dat Apache Hive pomocí Microsoft Power BI v Azure HDInsightu](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [Vizualizace dat Interactive Query Hive pomocí Power BI ve službě Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Připojení Excelu k Apache Hadoop pomocí Power Query](./hadoop/apache-hadoop-connect-excel-power-query.md) (vyžaduje Windows)

* [Připojení Excelu k Apache Hadoop s ovladačem Microsoft Hive ODBC (vyžaduje](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) Windows)

## <a name="next-steps"></a>Další kroky

* [Vytvoření clusteru Apache Hadoop v HDInsightu](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
