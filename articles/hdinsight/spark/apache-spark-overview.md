---
title: Co je Apache Spark – Azure HDInsight
description: Tento článek představuje Spark ve službě HDInsight a různé scénáře, ve kterých můžete použít cluster Spark ve službě HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: 284c9bb1b9032d2f9caf0ce62c45ffa7cd8bf9d4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68476859"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Co je Apache Spark ve službě Azure HDInsight

Apache Spark je paralelní procesor pro zpracování, který podporuje zpracování v paměti, aby se zvýšil výkon analytických aplikací s velkým objemem dat. Apache Spark ve službě Azure HDInsight je implementace Apache Sparku v cloudu od Microsoftu. HDInsight usnadňuje vytvoření a konfiguraci clusteru Spark v Azure. Clustery Spark v HDInsight jsou kompatibilní s Azure Storage a Azure Data Lake Storage. Clustery HDInsight Spark proto můžete použít ke zpracování dat uložených v Azure. Součásti a informace o verzi najdete v tématu [Apache Hadoop komponenty a verze ve službě Azure HDInsight](../hdinsight-component-versioning.md).

![Spark: jednotné rozhraní](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Co je Apache Spark?

Spark poskytuje primitivy pro clusterové výpočty v paměti. Úloha Spark může načítat data, ukládat je do paměti a opakovaně je dotazovat. Výpočetní prostředí je mnohem rychlejší než aplikace založené na discích, jako je Hadoop, které sdílí data prostřednictvím systému Hadoop Distributed File System (HDFS). Spark se také integruje do programovacího jazyka Scala a díky tomu umožňuje pracovat s distribuovanými datovými sadami stejně jako s místními kolekcemi. Není nutné strukturovat všechno jako mapovací a redukční operace.

![Tradiční MapReduce vs. Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

Clustery Spark ve službě HDInsight nabízejí plně spravovanou službu Spark. Tady najdete výhody, které přináší vytvoření clusteru Spark ve službě HDInsight.

| Funkce | Popis |
| --- | --- |
| Snadné vytvoření |Nový cluster Spark ve službě HDInsight můžete vytvořit během několika minut pomocí webu Azure Portal, Azure PowerShellu nebo sady HDInsight .NET SDK. Viz Začínáme [s clusterem Apache Spark v HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Snadné používání |Cluster Spark v HDInsight zahrnuje notebooky Jupyter a Apache Zeppelin. Tyto poznámkové bloky můžete použít pro interaktivní zpracování dat a vizualizaci.|
| Rozhraní REST API |Clustery Spark v HDInsight zahrnují [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), server pro Spark založený na REST API, který umožňuje vzdáleně odesílat a monitorovat úlohy. Přečtěte si téma [použití Apache Spark REST API k odeslání vzdálených úloh do clusteru HDInsight Spark](apache-spark-livy-rest-interface.md).|
| Podpora Azure Data Lake Storage | Clustery Spark v HDInsight můžou použít Azure Data Lake Storage jako primární úložiště i pro další úložiště. Další informace o Data Lake Storage najdete v tématu [přehled Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integrace se službami Azure |Cluster Spark ve službě HDInsight se dodává s konektorem k Azure Event Hubs. Můžete vytvářet aplikace streamování pomocí Event Hubs kromě [Apache Kafka](https://kafka.apache.org/), která je již k dispozici jako součást Sparku. |
| Podpora pro ML Server | Podpora pro ML Server v HDInsightu je k dispozici jako typ clusteru **ML Services**. Cluster ML Services můžete nastavit pro spouštění distribuovaných výpočtů v R s rychlostí, kterou zajišťuje cluster Spark. Další informace najdete v tématu [co je ml Services ve službě Azure HDInsight](../r-server/r-server-overview.md). |
| Integrace v prostředí IDE třetích stran | HDInsight poskytuje několik modulů plug-in pro integrovaná vývojová prostředí (IDE), které jsou užitečné k vytváření a odesílání aplikací do clusteru HDInsight Spark. Další informace najdete v tématu [použití Azure Toolkit for INTELLIJ nápadu](apache-spark-intellij-tool-plugin.md), [použití nástrojů pro & pro Spark pro VSCode](../hdinsight-for-vscode.md)a [použití Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Počet souběžných dotazů |Clustery Spark v HDInsight podporují souběžné dotazy. Tato schopnost umožňuje sdílení stejných prostředků clusteru pro více dotazů od jednoho uživatele nebo více dotazů od různých uživatelů a aplikací. |
| Ukládání do mezipaměti na SSD |Data do mezipaměti můžete ukládat volitelně buď do paměti nebo na SSD disky připojené k uzlům clusteru. Ukládání do mezipaměti zajišťuje nejlepší výkon dotazů, ale může být nákladné. Ukládání na disky SSD poskytuje skvělou možnost pro zlepšení výkonu dotazů, aniž by bylo nutné vytvořit cluster velikosti, která je potřeba pro umístění celé datové sady do paměti. |
| Integrace s nástroji BI |Clustery Spark ve službě HDInsight nabízí konektory pro nástroje BI, například [Power BI](https://www.powerbi.com/) pro analýzu dat. |
| Předem zavedené knihovny Anaconda |Clustery Spark ve službě HDInsight obsahují předinstalované knihovny Anaconda. [Anaconda](https://docs.continuum.io/anaconda/) poskytuje téměř 200 knihoven pro machine learning, analýzy dat, vizualizace atd. |
| Škálovatelnost | HDInsight umožňuje změnit počet uzlů clusteru. Clustery Spark je také možné vyřadit bez ztráty dat, protože všechna data jsou uložená v Azure Storage nebo Data Lake Storage. |
| SLA |Clustery Spark ve službě HDInsight přináší nepřetržitou podporu a smlouvu SLA zajišťující 99,9% dostupnost. |

Clustery Apache Spark v HDInsight obsahují následující součásti, které jsou ve výchozím nastavení k dispozici v clusterech.

* [Spark Core](https://spark.apache.org/docs/latest/). Obsahuje Spark Core, Spark SQL, rozhraní API pro vysílání datového proudu Spark, GraphX a MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Poznámkový blok Jupyter](https://jupyter.org)
* [Notebook Apache Zeppelin](http://zeppelin-project.org/)

Clustery Spark ve službě HDInsight také poskytují [ovladač ODBC](https://go.microsoft.com/fwlink/?LinkId=616229) umožňující připojení ke clusterům Spark ve službě HDInsight z nástrojů BI, například Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Architektura clusteru Spark

![Architektura HDInsight Sparku](./media/apache-spark-overview/spark-architecture.png)

Komponenty Sparku snadno pochopíte, když porozumíte způsobu, jakým se Spark spouští na clusterech HDInsight.

Aplikace Spark se v clusteru spouští jako nezávislé sady procesů, které koordinuje objekt SparkContext v hlavním programu (označuje se jako řídicí program).

SparkContext se může připojit k několika typům správců clusterů, kteří přidělují prostředky napříč aplikacemi. Tito správci clusteru zahrnují [Apache Mesos](https://mesos.apache.org/), [Apache Hadoop nitě](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)nebo Správce clusteru Spark. V HDInsight se Spark spouští pomocí správce clusteru YARN. Po připojení Spark získá exekutory na pracovních uzlech v clusteru, což jsou procesy, které provádí výpočty a ukládají data pro vaši aplikaci. Pak do exekutorů odešle kód vaší aplikace (definovaný v souborech JAR nebo Pythonu předaných do objektu SparkContext). Nakonec SparkContext odešle do exekutorů úlohy ke spuštění.

SparkContext spouští hlavní funkci uživatele a provádí různé paralelní operace na pracovních uzlech. Potom SparkContext shromažďuje výsledky těchto operací. Pracovní uzly čtou a zapisují data z a do systému Hadoop Distributed File System. Pracovní uzly také ukládají transformovaná data do paměti jako sady RDD (Resilient Distributed Dataset).

SparkContext se připojuje k hlavnímu uzlu Sparku a je zodpovědný za převod aplikace do orientovaného acyklického grafu (DAG) jednotlivých úloh, které se provádějí v rámci procesu exekutora na pracovních uzlech. Každá aplikace získá vlastní procesy exekutora, které zůstávají v provozu po dobu trvání celé aplikace a spouští úlohy ve více vláknech.

## <a name="spark-in-hdinsight-use-cases"></a>Případy použití Sparku ve službě HDInsight

Clustery Spark v HDInsight podporují následující klíčové scénáře:

- Interaktivní analýzu dat a BI

    Apache Spark v HDInsight ukládá data do Azure Storage nebo Azure Data Lake Storage. Obchodní specialisté a osoby provádějící klíčová rozhodnutí analyzují a vytváří z těchto dat sestavy a používají Microsoft Power BI pro vytváření interaktivních sestav z analyzovaných dat. Analytici mohou začínat z nestrukturovaných / částečně strukturovaných dat v úložišti clusteru, definovat schéma pro data s využitím poznámkových bloků a následně vytvořit modely dat pomocí Microsoft Power BI. Clustery Spark ve službě HDInsight podporují také různé nástroje BI třetích stran, například Tableau, a usnadňují tak práci datovým analytikům, obchodním specialistům a osobám provádějícím klíčová rozhodnutí.

    [Kurz: Vizualizace dat Sparku pomocí Power BI](apache-spark-use-bi-tools.md)

- Spark Machine Learning

    Systém Apache Spark je vybavený knihovnou [MLlib](https://spark.apache.org/mllib/) pro strojové učení, jejímž základem je Spark a kterou můžete používat z clusteru Spark v HDInsight. Cluster Spark ve službě HDInsight obsahuje také Anacondu, distribuci jazyka Python s různými balíčky pro strojové učení. Spojte tyto možnosti s integrovanou podporou pro poznámkové bloky Jupyter a Zeppelin a máte prostředí pro tvorbu aplikací strojového učení.

    [Kurz: Předpověď sestavování teplot pomocí dat TVK](apache-spark-ipython-notebook-machine-learning.md)  
    [Kurz: Předpověď výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)

- Vysílání datových proudů a analýza dat v reálném čase ve Sparku

    Clustery Spark v HDInsight nabízí bohatou podporu pro vytváření řešení pro analýzu v reálném čase. Zatímco Spark již obsahuje konektory pro načítání dat z mnoha zdrojů, například soketů Kafka, Flume, Twitter, ZeroMQ nebo TCP, Spark v HDInsight přidává prvotřídní podporu pro příjem dat z Azure Event Hubs. Event Hubs je nejpoužívanější službou řazení front v Azure. Díky integrované podpoře služby Event Hubs představují clustery Spark ve službě HDInsight ideální platformu pro vytvoření kanálu k analýze dat v reálném čase.

## <a name="where-do-i-start"></a>Kde mám začít?

Další informace o Apache Spark ve službě HDInsight najdete v následujících článcích:

- [Rychlé zprovoznění: Vytvoření clusteru Apache Spark v HDInsight a spuštění interaktivního dotazu pomocí Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
- [Kurz: Spuštění Apache Spark úlohy pomocí Jupyter](./apache-spark-load-data-run-query.md)
- [Kurz: Analýza dat pomocí nástrojů BI](./apache-spark-use-bi-tools.md)
- [Kurz: Strojové učení s využitím Apache Spark](./apache-spark-ipython-notebook-machine-learning.md)
- [Kurz: Vytvoření aplikace Scala Maven pomocí IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Další kroky

V tomto článku jste získali základní přehled Apache Sparku ve službě Azure HDInsight. V dalším článku se dozvíte, jak vytvořit cluster HDInsight Spark a spustit několik dotazů Spark SQL:

- [Vytvoření clusteru Apache Spark v HDInsight](./apache-spark-jupyter-spark-sql-use-portal.md)
