---
title: Konfigurace nastavení Spark – Azure HDInsight | Microsoft Docs
description: Postup konfigurace Spark pro cluster služby HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: maxluk
ms.openlocfilehash: db61cc81f51772aa98c034f1bfdf51777cfd68e7
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="configure-spark-settings"></a>Konfigurace nastavení Sparku

Cluster služby HDInsight Spark zahrnuje instalace knihovny Apache Spark.  Každý cluster HDInsight zahrnuje výchozí parametry konfigurace pro všechny její nainstalované služby, včetně Spark.  Klíčovým prvkem Správa clusteru HDInsight Hadoop je monitorování úlohy, včetně úloh Spark, ujistěte se, že úlohy běží předvídatelný způsobem. Chcete-li Spark nejlépe spouštění úloh, zvažte konfigurace fyzického clusteru při určování optimalizace logické konfigurace clusteru.

Výchozí cluster HDInsight Apache Spark zahrnuje následující uzly: tři uzly ZooKeeper, head dva uzly a jeden nebo více uzlů pracovního procesu:

![Architektura HDInsight Spark](./media/apache-spark-settings/spark-hdinsight-arch.png)

Počet virtuálních počítačů a velikosti virtuálních počítačů, pro uzly v clusteru HDInsight může ovlivnit také konfiguraci Spark. Jiné než výchozí hodnoty konfigurace HDInsight často vyžadují jiné než výchozí hodnoty konfigurace Spark. Při vytváření clusteru HDInsight Spark se zobrazují navrhované velikosti virtuálních počítačů pro všechny komponenty. Aktuálně [velikosti paměťově optimalizované virtuálních počítačů Linux](../../virtual-machines/linux/sizes-memory.md) pro Azure jsou D12 v2 nebo vyšší.

## <a name="spark-versions"></a>Spark verze

Použijte nejlepší verzi Spark pro cluster.  Služba HDInsight zahrnuje několik verzí Spark a HDInsight sám sebe.  Každá verze nástroje Spark obsahuje sadu výchozích nastavení clusteru.  

Když vytvoříte nový cluster, zde jsou aktuální verze Spark můžete vybrat ze:

![Spark verze](./media/apache-spark-settings/spark-version.png)

Spark 2.x můžete spustit mnohem lepší, než Spark 1.x. Spark 2.x má několik optimalizací výkonu, jako je například wolframu, zprostředkující dotaz optimalizace a další.  

> [!NOTE]
> Výchozí verze Apache Spark v HDInsight služby mohou změnit bez předchozího upozornění. Pokud máte závislost verze, společnost Microsoft doporučuje, zadejte tuto konkrétní verzi, při vytváření clusterů pomocí .NET SDK nebo Azure PowerShell a rozhraní příkazového řádku Azure.

Apache Spark má tři umístění konfigurace systému:

* Vlastnosti Spark řízení většinu parametrů aplikace a lze nastavit pomocí `SparkConf` objekt, nebo prostřednictvím vlastnosti systému Java.
* Proměnné prostředí slouží k nastavení podle počítače, jako je například IP adresa, prostřednictvím `conf/spark-env.sh` skript na každém uzlu.
* Protokolování můžete nakonfigurovat pomocí `log4j.properties`.

Když vyberete konkrétní verzi Spark, cluster obsahuje výchozí nastavení konfigurace.  Výchozí hodnoty konfigurace Spark můžete změnit pomocí vlastní konfigurační soubor Spark.  Příklad je uveden níže.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

Výše uvedený příklad přepíše několik výchozí hodnoty pro parametry konfigurace pět Spark.  Jedná se o kompresní kodek, Hadoop MapReduce rozdělí minimální velikosti a velikosti bloku parquet a také oddílu Spar SQL a otevřete soubor velikosti výchozí hodnoty.  Tyto změny konfigurace jsou zvolené, protože úlohy (v tomto příkladu Genomické dat) a přidružená data konkrétní vlastnosti, které provede lépe s tímto nastavením vlastní konfigurace.

---

## <a name="view-cluster-configuration-settings"></a>Zobrazení nastavení konfigurace clusteru

Před provedením optimalizace výkonu v clusteru, zkontrolujte aktuální nastavení konfigurace clusteru HDInsight. Řídicí panel HDInsight z portálu Azure spustit kliknutím **řídicí panel** odkaz v podokně clusteru Spark. Přihlaste se pomocí uživatelského jména a hesla správce clusteru.

