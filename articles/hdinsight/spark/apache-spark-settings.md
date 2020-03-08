---
title: Konfigurace nastavení Sparku – Azure HDInsight
description: Jak zobrazit a nakonfigurovat nastavení Apache Spark pro cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 48f19e5da8c7703cc597518246c2f62ebce3ae17
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397094"
---
# <a name="configure-apache-spark-settings"></a>Konfigurace nastavení Apache Sparku

Cluster An HDInsight Spark zahrnuje instalaci knihovny [Apache Spark](https://spark.apache.org/) .  Každý cluster HDInsight obsahuje výchozí parametry konfigurace pro všechny nainstalované služby, včetně Sparku.  Klíčovým aspektem správy clusteru HDInsight Apache Hadoop je monitorování úloh, včetně úloh Spark, aby se zajistilo, že úlohy budou spuštěné předvídatelným způsobem. Pokud chcete, aby se úlohy Sparku co nejlépe spouštěly, zvažte konfiguraci fyzického clusteru při určování, jak optimalizovat logickou konfiguraci clusteru.

Výchozí cluster HDInsight Apache Spark zahrnuje následující uzly: tři uzly [Apache Zookeeper](https://zookeeper.apache.org/) , dva hlavní uzly a jeden nebo více pracovních uzlů:

![Architektura Spark HDInsight](./media/apache-spark-settings/spark-hdinsight-arch.png)

Konfigurace pro uzly v clusteru HDInsight může mít vliv na počet virtuálních počítačů a velikost virtuálních počítačů. Nevýchozí hodnoty konfigurace HDInsight často vyžadují hodnoty konfigurace Sparku jiné než výchozí. Při vytváření clusteru HDInsight Spark se zobrazí doporučené velikosti virtuálních počítačů pro každou součást. V současné době jsou [velikosti virtuálních počítačů se systémem Linux optimalizované pro paměť](../../virtual-machines/linux/sizes-memory.md) pro Azure D12 v2 nebo vyšší.

## <a name="apache-spark-versions"></a>Verze Apache Spark

Pro svůj cluster použijte nejlepší verzi Sparku.  Služba HDInsight zahrnuje několik verzí Spark i HDInsight.  Každá verze Sparku obsahuje sadu výchozích nastavení clusteru.  

Když vytváříte nový cluster, existuje několik verzí Spark, ze kterých si můžete vybrat. Zobrazení úplného seznamu [komponenty a verzí HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> Výchozí verze Apache Spark ve službě HDInsight se může změnit bez předchozího upozornění. Pokud máte závislost verze, společnost Microsoft doporučuje zadat tuto konkrétní verzi při vytváření clusterů pomocí sady .NET SDK, Azure PowerShell a Azure Classic CLI.

Apache Spark má tři umístění konfigurace systému:

* Vlastnosti Sparku řídí většinu parametrů aplikace a lze je nastavit pomocí objektu `SparkConf` nebo pomocí vlastností systému Java.
* Proměnné prostředí lze použít k nastavení nastavení pro jednotlivé počítače, jako je například IP adresa, prostřednictvím skriptu `conf/spark-env.sh` v každém uzlu.
* Protokolování se dá nakonfigurovat pomocí `log4j.properties`.

Když vyberete konkrétní verzi Sparku, cluster obsahuje výchozí nastavení konfigurace.  Výchozí hodnoty konfigurace Sparku můžete změnit pomocí vlastního konfiguračního souboru Spark.  Příklad je uveden níže.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

Výše uvedený příklad přepisuje několik výchozích hodnot pro pět parametrů konfigurace Spark.  Jedná se o Kompresní kodek, Apache Hadoop MapReduce rozdělení minimální velikosti a bloků Parquet a také oddíly Spar SQL a otevřít výchozí hodnoty velikosti souborů.  Tyto změny konfigurace se volí, protože přidružená data a úlohy (v tomto příkladu data genomiky) mají konkrétní charakteristiky, které budou lépe využívat tato vlastní nastavení konfigurace.

---

## <a name="view-cluster-configuration-settings"></a>Zobrazit nastavení konfigurace clusteru

Před provedením optimalizace výkonu v clusteru ověřte aktuální nastavení konfigurace clusteru HDInsight. Spusťte řídicí panel HDInsight z Azure Portal kliknutím na odkaz **řídicí panel** v podokně clusteru Spark. Přihlaste se pomocí uživatelského jména a hesla správce clusteru.

Zobrazí se webové uživatelské rozhraní Apache Ambari se zobrazením řídicího panelu metriky využití prostředků clusteru klíče.  Na řídicím panelu Ambari se zobrazí konfigurace Apache Spark a další služby, které jste nainstalovali. Řídicí panel obsahuje kartu **Historie konfigurace** , kde můžete zobrazit informace o konfiguraci pro všechny nainstalované služby, včetně Sparku.

Pokud chcete zobrazit konfigurační hodnoty pro Apache Spark, vyberte **Historie konfigurace**a pak vyberte **Spark2**.  Vyberte kartu **Konfigurace** a potom v seznamu služba vyberte odkaz `Spark` (nebo `Spark2`podle vaší verze).  Zobrazí se seznam hodnot konfigurace pro váš cluster:

![Konfigurace Sparku](./media/apache-spark-settings/spark-configurations.png)

Pokud chcete zobrazit a změnit jednotlivé hodnoty konfigurace Sparku, vyberte v nadpisu odkazu všechny odkazy se slovem "Spark".  Konfigurace pro Spark zahrnují vlastní i pokročilé konfigurační hodnoty v těchto kategoriích:

* Vlastní Spark2 – výchozí
* Vlastní Spark2 – metriky – vlastnosti
* Rozšířené Spark2 – výchozí
* Advanced Spark2-ENV
* Advanced spark2-podregistr-site-override

Pokud vytvoříte jinou než výchozí sadu hodnot konfigurace, můžete také zobrazit historii aktualizací konfigurace.  Tato historie konfigurace může být užitečná k tomu, abyste viděli, která jiná než výchozí konfigurace má optimální výkon.

> [!NOTE]  
> Pokud chcete zobrazit, ale ne změnit, obecná nastavení konfigurace clusteru Spark, vyberte kartu **prostředí** v **uživatelském rozhraní úlohy Sparku** na nejvyšší úrovni.

## <a name="configuring-spark-executors"></a>Konfigurace prováděcích modulů Spark

Následující diagram znázorňuje klíčové objekty Spark: program ovladače a jeho přidružený kontext Spark a Správce clusteru a jeho uzly pracovního procesu *n* .  Každý pracovní uzel obsahuje instance úlohy prováděcího modulu, mezipaměti a *n* .

![Objekty clusteru](./media/apache-spark-settings/hdi-spark-architecture.png)

Úlohy Sparku používají pracovní prostředky, zejména paměť, takže je běžné upravovat hodnoty konfigurace Sparku pro vykonavatele pracovních uzlů.

Tři klíčové parametry, které se často upravují pro optimalizaci konfigurací Sparku pro zlepšení požadavků aplikace, jsou `spark.executor.instances`, `spark.executor.cores`a `spark.executor.memory`. Vykonavatel je proces, který se spustil pro aplikaci Spark. Prováděcí modul běží na pracovním uzlu a zodpovídá za úkoly aplikace. Pro každý cluster se počítá výchozí počet prováděcích modulů a velikosti prováděcího modulu, a to na základě počtu pracovních uzlů a velikosti pracovního uzlu. Tyto položky jsou uloženy v `spark-defaults.conf` na hlavních uzlech clusteru.  Tyto hodnoty můžete upravit ve spuštěném clusteru tak, že vyberete **vlastní odkaz Spark-Defaults** ve webovém uživatelském rozhraní Ambari.  Po provedení změn vás uživatelské rozhraní vyzve k **restartování** všech ovlivněných služeb.

> [!NOTE]  
> Tyto tři konfigurační parametry lze nakonfigurovat na úrovni clusteru (pro všechny aplikace, které jsou spuštěny v clusteru) a také jsou určeny pro každou jednotlivou aplikaci.

Dalším zdrojem informací o prostředcích, které využívají exekutory Spark, je uživatelské rozhraní aplikace Spark.  V uživatelském rozhraní Spark vyberte kartu **vykonavatelé** , chcete-li zobrazit souhrnné a podrobné zobrazení konfigurace a prostředků, které vykonavatelé využívané.  Tato zobrazení vám můžou pomoct určit, jestli máte změnit výchozí hodnoty exekutorů Spark pro celý cluster, nebo pro provádění konkrétní sady úloh.

![Prováděče Spark](./media/apache-spark-settings/apache-spark-executors.png)

Alternativně můžete pomocí REST API Ambari programově ověřit nastavení konfigurace clusteru HDInsight a Spark.  Další informace najdete v referenčních informacích k [rozhraní Apache Ambari API na GitHubu](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

V závislosti na konkrétní úloze Spark můžete zjistit, že jiná než výchozí konfigurace Sparku zajistí optimálnější provádění úloh Spark.  Při ověřování jakékoli jiné než výchozí konfigurace clusteru byste měli provést srovnávací testování s ukázkovými úlohami.  Tady je několik běžných parametrů, jejichž úpravu můžete zvážit:

* `--num-executors` nastaví počet prováděcích modulů.
* `--executor-cores` nastaví počet jader pro každý prováděcí modul. Doporučujeme používat vykonavatele střední velikosti, protože jiné procesy využívají také určitou část dostupné paměti.
* `--executor-memory` řídí velikost paměti (velikost haldy) každého prováděcího modulu v [Apache HADOOP přízi](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)a pro režijní náklady na spuštění bude nutné opustit určitou paměť.

Tady je příklad dvou pracovních uzlů s různými hodnotami konfigurace:

![Konfigurace dvou uzlů](./media/apache-spark-settings/executor-configuration.png)

V následujícím seznamu jsou uvedeny klíčové parametry paměti pro vykonavatele Spark.

* `spark.executor.memory` definuje celkovou velikost paměti, která je k dispozici pro prováděcí modul.
* `spark.storage.memoryFraction` (výchozí ~ 60%) definuje velikost paměti, která je k dispozici pro uložení trvalých RDD.
* `spark.shuffle.memoryFraction` (výchozí ~ 20%) definuje množství paměti rezervované pro přemístění.
* `spark.storage.unrollFraction` a `spark.storage.safetyFraction` (celková hodnota ~ 30% z celkové paměti) – tyto hodnoty se používají interně pomocí Spark a neměly by se měnit.

PŘÍZe řídí maximální součet paměti využívané kontejnery na jednotlivých uzlech Spark. Následující diagram znázorňuje vztahy jednotlivých uzlů mezi objekty konfigurace PŘÍZe a objekty Spark.

![Správa paměti PŘÍZe Spark](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Změna parametrů aplikace spuštěné v Jupyter poznámkovém bloku

Clustery Spark v HDInsight ve výchozím nastavení zahrnují několik součástí. Každá z těchto součástí zahrnuje výchozí hodnoty konfigurace, které můžete podle potřeby přepsat.

* Spark Core – Spark Core, Spark SQL, Spark streaming API, GraphX a Apache Spark MLlib.
* Anaconda – správce balíčků Pythonu.
* [Apache Livy](https://livy.incubator.apache.org/) – REST API Apache Spark, který slouží k odesílání vzdálených úloh do clusteru HDInsight Spark.
* [Jupyter](https://jupyter.org/) a [Apache Zeppelin](https://zeppelin.apache.org/) poznámkové bloky – interaktivní uživatelské rozhraní založené na prohlížeči pro interakci s clusterem Spark.
* Ovladač ODBC – propojuje Clustery Spark v HDInsight s nástroji business intelligence (BI), jako je Microsoft Power BI a Tableau.

Pro aplikace spuštěné v poznámkovém bloku Jupyter použijte příkaz `%%configure`, aby se změny konfigurace provedly v rámci samotného poznámkového bloku. Tyto změny konfigurace budou aplikovány na úlohy Spark spouštěné z vaší instance poznámkového bloku. Tyto změny byste měli provést na začátku aplikace před spuštěním první buňky kódu. Změněná konfigurace se použije pro relaci Livy při jejím vytvoření.

> [!NOTE]  
> Chcete-li změnit konfiguraci v pozdější fázi aplikace, použijte parametr `-f` (Force). Veškerý průběh aplikace však bude ztracen.

Následující kód ukazuje, jak změnit konfiguraci aplikace běžící v Jupyter poznámkovém bloku.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Závěr

Existuje několik základních nastavení konfigurace, která je potřeba monitorovat a upravovat, aby se zajistilo, že se úlohy Spark budou spouštět předvídatelným a vykonávajícím způsobem. Tato nastavení vám pomůžou určit nejlepší konfiguraci clusteru Spark pro vaše konkrétní úlohy.  Také budete muset monitorovat spouštění dlouhotrvajících úloh Sparku, které jsou náročné na prostředky a/nebo.  Nejběžnějším centrem pro problémy s tlakem na paměť v důsledku nesprávných konfigurací (zvláště nesprávně používaných prováděcích modulů), dlouhotrvajících operací a úloh, jejichž výsledkem jsou operace kartézském.

## <a name="next-steps"></a>Další kroky

* [Apache Hadoop součásti a verze, které jsou k dispozici v HDInsight?](../hdinsight-component-versioning.md)
* [Správa prostředků pro cluster Apache Spark v HDInsight](apache-spark-resource-manager.md)
* [Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími](../hdinsight-hadoop-provision-linux-clusters.md)
* [Konfigurace Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Spouštění Apache Spark v Apache Hadoop nitě](https://spark.apache.org/docs/latest/running-on-yarn.html)
