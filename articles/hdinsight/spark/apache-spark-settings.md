---
title: Konfigurace nastavení Spark - Azure HDInsight
description: Jak zobrazit a nakonfigurovat nastavení Apache Spark pro cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 48f19e5da8c7703cc597518246c2f62ebce3ae17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272016"
---
# <a name="configure-apache-spark-settings"></a>Konfigurace nastavení Apache Sparku

Cluster HDInsight Spark obsahuje instalaci knihovny [Apache Spark.](https://spark.apache.org/)  Každý cluster HDInsight obsahuje výchozí konfigurační parametry pro všechny nainstalované služby, včetně Spark.  Klíčovým aspektem správy clusteru HDInsight Apache Hadoop je monitorování úloh, včetně spark jobs, abyste se ujistili, že úlohy běží předvídatelným způsobem. Chcete-li nejlépe spustit úlohy Spark, zvažte konfiguraci fyzického clusteru při určování, jak optimalizovat logickou konfiguraci clusteru.

Výchozí cluster HDInsight Apache Spark obsahuje následující uzly: tři uzly [Apache ZooKeeper,](https://zookeeper.apache.org/) dva hlavní uzly a jeden nebo více pracovních uzlů:

![Architektura Spark HDInsight](./media/apache-spark-settings/spark-hdinsight-arch.png)

Počet virtuálních počítačů a velikosti virtuálních počítačů pro uzly v clusteru HDInsight může také ovlivnit konfiguraci Spark. Jiné než výchozí hodnoty konfigurace HDInsight často vyžadují jiné než výchozí hodnoty konfigurace Spark. Když vytvoříte cluster HDInsight Spark, zobrazí se navrhované velikosti virtuálních aplikací pro každou z komponent. V současné době [velikosti virtuálních zařízení Linuxu optimalizované pro paměť](../../virtual-machines/linux/sizes-memory.md) pro Azure jsou D12 v2 nebo vyšší.

## <a name="apache-spark-versions"></a>Verze Apache Spark

Použijte nejlepší verzi Spark pro váš cluster.  Služba HDInsight obsahuje několik verzí samotného Sparka i SAMOTNÉHO HDInsightu.  Každá verze Spark obsahuje sadu výchozích nastavení clusteru.  

Když vytvoříte nový cluster, můžete si vybrat z více verzí Spark. Chcete-li zobrazit úplný seznam, [komponenty a verze HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> Výchozí verze Apache Spark ve službě HDInsight se může změnit bez předchozího upozornění. Pokud máte závislost na verzi, společnost Microsoft doporučuje určit tuto konkrétní verzi při vytváření clusterů pomocí .NET SDK, Azure PowerShell a Azure Classic CLI.

Apache Spark má tři umístění konfigurace systému:

* Vlastnosti Spark řídí většinu parametrů aplikace a `SparkConf` lze je nastavit pomocí objektu nebo pomocí vlastností systému Java.
* Proměnné prostředí lze nastavit nastavení na počítač, jako je například `conf/spark-env.sh` IP adresa, prostřednictvím skriptu na každém uzlu.
* Protokolování lze `log4j.properties`konfigurovat prostřednictvím aplikace .

Když vyberete konkrétní verzi Spark, váš cluster obsahuje výchozí nastavení konfigurace.  Výchozí hodnoty konfigurace Spark můžete změnit pomocí vlastního konfiguračního souboru Spark.  Příklad najdete níže.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

Výše uvedený příklad přepíše několik výchozích hodnot pro pět konfiguračních parametrů Spark.  Jedná se o kompresní kodek, Apache Hadoop MapReduce rozdělit minimální velikost i velikostparketových bloků, a také Spar SQL oddíl a otevřené velikosti souborů výchozí hodnoty.  Tyto změny konfigurace jsou vybrány, protože přidružená data a úlohy (v tomto příkladu genomická data) mají určité charakteristiky, které budou fungovat lépe pomocí těchto vlastních nastavení konfigurace.

---

## <a name="view-cluster-configuration-settings"></a>Zobrazit nastavení konfigurace clusteru

Před provedením optimalizace výkonu v clusteru ověřte aktuální nastavení konfigurace clusteru HDInsight. Spusťte řídicí panel HDInsight z portálu Azure kliknutím na odkaz **Řídicí panel** v podokně clusteru Spark. Přihlaste se pomocí uživatelského jména a hesla správce clusteru.

Zobrazí se webové uživatelské uživatelské okno Apache Ambari s řídicím panelem klíčových metrik využití prostředků clusteru.  Řídicí panel Ambari zobrazuje konfiguraci Apache Spark a další služby, které jste nainstalovali. Řídicí panel obsahuje kartu **Historie konfigurace,** kde můžete zobrazit informace o konfiguraci pro všechny nainstalované služby, včetně Spark.

Chcete-li zobrazit hodnoty konfigurace pro Apache Spark, vyberte **Historie konfigurace**a pak vyberte **Spark2**.  Vyberte kartu **Configs** a `Spark` v `Spark2`seznamu služeb vyberte odkaz (nebo v závislosti na verzi).  Zobrazí se seznam konfiguračních hodnot pro váš cluster:

![Konfigurace jiskry](./media/apache-spark-settings/spark-configurations.png)

Chcete-li zobrazit a změnit jednotlivé hodnoty konfigurace Spark, vyberte libovolný odkaz se slovem "jiskra" v názvu odkazu.  Konfigurace pro Spark zahrnují vlastní i pokročilé hodnoty konfigurace v těchto kategoriích:

* Vlastní výchozí hodnoty Spark2
* Vlastní vlastnosti metrik Spark2
* Pokročilé výchozí hodnoty Spark2
* Pokročilé Spark2-env
* Pokročilé přepsání spark2-hive-site

Pokud vytvoříte nevýchozí sadu hodnot konfigurace, můžete také zobrazit historii aktualizací konfigurace.  Tato historie konfigurace může být užitečné zjistit, která konfigurace, která není výchozí, má optimální výkon.

> [!NOTE]  
> Chcete-li zobrazit, ale nezměnit, společné nastavení konfigurace clusteru Spark, vyberte kartu **Prostředí** na rozhraní uživatelského rozhraní **sparku nejvyšší** úrovně.

## <a name="configuring-spark-executors"></a>Konfigurace vykonavatelů sparku

Následující diagram znázorňuje klíčové objekty Spark: program ovladače a přidružený kontext spark a správce clusteru a jeho *pracovní uzly n.*  Každý pracovní uzel obsahuje instanci úloh executor, mezipaměť a *n* úloh.

![Objekty clusteru](./media/apache-spark-settings/hdi-spark-architecture.png)

Úlohy Spark používají pracovní prostředky, zejména paměť, takže je běžné upravit hodnoty konfigurace Spark pro vykonavatele pracovních uzlů.

Tři klíčové parametry, které jsou často upraveny tak, `spark.executor.instances` `spark.executor.cores`aby `spark.executor.memory`vyladily konfigurace Spark pro zlepšení požadavků na aplikaci, jsou , a . Exekutor je proces spuštěný pro aplikaci Spark. Vykonavatel běží na pracovní uzel a je zodpovědný za úkoly pro aplikaci. Pro každý cluster se vypočítá výchozí počet vykonavatelů a velikosti vykonavatelů na základě počtu pracovních uzlů a velikosti pracovního uzlu. Ty jsou `spark-defaults.conf` uloženy v hlavní uzly clusteru.  Tyto hodnoty můžete upravit v běžícím clusteru výběrem odkazu **Vlastní výchozí jiskra** v uživatelském okně webu Ambari.  Po provedené mši se zobrazí výzva k **restartování** všech ohrožených služeb.

> [!NOTE]  
> Tyto tři konfigurační parametry lze nakonfigurovat na úrovni clusteru (pro všechny aplikace spuštěné v clusteru) a také zadat pro každou jednotlivou aplikaci.

Dalším zdrojem informací o prostředcích, které využívají exekutory Spark, je uživatelské rozhraní aplikace Spark.  V uzdvižení vyberte kartu **Executors,** která zobrazí souhrnné a podrobné zobrazení konfigurace a prostředků spotřebovaných vykonavateli.  Tato zobrazení vám můžou pomoct určit, jestli máte změnit výchozí hodnoty exekutorů Spark pro celý cluster, nebo pro provádění konkrétní sady úloh.

![Jiskra Executors](./media/apache-spark-settings/apache-spark-executors.png)

Případně můžete použít rozhraní API Ambari REST k programovému ověření nastavení konfigurace clusteru HDInsight a Spark.  Více informací je k dispozici na [Apache Ambari API odkaz na GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

V závislosti na konkrétní úloze Spark můžete zjistit, že jiná než výchozí konfigurace Sparku zajistí optimálnější provádění úloh Spark.  Při ověřování jakékoli jiné než výchozí konfigurace clusteru byste měli provést srovnávací testování s ukázkovými úlohami.  Tady je několik běžných parametrů, jejichž úpravu můžete zvážit:

* `--num-executors`nastaví počet vykonavatelů.
* `--executor-cores`nastaví počet jader pro každého vykonavatele. Doporučujeme používat středně velké vykonavatele, protože jiné procesy také spotřebovávají část dostupné paměti.
* `--executor-memory`řídí velikost paměti (velikost haldy) každého vykonavatele na [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)a budete muset ponechat nějakou paměť pro režii spuštění.

Zde je příklad dvou pracovních uzlů s různými hodnotami konfigurace:

![Konfigurace dvou uzlů](./media/apache-spark-settings/executor-configuration.png)

V následujícím seznamu jsou uvedeny klíčové parametry paměti vykonavatele Spark.

* `spark.executor.memory`definuje celkové množství paměti dostupné pro vykonavatele.
* `spark.storage.memoryFraction`(výchozí ~60%) definuje množství paměti, která je k dispozici pro ukládání trvalých RDD.
* `spark.shuffle.memoryFraction`(výchozí ~20%) definuje velikost paměti vyhrazené pro náhodné přehrávání.
* `spark.storage.unrollFraction`a `spark.storage.safetyFraction` (celkem ~30% celkové paměti) - tyto hodnoty jsou používány interně Spark a neměly by být změněny.

YARN řídí maximální součet paměti používané kontejnery na každém uzlu Spark. Následující diagram znázorňuje vztahy mezi objekty konfigurace YARN a objekty Spark na uzel.

![Správa paměti YARN Spark](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Změna parametrů pro aplikaci spuštěnou v notebooku Jupyter

Clustery Spark ve službě HDInsight ve výchozím nastavení obsahují několik součástí. Každá z těchto součástí obsahuje výchozí hodnoty konfigurace, které lze podle potřeby přepsat.

* Spark Core – Spark Core, Spark SQL, Spark streamovaná api, GraphX a Apache Spark MLlib.
* Anaconda - správce balíčků pythonu.
* [Apache Livy](https://livy.incubator.apache.org/) - Apache Spark REST API, které se používá k odesílání vzdálených úloh do clusteru HDInsight Spark.
* [Notebooky Jupyter](https://jupyter.org/) a [Apache Zeppelin](https://zeppelin.apache.org/) – interaktivní ui založené na prohlížeči pro interakci s vaším clusterem Spark.
* Ovladač ODBC – propojuje clustery Spark v HDInsight u nástrojů business intelligence (BI), jako je Microsoft Power BI a Tableau.

U aplikací spuštěných v poznámkovém `%%configure` bloku Jupyter použijte příkaz k provádění změn konfigurace z poznámkového bloku samotného. Tyto změny konfigurace se použijí na úlohy Spark spuštěné z instance poznámkového bloku. Tyto změny byste měli provést na začátku aplikace před spuštěním první buňky kódu. Změněná konfigurace se použije pro relaci Livy, když se vytvoří.

> [!NOTE]  
> Chcete-li změnit konfiguraci v pozdější fázi `-f` aplikace, použijte parametr (force). Veškerý pokrok v aplikaci však bude ztracen.

Níže uvedený kód ukazuje, jak změnit konfiguraci pro aplikaci spuštěnou v poznámkovém bloku Jupyter.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Závěr

Existuje celá řada základních nastavení konfigurace, které je třeba sledovat a upravit, abyste zajistili, že vaše úlohy Spark budou spuštěny předvídatelným a výkonným způsobem. Tato nastavení pomáhají určit nejlepší konfiguraci clusteru Spark pro vaše konkrétní úlohy.  Budete také muset sledovat provádění dlouhotrvající chod a nebo prostředky náročné spuštění úloh spark.  Nejčastější problémy se soustřeďují kolem tlaku paměti kvůli nesprávným konfiguracím (zejména nesprávně velikosti vykonavatelů), dlouhotrvajícím operacím a úlohám, které vedou k kartézovým operacím.

## <a name="next-steps"></a>Další kroky

* [Apache Hadoop komponenty a verze k dispozici s HDInsight?](../hdinsight-component-versioning.md)
* [Správa prostředků pro cluster Apache Spark na HDInsightu](apache-spark-resource-manager.md)
* [Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími](../hdinsight-hadoop-provision-linux-clusters.md)
* [Konfigurace Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Běh Apache Spark na Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