Webové uživatelské rozhraní Ambari, zobrazí se s zobrazení řídicího panelu metrik využití prostředků klíče clusteru.  Ambari řídicího panelu ukazuje konfiguraci Apache Spark a dalším službám, které jste nainstalovali. Řídicí panel obsahuje **historie konfigurací** kartě, kde můžete zobrazit informace o konfiguraci pro všechny nainstalované služby, včetně Spark.

Pokud chcete zobrazit hodnoty konfigurace pro Apache Spark, vyberte **historie konfigurací**, pak vyberte **Spark2**.  Vyberte **konfigurací** a potom vyberte `Spark` (nebo `Spark2`, v závislosti na vaší verzi) odkaz v seznamu služeb.  Zobrazí seznam hodnot konfigurace pro váš cluster:

![Konfigurace Spark](./media/apache-spark-settings/spark-config.png)

Pokud chcete zobrazit a změnit hodnoty konfigurace jednotlivých Spark, vyberte všechny propojení se slovem "spark" v názvu odkazu.  Konfigurace pro Spark zahrnují obě hodnoty pokročilé a vlastní konfigurace v těchto kategoriích:

* Vlastní Spark2-výchozí hodnoty
* Vlastní Spark2-metriky – vlastnosti
* Pokročilé Spark2-výchozí hodnoty
* Pokročilé Spark2 env
* Pokročilé spark2-hive lokality – přepsat

Pokud vytvoříte sadu jiné než výchozí hodnoty konfigurace, potom můžete také zobrazit historii aktualizace vaší konfigurace.  Historie tato konfigurace může být užitečné jiné než výchozí konfiguraci, kterou má optimální výkon.

> [!NOTE]
> Zobrazit, ale nemění běžných nastavení konfigurace clusteru Spark, vyberte **prostředí** karta na nejvyšší úrovni **uživatelského rozhraní úlohy Spark** rozhraní.

## <a name="configuring-spark-executors"></a>Konfigurace vykonavatelů Spark

Následující diagram znázorňuje objekty klíče Spark: program ovladače a jeho přidružený kontext Spark a Správce clusteru a jeho *n* uzlů pracovního procesu.  Každý pracovní uzel zahrnuje vykonavatele, mezipaměť, a *n* úkolů instancí.

![Objekty clusteru](./media/apache-spark-settings/spark-arch.png)

Úlohy Spark používat prostředky pracovního procesu, zejména paměti, proto je běžné, chcete-li upravit hodnoty konfigurace Spark pro pracovního uzlu vykonavatelů.

Tři klíčové parametry, které jsou často upravena k optimalizaci Spark konfigurace ke zlepšení požadavky na aplikace jsou `spark.executor.instances`, `spark.executor.cores`, a `spark.executor.memory`. Vykonavatele je proces spuštění pro aplikaci Spark. Vykonavatele spustí v pracovním uzlu a je odpovědná za úlohy pro aplikaci. Pro každý cluster výchozí počet vykonavatelů a velikosti prováděcího modulu, je vypočtena na základě počet uzlů pracovního procesu a velikost uzlu pracovníka. Tyto jsou uložené v `spark-defaults.conf` o hlavních uzlech clusteru.  Tyto hodnoty v clusteru s podporou spuštěné můžete upravit tak, že vyberete **vlastní spark – výchozí** odkaz v webovému uživatelskému rozhraní Ambari.  Když provedete změny, se zobrazí výzva pomocí uživatelského rozhraní pro **restartujte** všechny ovlivněné služby.

> [!NOTE]
> Tyto tři konfigurační parametry můžete nakonfigurovat na úrovni clusteru (pro všechny aplikace, které běží na clusteru) a také zadané pro všechny aplikace.

Další zdroje informací o prostředcích používá vykonavatelů Spark je rozhraní aplikací Spark.  V uživatelském rozhraní Spark, vyberte **vykonavatelů** zobrazte Souhrn a podrobnosti zobrazení konfigurace a prostředky spotřebované vykonavatelů.  Tato zobrazení vám pomohou určit, jestli chcete-li změnit výchozí hodnoty pro Spark vykonavatelů pro celý cluster nebo konkrétní sada spuštění úlohy.

![Spark vykonavatelů](./media/apache-spark-settings/spark-executors.png)

