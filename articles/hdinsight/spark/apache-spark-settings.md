---
title: Konfigurace nastavení Spark - Azure HDInsight
description: Jak zobrazit a nakonfigurovat nastavení Apache Spark pro cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: e13390067f8767e8c07b9c189264444e6d999a7a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411302"
---
# <a name="configure-apache-spark-settings"></a>Konfigurace nastavení Apache Sparku

Cluster HDInsight Spark obsahuje instalaci knihovny [Apache Spark.](https://spark.apache.org/)  Každý cluster HDInsight obsahuje výchozí konfigurační parametry pro všechny nainstalované služby, včetně Spark.  Klíčovým aspektem správy clusteru HDInsight Apache Hadoop je monitorování úloh, včetně Spark Jobs. Chcete-li nejlépe spustit úlohy Spark, zvažte konfiguraci fyzického clusteru při určování logické konfigurace clusteru.

Výchozí cluster HDInsight Apache Spark obsahuje následující uzly: tři uzly [Apache ZooKeeper,](https://zookeeper.apache.org/) dva hlavní uzly a jeden nebo více pracovních uzlů:

![Architektura Spark HDInsight](./media/apache-spark-settings/spark-hdinsight-arch.png)

Počet virtuálních počítačů a velikosti virtuálních počítačů pro uzly v clusteru HDInsight může ovlivnit konfiguraci Spark. Jiné než výchozí hodnoty konfigurace HDInsight často vyžadují jiné než výchozí hodnoty konfigurace Spark. Když vytvoříte cluster HDInsight Spark, zobrazí se navrhované velikosti virtuálních aplikací pro každou z komponent. V současné době [velikosti virtuálních zařízení Linuxu optimalizované pro paměť](../../virtual-machines/linux/sizes-memory.md) pro Azure jsou D12 v2 nebo vyšší.

## <a name="apache-spark-versions"></a>Verze Apache Spark

Použijte nejlepší verzi Spark pro váš cluster.  Služba HDInsight obsahuje několik verzí samotného Sparka i SAMOTNÉHO HDInsightu.  Každá verze Spark obsahuje sadu výchozích nastavení clusteru.  

Když vytvoříte nový cluster, můžete si vybrat z více verzí Spark. Chcete-li zobrazit úplný seznam, [hdinsight komponenty a verze](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).

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

Výše uvedený příklad přepíše několik výchozích hodnot pro pět konfiguračních parametrů Spark.  Tyto hodnoty jsou kompresní kodek, Apache Hadoop MapReduce rozdělit minimální velikost velikosti a parkety bloku velikosti. Oddíl Spar SQL a velikosti otevřeného souboru také výchozí hodnoty.  Tyto změny konfigurace jsou vybrány, protože přidružená data a úlohy (v tomto příkladu genomická data) mají zvláštní charakteristiky. Tyto vlastnosti budou lépe pomocí těchto vlastních nastavení konfigurace.

---

## <a name="view-cluster-configuration-settings"></a>Zobrazit nastavení konfigurace clusteru

Před optimalizací výkonu v clusteru ověřte aktuální nastavení konfigurace clusteru HDInsight. Spusťte řídicí panel HDInsight z portálu Azure kliknutím na odkaz **Řídicí panel** v podokně clusteru Spark. Přihlaste se pomocí uživatelského jména a hesla správce clusteru.

Zobrazí se webové uživatelské prostředí Apache Ambari s řídicím panelem metrik využití prostředků klíčového clusteru.  Řídicí panel Ambari zobrazuje konfiguraci Apache Spark a další nainstalované služby. Řídicí panel obsahuje kartu **Historie konfigurace,** kde si prohlížíte informace o nainstalovaných službách, včetně Spark.

Chcete-li zobrazit hodnoty konfigurace pro Apache Spark, vyberte **Historie konfigurace**a pak vyberte **Spark2**.  Vyberte kartu **Configs** a `Spark` v `Spark2`seznamu služeb vyberte odkaz (nebo v závislosti na verzi).  Zobrazí se seznam konfiguračních hodnot pro váš cluster:

![Konfigurace jiskry](./media/apache-spark-settings/spark-configurations.png)

Chcete-li zobrazit a změnit jednotlivé hodnoty konfigurace Spark, vyberte v názvu libovolný odkaz s "jiskrou".  Konfigurace pro Spark zahrnují vlastní i pokročilé hodnoty konfigurace v těchto kategoriích:

* Vlastní výchozí hodnoty Spark2
* Vlastní vlastnosti metrik Spark2
* Pokročilé výchozí hodnoty Spark2
* Pokročilé Spark2-env
* Pokročilé přepsání spark2-hive-site

Pokud vytvoříte nevýchozí sadu hodnot konfigurace, historie aktualizací se zobrazí.  Tato historie konfigurace může být užitečné zjistit, která konfigurace, která není výchozí, má optimální výkon.

> [!NOTE]  
> Chcete-li zobrazit, ale nezměnit, společné nastavení konfigurace clusteru Spark, vyberte kartu **Prostředí** na rozhraní uživatelského rozhraní **sparku nejvyšší** úrovně.

## <a name="configuring-spark-executors"></a>Konfigurace vykonavatelů sparku

Následující diagram znázorňuje klíčové objekty Spark: program ovladače a přidružený kontext spark a správce clusteru a jeho *pracovní uzly n.*  Každý pracovní uzel obsahuje instanci úloh executor, mezipaměť a *n* úloh.

![Objekty clusteru](./media/apache-spark-settings/hdi-spark-architecture.png)

Úlohy Spark používají pracovní prostředky, zejména paměť, takže je běžné upravit hodnoty konfigurace Spark pro vykonavatele pracovních uzlů.

Tři klíčové parametry, které jsou často upraveny tak, `spark.executor.instances` `spark.executor.cores`aby `spark.executor.memory`vyladily konfigurace Spark pro zlepšení požadavků na aplikaci, jsou , a . Exekutor je proces spuštěný pro aplikaci Spark. Vykonavatel běží na pracovní uzel a je zodpovědný za úkoly pro aplikaci. Počet pracovních uzlů a velikost pracovního uzlu určuje počet vykonavatelů a velikosti vykonavatelů. Tyto hodnoty jsou `spark-defaults.conf` uloženy v hlavní uzly clusteru.  Tyto hodnoty můžete upravit v běžícím clusteru výběrem **vlastních výchozích hodnot jiskry** ve webovém uživatelském uživatelském panelu Ambari.  Po provedené mši se zobrazí výzva k **restartování** všech ohrožených služeb.

> [!NOTE]  
> Tyto tři konfigurační parametry lze nakonfigurovat na úrovni clusteru (pro všechny aplikace spuštěné v clusteru) a také zadat pro každou jednotlivou aplikaci.

Dalším zdrojem informací o prostředcích používaných vykonavatelé spark je spark application uI.  V **ui, prováděcí příkazy** zobrazuje souhrnné a podrobné zobrazení konfigurace a spotřebované prostředky.  Určete, zda chcete změnit hodnoty prováděcích modulů pro celý cluster nebo konkrétní sadu spuštění úloh.

![Jiskra Executors](./media/apache-spark-settings/apache-spark-executors.png)

Nebo můžete použít rozhraní API Ambari REST k programovému ověření nastavení konfigurace clusteru HDInsight a Spark.  Více informací je k dispozici na [Apache Ambari API odkaz na GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

V závislosti na konkrétní úloze Spark můžete zjistit, že jiná než výchozí konfigurace Sparku zajistí optimálnější provádění úloh Spark.  Proveďte testování srovnávacích testů s ukázkovými úlohami a ověřte všechny konfigurace clusteru, které nejsou výchozí.  Tady je několik běžných parametrů, jejichž úpravu můžete zvážit:

|Parametr |Popis|
|---|---|
|--num-executors|Nastaví počet vykonavatelů.|
|--executor-cores|Nastaví počet jader pro každého vykonavatele. Doporučujeme používat středně velké vykonavatele, protože jiné procesy také spotřebovávají část dostupné paměti.|
|--vykonavatel paměti|Řídí velikost paměti (velikost haldy) každého vykonavatele na [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)a budete muset ponechat nějakou paměť pro režii spuštění.|

Zde je příklad dvou pracovních uzlů s různými hodnotami konfigurace:

![Konfigurace dvou uzlů](./media/apache-spark-settings/executor-configuration.png)

V následujícím seznamu jsou uvedeny klíčové parametry paměti vykonavatele Spark.

|Parametr |Popis|
|---|---|
|spark.executor.memory|Definuje celkové množství paměti, které je k dispozici pro vykonavatele.|
|spark.storage.memoryFraction|(výchozí ~60%) definuje množství paměti, která je k dispozici pro ukládání trvalých RDD.|
|spark.shuffle.memoryFraction|(výchozí ~20%) definuje velikost paměti vyhrazené pro náhodné přehrávání.|
|spark.storage.unrollFraction a spark.storage.safetyFrakce|(celkem ~30% celkové paměti) - tyto hodnoty jsou používány interně Spark a neměly by být změněny.|

YARN řídí maximální součet paměti používané kontejnery na každém uzlu Spark. Následující diagram znázorňuje vztahy mezi objekty konfigurace YARN a objekty Spark na uzel.

![Správa paměti YARN Spark](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Změna parametrů pro aplikaci spuštěnou v notebooku Jupyter

Clustery Spark ve službě HDInsight ve výchozím nastavení obsahují několik součástí. Každá z těchto součástí obsahuje výchozí hodnoty konfigurace, které lze podle potřeby přepsat.

|Komponenta |Popis|
|---|---|
|Jádro jiskry|Spark Core, Spark SQL, Spark streamovaná api, GraphX a Apache Spark MLlib.|
|Anaconda|Správce balíčků pythonu.|
|[Apačská Livy](https://livy.incubator.apache.org/)|Rozhraní Apache Spark REST API, které se používá k odesílání vzdálených úloh do clusteru HDInsight Spark.|
|[Notebooky Jupyter](https://jupyter.org/) a [Apache Zeppelin](https://zeppelin.apache.org/)|Interaktivní ui založené na prohlížeči pro interakci s clusterem Spark.|
|Ovladač ODBC|Propojuje clustery Spark v HDInsightu s nástroji business intelligence (BI), jako jsou Microsoft Power BI a Tableau.|

U aplikací spuštěných v poznámkovém `%%configure` bloku Jupyter použijte příkaz k provádění změn konfigurace z poznámkového bloku samotného. Tyto změny konfigurace se použijí na úlohy Spark spuštěné z instance poznámkového bloku. Před spuštěním první buňky kódu proveďte tyto změny na začátku aplikace. Změněná konfigurace se použije pro relaci Livy, když se vytvoří.

> [!NOTE]  
> Chcete-li změnit konfiguraci v pozdější fázi `-f` aplikace, použijte parametr (force). Veškerý pokrok v aplikaci však bude ztracen.

Níže uvedený kód ukazuje, jak změnit konfiguraci pro aplikaci spuštěnou v poznámkovém bloku Jupyter.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Závěr

Monitorujte nastavení konfigurace jádra, abyste zajistili, že vaše úlohy Spark budou fungovat předvídatelným a výkonným způsobem. Tato nastavení pomáhají určit nejlepší konfiguraci clusteru Spark pro vaše konkrétní úlohy.  Budete také muset sledovat provádění dlouhotrvající a nebo prostředky náročné spuštění úloh spark.  Nejběžnější problémy se soustředí na tlak paměti z nesprávných konfigurací, jako jsou nesprávně velké vykonavatelé. Také dlouhodobé operace a úkoly, které vedou k kartézské operace.

## <a name="next-steps"></a>Další kroky

* [Apache Hadoop komponenty a verze k dispozici s HDInsight?](../hdinsight-component-versioning.md)
* [Správa prostředků pro cluster Apache Spark na HDInsightu](apache-spark-resource-manager.md)
* [Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími](../hdinsight-hadoop-provision-linux-clusters.md)
* [Konfigurace Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Běh Apache Spark na Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
