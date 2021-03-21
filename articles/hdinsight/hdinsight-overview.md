---
title: Co je Azure HDInsight
description: Úvod do HDInsight a Apache Hadoop a Apache Spark technologický zásobník a komponenty, včetně Kafka, podregistru, bohatého a HBA pro analýzu velkých objemů dat.
ms.service: hdinsight
ms.topic: overview
ms.custom: contperf-fy21q1
ms.date: 08/21/2020
ms.openlocfilehash: d1c32bf749850ac40e23c1a9cb9c5cd7755d45c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939440"
---
# <a name="what-is-azure-hdinsight"></a>Co je Azure HDInsight?

Azure HDInsight je spravovaná, plně spektrum Open Source služby pro analýzu v cloudu pro podniky. Můžete použít Open Source architektury, jako jsou Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, Apache Storm, R a další.

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Co je HDInsight a technologie Hadoop?

Azure HDInsight je cloudová distribuce komponent systému Hadoop. Azure HDInsight umožňuje snadné, rychlé a nákladově efektivní zpracování obrovského množství dat. Můžete využít nejoblíbenější opensourcové architektury, jako jsou Hadoop, Spark, Hive, LLAP, Kafka, Storm, R a další. Tyto architektury podporují širokou škálu scénářů, jako jsou ETL (extrakce, transformace a načítání), datové sklady, strojové učení a IoT.