Alternativně můžete použít Ambari REST API programově ověření konfiguračních nastavení clusteru HDInsight a Spark.  Další informace najdete na [Ambari API odkaz na Githubu](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

V závislosti na vaše úlohy Spark může určit, že nevýchozí Spark konfigurace poskytuje více optimalizované Spark spuštění úlohy.  Měli byste provést srovnávací test testování pomocí ukázkové úlohy k ověření konfigurace jakékoli jiné než výchozí clusteru.  Některé společné parametry, které můžete zvážit při úpravě jsou:

* `--num-executors` Nastaví počet vykonavatelů.
* `--executor-cores` Nastaví počet jader pro každý prováděcího modulu. Doporučujeme používat middle-sized vykonavatelů jako další procesy, využívat také některé část dostupné paměti.
* `--executor-memory` ovládací prvky velikost paměti (velikost haldy) každý vykonavatele na YARN a vy budete muset nechte některé paměť pro spuštění režii.

Tady je příklad dvou uzlů pracovního procesu s jinou konfiguraci hodnotami:

![Dvě konfigurace uzlu](./media/apache-spark-settings/executor-config.png)

V následujícím seznamu jsou klíče vykonavatele Spark paměti parametry.

* `spark.executor.memory` Definuje vykonavatele celkovou velikost dostupné paměti.
* `spark.storage.memoryFraction` (výchozí ~ 60 %) definuje množství paměti k dispozici pro ukládání trvalou RDDs.
* `spark.shuffle.memoryFraction` (výchozí ~ 20 %) definuje množství paměti, které jsou vyhrazené pro náhodné.
* `spark.storage.unrollFraction` a `spark.storage.safetyFraction` (součtem ~ 30 % celkové paměti) - tyto hodnoty se používá interně Spark a neměli byste ji měnit.

YARN řídí maximální součtem velikostí paměti používané kontejnery v každém uzlu Spark. Následující diagram znázorňuje podle uzlu vztahy mezi objekty konfigurace YARN a objekty Spark.

![Správa paměti YARN Spark](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Změňte parametry pro aplikace běžící v poznámkového bloku Jupyter

Clustery Spark v HDInsight obsahuje mnoho součástí ve výchozím nastavení. Každou z těchto součástí zahrnuje výchozí konfigurace hodnoty, které je možné přepsat podle potřeby.

* Spark Core - Spark Core, Spark SQL, Spark streamování rozhraní API, GraphX a MLlib
* Anaconda - python balíček správce
* Livy – rozhraní API REST Apache Spark používá vzdálené úlohy do clusteru HDInsight Spark
* Bloky Jupyter a Zeppelin - interaktivní webového uživatelského rozhraní pro interakci s váš cluster Spark
* Ovladač ODBC - připojí clustery Spark v HDInsight k nástroje business intelligence (BI), jako je například Microsoft Power BI a Tableau

Pro aplikace běžící v poznámkového bloku Jupyter, použijte `%%configure` příkazu konfigurace se změní z v samotné poznámkového bloku. Tyto změny konfigurace se použijí pro úlohy Spark, spusťte z vaší instance poznámkového bloku. Tyto změny měli na začátku aplikace před spuštěním vaše první buňky kódu. Změněné konfigurace se použijí na Livy relace, když se vytvoří.

> [!NOTE]
> Chcete-li změnit konfiguraci v pozdější fázi v aplikaci, použijte `-f` parametr (vynutit). Všechny průběh v aplikaci však budou ztraceny.

Následující kód ukazuje, jak změnit konfiguraci pro aplikace běžící v poznámkového bloku Jupyter.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Závěr

Existuje několik základní nastavení konfigurace, které potřebujete k monitorování a upravit zajistit, že běží vaše úlohy Spark předvídatelný a původce způsobem. Tato nastavení pomohou určit nejlepší konfiguraci clusteru Spark pro konkrétní zatížení.  Musíte taky monitorovat spuštěních úloh Spark dlouho běžící nebo využívání prostředků.  Nejběžnější problémy center kolem přetížení paměti z důvodu nesprávné konfigurace (zejména nesprávně proměnlivé velikosti vykonavatelů), dlouhotrvající operace a úlohy, které mít za následek kartézských operace.

## <a name="next-steps"></a>Další postup

* [Komponent systému Hadoop a verze, které jsou k dispozici v prostředí HDInsight?](../hdinsight-component-versioning.md)
* [Správa prostředků v clusteru Spark v HDInsight](apache-spark-resource-manager.md)
* [Nastavit clusterů v HDInsight Hadoop, Spark, Kafka a dalšími](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark konfigurace](https://spark.apache.org/docs/latest/configuration.html)
* [Spark systémem YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
