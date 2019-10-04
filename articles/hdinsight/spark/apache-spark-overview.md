---
title: Co je Apache Spark – Azure HDInsight
description: Tento článek poskytuje Úvod do Sparku ve službě HDInsight a různé scénáře, ve kterých můžete v HDInsight použít cluster Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 10/01/2019
ms.openlocfilehash: 923e5a961ee9b7bdea94cf7a3e6fc833f596fafd
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937610"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Co je Apache Spark ve službě Azure HDInsight

Apache Spark je paralelní procesor pro zpracování, který podporuje zpracování v paměti, aby se zvýšil výkon analytických aplikací s velkým objemem dat. Apache Spark ve službě Azure HDInsight je implementace Apache Spark v cloudu od Microsoftu. HDInsight usnadňuje vytváření a konfiguraci clusteru Spark v Azure. Clustery Spark v HDInsight jsou kompatibilní s Azure Storage a Azure Data Lake Storage. Díky tomu můžete použít clustery HDInsight Spark ke zpracování dat uložených v Azure. Součásti a informace o verzi najdete v tématu [Apache Hadoop komponenty a verze ve službě Azure HDInsight](../hdinsight-component-versioning.md).

![Spark: sjednocený rámec](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Co je Apache Spark?

Spark poskytuje primitivní prvky pro výpočetní clustery v paměti. Úloha Sparku může načítat data a ukládat je do paměti a opakovaně je dotazovat. Výpočetní prostředí je mnohem rychlejší než aplikace založené na discích, jako je Hadoop, které sdílí data prostřednictvím systému Hadoop Distributed File System (HDFS). Spark se taky integruje do programovacího jazyka Scala, aby vám umožnil manipulovat s distribuovanými datovými sadami, jako jsou místní kolekce. Není potřeba strukturovat všechno jako mapování a snižovat operace.

![Tradiční MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark1.png)

Clustery Spark v HDInsight nabízejí plně spravovanou službu Spark. Tady jsou uvedené výhody vytvoření clusteru Spark v HDInsight.

| Funkce | Popis |
| --- | --- |
| Snadné vytvoření |Během několika minut můžete vytvořit nový cluster Spark v HDInsight pomocí Azure Portal, Azure PowerShell nebo sady HDInsight .NET SDK. Viz Začínáme [s clusterem Apache Spark v HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Snadné použití |Cluster Spark v HDInsight zahrnuje notebooky Jupyter a Apache Zeppelin. Tyto poznámkové bloky můžete použít pro interaktivní zpracování dat a vizualizaci.|
| Rozhraní REST API |Clustery Spark v HDInsight zahrnují [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), server pro Spark založený na REST API, který umožňuje vzdáleně odesílat a monitorovat úlohy. Přečtěte si téma [použití Apache Spark REST API k odeslání vzdálených úloh do clusteru HDInsight Spark](apache-spark-livy-rest-interface.md).|
| Podpora Azure Data Lake Storage | Clustery Spark v HDInsight můžou použít Azure Data Lake Storage jako primární úložiště i pro další úložiště. Další informace o Data Lake Storage najdete v tématu [přehled Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integrace se službami Azure |Cluster Spark v HDInsight se dodává s konektorem pro Azure Event Hubs. Můžete vytvářet aplikace streamování pomocí Event Hubs kromě [Apache Kafka](https://kafka.apache.org/), která je již k dispozici jako součást Sparku. |
| Podpora ML Server | Podpora ML Server v HDInsight je poskytována jako typ clusteru **služby ml** . Cluster služby ML můžete nastavit tak, aby běžel distribuované výpočty R s rychlostmi přislíbenými u clusteru Spark. Další informace najdete v tématu [co je ml Services ve službě Azure HDInsight](../r-server/r-server-overview.md). |
| Integrace s prostředím jiného výrobce | HDInsight poskytuje několik modulů plug-in pro IDE, které jsou užitečné pro vytváření a odesílání aplikací do clusteru HDInsight Spark. Další informace najdete v tématu [použití Azure Toolkit for INTELLIJ nápadu](apache-spark-intellij-tool-plugin.md), [použití nástrojů pro & pro Spark pro VSCode](../hdinsight-for-vscode.md)a [použití Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Souběžné dotazy |Clustery Spark v HDInsight podporují souběžné dotazy. Tato funkce umožňuje více dotazů od jednoho uživatele nebo několika dotazů od různých uživatelů a aplikací ke sdílení stejných prostředků clusteru. |
| Ukládání do mezipaměti v SSD |Data můžete ukládat do mezipaměti buď v paměti, nebo v SSD připojené k uzlům clusteru. Ukládání do paměti poskytuje nejlepší výkon dotazů, ale může být nákladné. Ukládání do mezipaměti v SSD poskytuje skvělou možnost pro zlepšení výkonu dotazů, aniž by bylo nutné vytvořit cluster s velikostí, která je požadována pro celou datovou sadu v paměti. |
| Integrace s nástroji BI |Clustery Spark v HDInsight poskytují konektory pro nástroje BI, jako je [Power BI](https://www.powerbi.com/) pro analýzu dat. |
| Předem načtené knihovny Anaconda |Clustery Spark v HDInsight přicházejí s předem instalovanými knihovnami Anaconda. [Anaconda](https://docs.continuum.io/anaconda/) poskytuje pro strojové učení, analýzu dat, vizualizaci a tak dále tyto knihovny: 200. |
| Škálovatelnost | HDInsight umožňuje změnit počet uzlů clusteru. Clustery Spark je také možné vyřadit bez ztráty dat, protože všechna data jsou uložená v Azure Storage nebo Data Lake Storage. |
| SLA |Clustery Spark v HDInsight přináší podporu 24/7 a smlouvu SLA o 99,9% dobu provozu. |

Clustery Apache Spark v HDInsight obsahují následující součásti, které jsou ve výchozím nastavení k dispozici v clusterech.

* [Spark Core](https://spark.apache.org/docs/latest/). Zahrnuje Spark Core, Spark SQL, rozhraní API pro streamování Spark, GraphX a MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Poznámkový blok Jupyter](https://jupyter.org)
* [Notebook Apache Zeppelin](http://zeppelin-project.org/)

Clustery Spark v HDInsight také poskytují [ovladač ODBC](https://go.microsoft.com/fwlink/?LinkId=616229) pro připojení ke clusterům Spark v HDInsight z nástrojů BI, jako je Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Architektura clusteru Spark

![Architektura HDInsight Spark](./media/apache-spark-overview/hdi-spark-architecture.png)

Je snadné pochopit komponenty Sparku tím, že pochopíte, jak Spark běží na clusterech HDInsight.

Aplikace Spark běží jako nezávislé sady procesů v clusteru, které jsou koordinovány objektem SparkContext v hlavním programu (nazývaný program ovladače).

SparkContext se může připojit k několika typům správců clusteru, které přidělují prostředky napříč aplikacemi. Tito správci clusteru zahrnují [Apache Mesos](https://mesos.apache.org/), [Apache Hadoop nitě](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)nebo Správce clusteru Spark. Ve službě HDInsight běží Spark pomocí Správce clusteru PŘÍZe. Po připojení Spark získá vykonavatele na uzlech Worker v clusteru, což jsou procesy, které spouštějí výpočty a ukládají data pro vaši aplikaci. Dále pošle kód vaší aplikace (definovaný JAR nebo soubory Pythonu, které jsou předány do SparkContext) vykonavatelům. Nakonec SparkContext odesílá úlohy do prováděcích modulů ke spuštění.

SparkContext spustí hlavní funkci uživatele a provede různé paralelní operace na pracovních uzlech. Pak SparkContext shromažďuje výsledky operací. Pracovní uzly čtou a zapisují data z a do systému Hadoop Distributed File System. Pracovní uzly také v mezipaměti transformují data v paměti jako odolné distribuované datové sady (RDD).

SparkContext se připojuje ke službě Spark Master a zodpovídá za převod aplikace na orientovaný graf (DAG) jednotlivých úloh, které se spustí v procesu prováděče na pracovních uzlech. Každá aplikace získá vlastní prováděcí procesy, které mají zůstat po celou dobu trvání celé aplikace a spouštějí úlohy ve více vláknech.

## <a name="spark-in-hdinsight-use-cases"></a>Spark ve službě HDInsight – případy použití

Clustery Spark v HDInsight umožňují následující klíčové scénáře:

* Interaktivní analýza dat a BI

    Apache Spark v HDInsight ukládá data do Azure Storage nebo Azure Data Lake Storage. Obchodní specialisté a tvůrci klíčových rozhodnutí mohou analyzovat a sestavovat sestavy z těchto dat a používat Microsoft Power BI k vytváření interaktivních sestav z analyzovaných dat. Analytiké můžou začít z nestrukturovaných/částečně strukturovaných dat v úložišti clusteru, definovat schéma pro data pomocí poznámkových bloků a potom vytvářet datové modely pomocí Power BI Microsoft. Clustery Spark v HDInsight také podporují řadu nástrojů BI jiných výrobců, jako je Tableau, které usnadňují analytikům dat, obchodním odborníkům a vedoucím klíčových rozhodnutí.

    [Kurz: vizualizace dat Sparku pomocí Power BI](apache-spark-use-bi-tools.md)

* Machine Learning Spark

    Apache Spark se dodává s [MLlib](https://spark.apache.org/mllib/), knihovny strojového učení postavené na Sparku, kterou můžete použít z clusteru Spark v HDInsight. Cluster Spark v HDInsight zahrnuje také Anaconda, distribuci Pythonu s různými druhy balíčků pro strojové učení. Podělte se o integrovanou podporu pro notebooky Jupyter a Zeppelin a máte prostředí pro vytváření aplikací strojového učení.

    [Kurz: předpověď sestavování teplot pomocí dat TVK](apache-spark-ipython-notebook-machine-learning.md)  
    [Kurz: předpověď výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)

* Streamování Sparku a analýza dat v reálném čase

    Clustery Spark v HDInsight nabízí bohatou podporu pro vytváření řešení pro analýzu v reálném čase. I když Spark již obsahuje konektory k ingestování dat z mnoha zdrojů, jako jsou Kafka, Flume, Twitter, ZeroMQ nebo TCP Sockets, Spark v HDInsight přidává prvotřídní podporu pro ingestování dat z Azure Event Hubs. Event Hubs je nejčastěji používaná služba řazení do fronty v Azure. Díky okamžité podpoře pro Event Hubs zpřístupňují Clustery Spark v HDInsight ideální platformu pro vytváření analytických kanálů v reálném čase.

## <a name="where-do-i-start"></a>Kde mám začít?

Další informace o Apache Spark ve službě HDInsight najdete v následujících článcích:

* [Rychlý Start: Vytvoření clusteru Apache Spark ve službě HDInsight a spuštění interaktivního dotazu pomocí Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Kurz: spuštění úlohy Apache Spark pomocí Jupyter](./apache-spark-load-data-run-query.md)
* [Kurz: Analýza dat pomocí nástrojů BI](./apache-spark-use-bi-tools.md)
* [Kurz: Machine Learning pomocí Apache Spark](./apache-spark-ipython-notebook-machine-learning.md)
* [Kurz: Vytvoření aplikace Scala Maven pomocí IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Další kroky

V tomto přehledu získáte základní porozumění Apache Spark ve službě Azure HDInsight. Přejděte k dalšímu článku, kde se dozvíte, jak vytvořit cluster HDInsight Spark a spustit některé dotazy Spark SQL:

* [Vytvoření clusteru Apache Spark v HDInsight](./apache-spark-jupyter-spark-sql-use-portal.md)