Komponenty technologie Hadoop dostupné ve službě HDInsight najdete v tématu [Dostupné komponenty a verze ve službě HDInsight](./hdinsight-component-versioning.md). Další informace o platformě Hadoop v prostředí HDInsight najdete v tématu [Funkce Azure pro HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-big-data"></a>Co jsou velké objemy dat?

Velké objemy dat se shromažďují v narůstajícím množství, s vyšší rychlostí a stále větší pestrostí formátů. Může jít o historické (tzn. uložené) objemy dat nebo o objemy dat v reálném čase (streamované ze zdroje). Informace o nejběžnějších případech použití velkých objemů dat najdete v části [Scénáře použití služby HDInsight](#scenarios-for-using-hdinsight).

## <a name="why-should-i-use-azure-hdinsight"></a>Proč mám použít Azure HDInsight?

Tato část uvádí schopnosti služby Azure HDInsight.

|Schopnost  |Description  |
|---------|---------|
|Nativní pro cloud     |     Azure HDInsight umožňuje vytvářet optimalizované clustery pro služby Hadoop, Spark, [interaktivní dotazy (LLAP)](./interactive-query/apache-interactive-query-get-started.md), Kafka, proplavování, HBA a ml v Azure. HDInsight poskytuje také komplexní smlouvu SLA pro všechny vaše produkční úlohy.  |
|Škálovatelnost a nízké náklady     | HDInsight vám umožní škálovat úlohy nahoru nebo dolů.Můžete snížit náklady tím, že vytvoříte clustery na vyžádání a platíte jenom za to, co využijete. Můžete také sestavovat datové kanály pro zprovoznění úloh. Oddělený výpočetní výkon a úložiště poskytují lepší výkon a flexibilitu. |
|Bezpečnost a dodržování předpisů    | HDInsight umožňuje chránit datové prostředky vašeho podniku pomocí služby Azure Virtual Network, šifrování a integrace se službou Azure Active Directory. HDInsight také splňuje nejoblíbenější oborové a vládní standardy dodržování předpisů.        |
|Monitorování    | Azure HDInsight se integruje s protokoly Azure Monitor a poskytuje jedno rozhraní, se kterým můžete monitorovat všechny clustery.        |
|Globální dostupnost | HDInsight je k dispozici ve více oblastech než jakákoli jiná nabídka analýzy velkých objemů dat. Služba Azure HDInsight je dostupná také pro Azure Government, Čínu a Německo a umožňuje tak splnit požadavky vašeho podniku v klíčových suverénních oblastech. |  
|Produktivita     |  Azure HDInsight umožňuje používat bohaté nástroje zvyšující produktivitu pro Hadoop a Spark s oblíbeným vývojovým prostředím. Mezi tato vývojová prostředí patří Visual Studio, VSCode, Eclipse a IntelliJ pro zajištění podpory jazyků Scala, Python, R, Java a .NET. Odborníci přes data můžou také spolupracovat s využitím oblíbených poznámkových bloků, jako jsou Jupyter a Zeppelin.    |
|Rozšiřitelnost     |  Clustery HDInsight můžete rozšířit instalací komponent (Hue, Presto atd.) pomocí akcí skriptů, přidáním hraničních uzlů nebo integrací s dalšími aplikacemi certifikovanými pro práci s velkými objemy dat. HDInsight umožňuje bezproblémovou integraci s nejoblíbenějšími řešeními pro velké objemy dat prostřednictvím nasazení jedním kliknutím.|

## <a name="scenarios-for-using-hdinsight"></a>Scénáře použití služby HDInsight

Azure HDInsight je možné použít v nejrůznějších scénářích velkých objemů dat. Může se jednat o historická data (data, která jsou už shromážděná a uložená) nebo o data v reálném čase (data proudící přímo ze zdroje). Scénáře zpracování těchto dat můžeme shrnout do následujících kategorií:

### <a name="batch-processing-etl"></a>Dávkové zpracování (ETL)

Extrakce, transformace a načítání (ETL) je proces, při kterém se nestrukturovaná nebo strukturovaná data extrahují z heterogenních zdrojů dat. Potom se transformují do strukturovaného formátu a načítají do úložiště dat. Transformovaná data je možné použít pro datové vědy nebo datové sklady.

### <a name="data-warehousing"></a>Datové sklady

Pomocí služby HDInsight můžete provádět interaktivní dotazy v petabajtovém měřítku nad strukturovanými i nestrukturovanými daty v jakémkoli měřítku. Můžete také sestavovat modely, které je propojí s nástroji BI.

![Architektura HDInsight: Datové sklady](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png "Architektura HDInsight pro datové sklady")

### <a name="internet-of-things-iot"></a>Internet věcí (IoT)

Pomocí služby HDInsight můžete zpracovávat streamovaná data přijímaná v reálném čase z různých druhů zařízení. Pokud chcete získat další informace, [přečtěte si tento blogový příspěvek z Azure, který oznamuje verzi Public Preview pro Apache Kafka v HDInsightu se Spravovanými disky Azure](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![Architektura HDInsight: Internet věcí](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-iot.png "Architektura IoT HDInsight")

### <a name="data-science"></a>Datové vědy

Pomocí služby HDInsight můžete sestavovat aplikace, které z dat extrahují důležité informace. Navíc můžete pomocí služby Azure Machine Learning předvídat budoucí trendy pro vaši firmu. Další informace najdete v [tomto příběhu zákazníka](https://customers.microsoft.com/story/pros).

![Architektura HDInsight: Datové vědy](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-science.png "Architektura pro datové vědy pro HDInsight")

### <a name="hybrid"></a>Hybridní

Pomocí služby HDInsight můžete rozšířit svou stávající místní infrastrukturu velkých dat do Azure a využívat možnosti pokročilých analýz v cloudu.

![Architektura HDInsight: Hybridní](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png "Hybridní architektura HDInsight")

## <a name="cluster-types-in-hdinsight"></a>Typy clusterů ve službě HDInsight

HDInsight zahrnuje specifické typy clusterů a možnosti přizpůsobení clusterů, jako je například možnost přidávání komponent, nástrojů a jazyků. HDInsight nabízí následující typy clusteru:

|Typ clusteru | Description |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|Rozhraní, které používá HDFS, správu prostředků PŘÍZe a jednoduchý programovací model MapReduce pro souběžné zpracování a analýzu dat Batch.|
|[Apache Spark](./spark/apache-spark-overview.md)|Open source architektura paralelního zpracování, která podporuje zpracování v paměti pro zvýšení výkonu aplikací pro analýzu velkých objemů dat. Podívejte [se, co je Apache Spark v HDInsight?](./spark/apache-spark-overview.md).|
|[Apache HBase](./hbase/apache-hbase-overview.md)|Databáze NoSQL postavená na systému Hadoop, která poskytuje náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat – potenciálně miliardy řádků krát miliony sloupců. Přečtěte si téma [Co je HBase v HDInsight?](./hbase/apache-hbase-overview.md)|
|[Služby ML](./r-server/r-server-overview.md)|Server pro hostování a správu paralelních, distribuovaných procesů R. Poskytuje datovým vědcům, statistikům a programátorům v R přístup ke škálovatelným, distribuovaným analytickým metodám služby HDInsight na vyžádání. Viz [Přehled ML Services ve službě HDInsight](./r-server/r-server-overview.md).|
|[Apache Storm](./storm/apache-storm-overview.md)|Distribuovaný výpočetní systém v reálném čase pro rychlé zpracování velkých proudů dat. Storm je poskytován jako spravovaný cluster v prostředí HDInsight. Viz [Analýza dat snímačů v reálném čase pomocí nástrojů Storm a Hadoop](./storm/apache-storm-overview.md).|
|[Interaktivní dotaz Apache](./interactive-query/apache-interactive-query-get-started.md)|Ukládání do mezipaměti v paměti pro interaktivní a rychlejší dotazy na podregistr. Viz [Použití Interactive Query ve službě HDInsight](./interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|Open Source platforma, která se používá k sestavování kanálů a aplikací streamování dat. Kafka také poskytuje funkce propojující fronty zpráv, pomocí kterých můžete publikovat datové streamy a přihlašovat se k jejich odběru. Viz [Úvod k Apache Kafka ve službě HDInsight](./kafka/apache-kafka-introduction.md).|

## <a name="open-source-components-in-hdinsight"></a>Opensourcové komponenty ve službě HDInsight

Azure HDInsight umožňuje vytvářet clustery s open source rozhraními, jako jsou Hadoop, Spark, podregistr, LLAP, Kafka, vyplavení, HBA a R. Tyto clustery ve výchozím nastavení přidávají s dalšími Open Source komponentami, které jsou součástí clusteru, jako je Apache Ambari5, Avro5, Apache Hive3, HCatalog2, Apache Mahout2, Apache Hadoop MapReduce3, Apache Hadoop YARN2, Apache Phoenix3, Apache Pig3, Apache Sqoop3, Apache Tez3, Apache Oozie2 a Apache ZooKeeper5.  

## <a name="programming-languages-in-hdinsight"></a>Programovací jazyky v prostředí HDInsight

Clustery HDInsight, včetně clusterů Spark, HBase, Kafka, Hadoop a dalších, podporují celou řadu programovacích jazyků. Některé z nich ale nejsou ve výchozím nastavení nainstalované. Pro knihovny, moduly nebo balíčky, které nejsou nainstalované ve výchozím nastavení, použijte k instalaci komponenty akci skriptu.

|Programovací jazyk  |Informace  |
|---------|---------|
|Výchozí podpora programovacích jazyků     | Ve výchozím nastavení podporují clustery prostředí HDInsight tyto jazyky:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Přejít</li></ul>  |
|Jazyky Java virtual machine (JVM)     | V prostředí Java Virtual Machine (JVM) je možné spouštět celou řadu jiných jazyků, než je Java. Pokud ale některé z těchto jazyků spustíte, může být v clusteru potřeba nainstalovat další součásti. Clustery prostředí HDInsight podporují následující jazyky založené na JVM: <ul><li>Clojure</li><li>Jython (Python pro jazyk Java)</li><li>Scala</li></ul>     |
|Jazyky pro Hadoop     | Clustery HDInsight podporují následující jazyky, které jsou určené konkrétně pro technologii Hadoop: <ul><li>Pig Latin pro úlohy Pig</li><li>HiveQL pro úlohy Hive a SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>Vývojářské nástroje pro HDInsight

Vývojářské nástroje pro HDInsight, včetně nástrojů IntelliJ, Eclipse, Visual Studio Code a Visual Studio, můžete díky bezproblémové integraci s Azure použít k vytváření a odesílání úloh a dotazů na data HDInsight.

* Sada Azure Toolkit pro IntelliJ10
* Sada Azure Toolkit pro Eclipse6
* Nástroje Azure HDInsight pro VS Code13
* Nástroje Azure Data Lake pro Visual Studio9

## <a name="business-intelligence-on-hdinsight"></a>Business intelligence ve službě HDInsight

Známé nástroje business intelligence (BI) načítají, analyzují a vykazují data integrovaná v prostředí HDInsight buď pomocí doplňku Power Query, nebo ovladače Microsoft Hive ODBC Driver:

* [Apache Spark BI s využitím nástrojů pro vizualizaci dat ve službě Azure HDInsight](./spark/apache-spark-use-bi-tools.md)

* [Vizualizace Apache Hive dat pomocí Microsoft Power BI ve službě Azure HDInsight](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [Vizualizace dat Interactive Query Hive pomocí Power BI ve službě Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Připojení Excelu k Apache Hadoop s](./hadoop/apache-hadoop-connect-excel-power-query.md) využitím Power Query (vyžaduje Windows)

* [Připojení Excelu k Apache Hadoop pomocí Microsoft Hive ODBC Driver](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (vyžaduje Windows)


## <a name="in-region-data-residency"></a>Zasídlí dat v oblasti 

Spark, Hadoop, LLAP, Hadoop a MLService neukládají zákaznická data, aby tyto služby automaticky splňovaly požadavky na umístění dat v regionu, včetně těch, které jsou uvedené v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net/). 

Kafka a HBA ukládají zákaznická data. Tato data se automaticky ukládají pomocí Kafka a HBA v jedné oblasti, takže tato služba splňuje požadavky na umístění dat v rámci oblasti, včetně těch, které jsou uvedené v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net/). 


Známé nástroje business intelligence (BI) načítají, analyzují a vykazují data, která jsou integrovaná do HDInsight, pomocí Power Queryho doplňku nebo Microsoft Hive ODBC Driver.

## <a name="next-steps"></a>Další kroky

* [Vytvoření clusteru Apache Hadoop v HDInsight](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Vytvoření clusteru Apache Spark – portál](./spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Podnikové zabezpečení ve službě Azure HDInsight](./domain-joined/hdinsight-security-overview.md)
