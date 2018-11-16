---
title: Konfigurace nastavení Sparku – Azure HDInsight
description: Postup konfigurace pro cluster Azure HDInsight Spark.
services: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: b31be534641f2777bcbfaaa33497d96b457db191
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684082"
---
# <a name="configure-spark-settings"></a>Konfigurace nastavení Sparku

Cluster Spark v HDInsight zahrnuje instalaci knihovně Apache Spark.  Každý cluster HDInsight zahrnuje výchozí hodnoty parametrů konfigurace pro všechny jeho nainstalovaných služeb, včetně Spark.  Klíčový aspekt správě clusteru HDInsight Hadoop je monitorování úloh, včetně Sparkových úloh, abyste měli jistotu, že úlohy, které jsou spuštěny předvídatelným způsobem. Chcete-li nejlépe spouštět úlohy Spark, zvažte konfigurace fyzického clusteru při zjišťování toho, jak optimalizovat logické konfigurace clusteru.

Výchozí cluster HDInsight Apache Spark zahrnuje následující uzly: tři uzly ZooKeeper, dva hlavní uzly a jeden nebo více uzlů pracovních procesů:

![Architektura HDInsight Spark](./media/apache-spark-settings/spark-hdinsight-arch.png)

Počet virtuálních počítačů a velikosti virtuálních počítačů, pro uzly v clusteru HDInsight může také ovlivnit konfiguraci Spark. Nestandardní HDInsight konfigurační hodnoty často vyžadují jiné než výchozí Spark konfigurační hodnoty. Když vytvoříte cluster HDInsight Spark, jsou uvedeny doporučené velikosti virtuálních počítačů pro všechny komponenty. Aktuálně [velikostí optimalizovaných pro paměť virtuálního počítače s Linuxem](../../virtual-machines/linux/sizes-memory.md) Azure jsou D12 v2 nebo vyšší.

## <a name="spark-versions"></a>Verze Sparku

Používejte doporučené verze Sparku pro váš cluster.  Služba HDInsight zahrnuje několik verzí Spark a HDInsight samotný.  Sada výchozích nastavení clusteru zahrnuje každá verze Sparku.  

Při vytváření nového clusteru existuje více verzí Spark lze vybírat. Pokud chcete zobrazit úplný seznam [HDInsight komponenty a verze](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]
> Výchozí verze Apache Spark ve službě HDInsight mohou změnit bez předchozího upozornění. Pokud máte verzi závislosti, společnost Microsoft doporučuje, zadejte tuto konkrétní verzi při vytváření clusterů s využitím sady .NET SDK, Azure Powershellu a rozhraní příkazového řádku Azure Classic.

Apache Spark má tři umístění konfigurace systému:

* Spark vlastnosti řídit většinu parametrů aplikace a můžete nastavit pomocí `SparkConf` objektu, nebo prostřednictvím vlastnosti systém v jazyce Java.
* Proměnné prostředí slouží k nastavení vázaná na počítač, jako je například IP adresu prostřednictvím `conf/spark-env.sh` skript na každém uzlu.
* Protokolování je možné nakonfigurovat pomocí `log4j.properties`.

Když vyberete konkrétní verzi Sparku, váš cluster obsahuje výchozí nastavení konfigurace.  Výchozí hodnoty konfigurace Spark můžete změnit pomocí konfiguračního souboru vlastní Spark.  Příklad je uveden níže.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

Výše uvedený příklad přepíše několik výchozích hodnot pro parametry konfigurace pět Spark.  Jedná se o kompresní kodek, Hadoop MapReduce rozdělí minimální velikost a parquet velikosti bloku a také oddíl Spar SQL a otevřít soubor velikosti výchozí hodnoty.  Protože je možné zvolit těchto změn konfigurace úlohy (v tomto příkladu genomová datová) a přidružená data mají konkrétní vlastnosti, které provede lépe pomocí těchto nastavení vlastní konfigurace.

---

## <a name="view-cluster-configuration-settings"></a>Zobrazení nastavení konfigurace clusteru

Ověřte aktuální nastavení konfigurace clusteru HDInsight před provedením optimalizace výkonu v clusteru. Spuštění řídicího panelu HDInsight z portálu Azure portal kliknutím **řídicí panel** odkaz v podokně clusteru Spark. Přihlaste se pomocí uživatelského jména a hesla správce clusteru.

Webové uživatelské rozhraní Ambari, zobrazí se zobrazení řídicího panelu klíčových clusteru metriky využití prostředků.  Ambari řídicí panel ukazuje konfiguraci Apache Spark a dalším službám, které jste nainstalovali. Řídicí panel obsahuje **Config historie** kartu, kde můžete zobrazit informace o konfiguraci pro všechny nainstalované služby, včetně Spark.

Pokud chcete zobrazit hodnoty konfigurace pro Apache Spark, vyberte **Config historie**a pak vyberte **Spark2**.  Vyberte **Configs** kartu a potom vyberte `Spark` (nebo `Spark2`, v závislosti na vaší verzi) odkaz v seznamu služeb.  Zobrazí seznam hodnot konfigurace pro váš cluster:

![Konfigurace Spark](./media/apache-spark-settings/spark-config.png)

Pokud chcete zobrazit a změnit hodnoty konfigurace jednotlivých Spark, vyberte libovolný propojení se slovem "spark" v názvu odkazu.  Konfigurace pro Spark zahrnují obě hodnoty vlastní a pokročilá konfigurace v těchto kategoriích:

* Vlastní Spark2 – výchozí hodnoty
* Vlastní Spark2-metrics-properties
* Pokročilé Spark2 – výchozí hodnoty
* Pokročilé Spark2 env
* Pokročilé spark2 hive lokality – přepsat

Pokud vytvoříte sadu jiné než výchozí konfigurační hodnoty, můžete také zobrazit historii aktualizací konfigurace.  Historie této konfigurace může být užitečné jiné než výchozí konfiguraci, kterou má optimální výkon.

> [!NOTE]
> Chcete-li zobrazit, ale nikoli změnit, běžných nastavení konfigurace clusteru Spark, vyberte **prostředí** karty na nejvyšší úrovni **uživatelského rozhraní úlohy Spark** rozhraní.

## <a name="configuring-spark-executors"></a>Konfigurace prováděcí moduly Spark

Následující diagram ukazuje klíčové objekty Spark: program ovladače a jeho přidruženého kontextu Sparku a Správce clusteru a jeho *n* pracovních uzlů.  Každý pracovní uzel obsahuje prováděcího modulu, mezipaměti, a *n* instance úlohy.

![Clusterové objekty](./media/apache-spark-settings/spark-arch.png)

Sparkových úloh pomocí prostředky pracovního procesu, zejména paměti tak, aby byl běžné upravit Spark konfigurační hodnoty pro pracovníka uzel prováděcí moduly.

Jsou tři parametry klíče, které jsou často k vyladění konfigurace Spark ke zlepšení požadavky aplikace upraveny `spark.executor.instances`, `spark.executor.cores`, a `spark.executor.memory`. Vykonavatele je proces spuštěn z aplikace Spark. Vykonavatele běží na pracovní uzel a zodpovídá za úlohy pro aplikaci. Pro každý cluster výchozí číslo prováděcí moduly a velikosti prováděcí modul se počítají na základě počtu pracovních uzlů a velikost uzlu pracovního procesu. Tyto jsou uložené v `spark-defaults.conf` o hlavních uzlech clusteru.  Tyto hodnoty spuštěný cluster můžete upravit tak, že vyberete **vlastní spark – výchozí hodnoty** odkaz do webového uživatelského rozhraní Ambari.  Když provedete změny, se zobrazí výzva k uživatelským rozhraním pro **restartovat** všechny ovlivněné služby.

> [!NOTE]
> Tyto tři konfigurační parametry, můžete nakonfigurovat na úrovni clusteru (pro všechny aplikace, které běží na clusteru) a také zadat pro každou jednotlivou aplikaci.

Další zdroje informací o prostředcích používá prováděcí moduly Spark se uživatelské rozhraní aplikace Spark.  V uživatelském rozhraní Spark, vyberte **prováděcí moduly** karet zobrazit souhrn a podrobnosti zobrazení konfigurace a prostředky spotřebované prováděcí moduly.  Tato zobrazení vám pomohou určit, zda chcete-li změnit výchozí hodnoty pro Spark vykonavatele pro celý cluster, nebo konkrétní sadu se prováděné úlohy.

![Prováděcí moduly Spark](./media/apache-spark-settings/spark-executors.png)

Alternativně můžete použít rozhraní Ambari REST API programově ověřit nastavení konfigurace clusteru HDInsight a Spark.  Další informace jsou k dispozici na [reference k rozhraní API Ambari na Githubu](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

V závislosti na vašich úloh Spark můžete určit, že nevýchozí konfigurace Spark poskytuje více optimalizované Spark se prováděné úlohy.  Měli byste provést srovnávací test testování pomocí ukázkových úloh k ověření konfigurace clusteru jiné než výchozí.  Zde jsou některé společné parametry, které může být vhodné nastavení:

* `--num-executors` Nastaví počtu prováděcích procesů.
* `--executor-cores` Nastaví počet jader pro každý prováděcího modulu. Doporučujeme používat middle-sized moduly provádění, jako jiné procesy také využívat některé části dostupné paměti.
* `--executor-memory` ovládací prvky velikosti paměti (velikost haldy) každý prováděcí modul na YARN a vy budete muset opustit část paměti pro režijní náklady na spuštění.

Tady je příklad dvou uzlů pracovního procesu s jinou konfiguraci hodnoty:

![Dvě konfigurace uzlu](./media/apache-spark-settings/executor-config.png)

Následující seznam obsahuje parametry paměti klíče prováděcí modul Sparku.

* `spark.executor.memory` Určuje celkovou velikost paměti, které jsou k dispozici pro vykonavatele.
* `spark.storage.memoryFraction` (výchozí přibližně 60 %) definuje velikost paměti dostupné pro ukládání trvalých Rdd.
* `spark.shuffle.memoryFraction` (výchozí přibližně 20 %) definuje velikost paměti, které jsou vyhrazené pro náhodné.
* `spark.storage.unrollFraction` a `spark.storage.safetyFraction` (celkem tedy ~ 30 % z celkové paměti) – tyto hodnoty se používají interně ve Sparku a neměl měnit.

YARN řídí maximální součtem velikostí paměti využívaných kontejnery na každém uzlu Spark. Následující diagram znázorňuje jeden uzel vztahy mezi objekty konfigurace YARN a Spark.

![Správa paměti YARN Spark](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Změna parametrů pro aplikace běžící v Poznámkový blok Jupyter

Clustery Spark v HDInsight zahrnují komponent ve výchozím nastavení. Každá z těchto komponent zahrnuje výchozí konfigurační hodnoty, které mohou být přepsána nastaveními podle potřeby.

* Spark Core – Spark Core, Spark SQL, streamování rozhraní API, GraphX a MLlib ve Sparku
* Anaconda – Správce balíčků pythonu
* Livy – Apache Spark REST API, používá k odeslání vzdálené úlohy ke clusteru HDInsight Spark
* Bloky Jupyter a Zeppelin – interaktivní webové rozhraní pro interakci s vaším clusterem Sparku
* Ovladač ODBC – připojení k nástroji business intelligence (BI), jako je například Microsoft Power BI a tableau. představují clustery Spark v HDInsight

Pro aplikace běžící v poznámkovém bloku Jupyter, použijte `%%configure` příkazu konfigurace se změní z v rámci samotného poznámkového bloku. Tyto změny konfigurace se použijí k Sparkových úloh spustit z vaší instance poznámkového bloku. Měli byste zajistit takové změny na začátku aplikace před spuštěním vaší první buňky kódu. Změny konfigurace se použijí pro relaci Livy když se vytvoří.

> [!NOTE]
> Chcete-li změnit konfiguraci v pozdější fázi v aplikaci, použijte `-f` parametr (force). Všechny probíhající aplikace však budou ztraceny.

Následující kód ukazuje, jak změnit konfiguraci pro aplikace běžící v poznámkovém bloku Jupyter.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Závěr

Existuje několik základních nastavení konfigurace, které potřebujete k monitorování a přizpůsobit je tak Ujistěte se, že spouštění Sparkových úloh předvídatelný a výkonným způsobem. Tato nastavení pomůžou určit nejvhodnější konfiguraci clusteru Spark pro vaše konkrétní úlohy.  Musíte také sleduje provádění dlouhotrvajících nebo využívání prostředků se prováděné úlohy Spark.  Nejběžnější center problémy kolem přetížení paměti z důvodu nesprávné konfigurace (zejména nesprávně velikostí prováděcí moduly), dlouhotrvající operace a úkoly, které za následek Kartézském operace.

## <a name="next-steps"></a>Další postup

* [Komponenty a verze Hadoopu HDInsight přináší?](../hdinsight-component-versioning.md)
* [Správa prostředků v clusteru Spark v HDInsight](apache-spark-resource-manager.md)
* [Nastavení clusterů v HDInsight pomocí Hadoop, Spark, Kafka a další](../hdinsight-hadoop-provision-linux-clusters.md)
* [Konfigurace Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Spouštění Spark na technologii YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
