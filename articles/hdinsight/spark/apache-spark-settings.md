---
title: Konfigurace nastavení Sparku – Azure HDInsight
description: Jak zobrazit a nakonfigurovat nastavení Apache Spark pro cluster Azure HDInsight
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 148ba2ce256b11b623bf28cf7723dbb0cce60830
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929745"
---
# <a name="configure-apache-spark-settings"></a>Konfigurace nastavení Apache Sparku

Cluster An HDInsight Spark zahrnuje instalaci knihovny Apache Spark.  Každý cluster HDInsight obsahuje výchozí parametry konfigurace pro všechny nainstalované služby, včetně Sparku.  Klíčovým aspektem správy clusteru HDInsight Apache Hadoop je monitorování úloh, včetně úloh Sparku. Aby se spouštěly úlohy Sparku, při určování logické konfigurace clusteru zvažte konfiguraci fyzického clusteru.

Výchozí cluster HDInsight Apache Spark zahrnuje následující uzly: tři uzly Apache ZooKeeper, dva hlavní uzly a jeden nebo více pracovních uzlů:

![Architektura Spark HDInsight](./media/apache-spark-settings/spark-hdinsight-arch.png)

Počet virtuálních počítačů a velikostí virtuálních počítačů pro uzly v clusteru HDInsight může ovlivnit konfiguraci Sparku. Nevýchozí hodnoty konfigurace HDInsight často vyžadují hodnoty konfigurace Sparku jiné než výchozí. Při vytváření clusteru HDInsight Spark se zobrazí doporučené velikosti virtuálních počítačů pro každou součást. V současné době jsou [velikosti virtuálních počítačů se systémem Linux optimalizované pro paměť](../../virtual-machines/sizes-memory.md) pro Azure D12 v2 nebo vyšší.

## <a name="apache-spark-versions"></a>Verze Apache Spark

Pro svůj cluster použijte nejlepší verzi Sparku.  Služba HDInsight zahrnuje několik verzí Spark i HDInsight.  Každá verze Sparku obsahuje sadu výchozích nastavení clusteru.  

Když vytváříte nový cluster, existuje několik verzí Spark, ze kterých si můžete vybrat. Zobrazí úplný seznam  [komponent a verzí HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Výchozí verze Apache Spark ve službě HDInsight se může změnit bez předchozího upozornění. Pokud máte závislost verze, společnost Microsoft doporučuje zadat tuto konkrétní verzi při vytváření clusterů pomocí sady .NET SDK, Azure PowerShell a Azure Classic CLI.

Apache Spark má tři umístění konfigurace systému:

* Vlastnosti Sparku řídí většinu parametrů aplikace a lze je nastavit pomocí `SparkConf` objektu nebo pomocí vlastností systému Java.
* Proměnné prostředí lze použít k nastavení nastavení pro jednotlivé počítače, jako je například IP adresa, prostřednictvím `conf/spark-env.sh` skriptu na každém uzlu.
* Protokolování lze nakonfigurovat prostřednictvím `log4j.properties` .

Když vyberete konkrétní verzi Sparku, cluster obsahuje výchozí nastavení konfigurace.  Výchozí hodnoty konfigurace Sparku můžete změnit pomocí vlastního konfiguračního souboru Spark.  Příklad najdete níže.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

Výše uvedený příklad přepisuje několik výchozích hodnot pro pět parametrů konfigurace Spark.  Tyto hodnoty jsou Kompresní kodek, Apache Hadoop MapReduce rozdělená na minimální velikost a Parquet velikosti bloku. Také oddíly SQL Spar a velikosti souborů otevřou výchozí hodnoty.  Tyto změny konfigurace se volí, protože přidružená data a úlohy (v tomto příkladu data genomiky) mají určité charakteristiky. Tyto charakteristiky budou lépe používat tato vlastní nastavení konfigurace.

---

## <a name="view-cluster-configuration-settings"></a>Zobrazit nastavení konfigurace clusteru

Než začnete s optimalizací výkonu v clusteru, ověřte aktuální nastavení konfigurace clusteru HDInsight. Spusťte řídicí panel HDInsight z Azure Portal kliknutím na odkaz **řídicí panel** v podokně clusteru Spark. Přihlaste se pomocí uživatelského jména a hesla správce clusteru.

Zobrazí se webové uživatelské rozhraní Apache Ambari s řídicím panelem klíčových metrik využití prostředků clusteru.  Na řídicím panelu Ambari se zobrazí konfigurace Apache Spark a další instalované služby. Řídicí panel obsahuje kartu **Historie konfigurace** , kde můžete zobrazit informace o nainstalovaných službách, včetně Sparku.

Pokud chcete zobrazit konfigurační hodnoty pro Apache Spark, vyberte **Historie konfigurace** a pak vyberte **Spark2**.  Vyberte kartu **Konfigurace** a potom `Spark` `Spark2` v seznamu služba vyberte odkaz (nebo v závislosti na vaší verzi).  Zobrazí se seznam hodnot konfigurace pro váš cluster:

![Konfigurace Sparku](./media/apache-spark-settings/spark-configurations.png)

Pokud chcete zobrazit a změnit jednotlivé hodnoty konfigurace Sparku, vyberte v nadpisu všechny odkazy s názvem Spark.  Konfigurace pro Spark zahrnují vlastní i pokročilé konfigurační hodnoty v těchto kategoriích:

* Vlastní Spark2 – výchozí
* Vlastní Spark2 – metriky – vlastnosti
* Rozšířené Spark2 – výchozí
* Advanced Spark2-ENV
* Advanced spark2-podregistr-site-override

Pokud vytvoříte jinou než výchozí sadu hodnot konfigurace, zobrazí se vaše historie aktualizací.  Tato historie konfigurace může být užitečná k tomu, abyste viděli, která jiná než výchozí konfigurace má optimální výkon.

> [!NOTE]  
> Pokud chcete zobrazit, ale ne změnit, obecná nastavení konfigurace clusteru Spark, vyberte kartu **prostředí** v **uživatelském rozhraní úlohy Sparku** na nejvyšší úrovni.

## <a name="configuring-spark-executors"></a>Konfigurace prováděcích modulů Spark

Následující diagram znázorňuje klíčové objekty Spark: program ovladače a jeho přidružený kontext Spark a Správce clusteru a jeho uzly pracovního procesu *n* .  Každý pracovní uzel obsahuje instance úlohy prováděcího modulu, mezipaměti a *n* .

![Objekty clusteru](./media/apache-spark-settings/hdi-spark-architecture.png)

Úlohy Sparku používají pracovní prostředky, zejména paměť, takže je běžné upravovat hodnoty konfigurace Sparku pro vykonavatele pracovních uzlů.

Tři klíčové parametry, které se často upravují pro optimalizaci konfigurací Sparku pro zlepšení požadavků aplikací, jsou, `spark.executor.instances` `spark.executor.cores` a `spark.executor.memory` . Vykonavatel je proces, který se spustil pro aplikaci Spark. Prováděcí modul běží na pracovním uzlu a zodpovídá za úkoly aplikace. Počet uzlů pracovního procesu a velikost pracovního uzlu určuje počet prováděcích modulů a velikost prováděcích procesů. Tyto hodnoty jsou uloženy v `spark-defaults.conf` uzlech hlavní uzly clusteru.  Tyto hodnoty můžete v běžícím clusteru upravit výběrem možnosti **vlastní Spark-výchozí** ve webovém uživatelském rozhraní Ambari.  Po provedení změn vás uživatelské rozhraní vyzve k **restartování** všech ovlivněných služeb.

> [!NOTE]  
> Tyto tři konfigurační parametry lze nakonfigurovat na úrovni clusteru (pro všechny aplikace, které jsou spuštěny v clusteru) a také jsou určeny pro každou jednotlivou aplikaci.

Dalším zdrojem informací o prostředcích, které používají vykonavatelé Spark, je uživatelské rozhraní aplikace Spark.  V uživatelském rozhraní zobrazí  **vykonavatelé** zobrazení souhrnných a podrobných informací o konfiguraci a spotřebovaných prostředcích.  Určete, jestli se mají změnit hodnoty prováděcích modulů pro celý cluster, nebo konkrétní sada spuštění úloh.

![Prováděče Spark](./media/apache-spark-settings/apache-spark-executors.png)

Případně můžete pomocí REST API Ambari programově ověřit nastavení konfigurace clusteru HDInsight a Spark.  Další informace najdete v referenčních informacích k [rozhraní Apache Ambari API na GitHubu](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

V závislosti na konkrétní úloze Spark můžete zjistit, že jiná než výchozí konfigurace Sparku zajistí optimálnější provádění úloh Spark.  Proveďte testování srovnávacích testů s ukázkovými úlohami a ověřte všechny konfigurace clusteru, které nejsou výchozí.  Tady je několik běžných parametrů, jejichž úpravu můžete zvážit:

|Parametr |Popis|
|---|---|
|--NUM-prováděcí moduly|Nastaví počet prováděcích modulů.|
|--prováděcí modul – jádra|Nastaví počet jader pro každý prováděcí modul. Doporučujeme používat vykonavatele střední velikosti, protože jiné procesy využívají také určitou část dostupné paměti.|
|--prováděcí modul – paměť|Určuje velikost paměti (velikost haldy) každého prováděcího modulu na Apache Hadoop PŘÍZi a pro režijní náklady na výkon je potřeba opustit určitou paměť.|

Tady je příklad dvou pracovních uzlů s různými hodnotami konfigurace:

![Konfigurace dvou uzlů](./media/apache-spark-settings/executor-configuration.png)

V následujícím seznamu jsou uvedeny klíčové parametry paměti pro vykonavatele Spark.

|Parametr |Popis|
|---|---|
|spark.executor. Memory|Definuje celkovou velikost paměti, která je k dispozici pro prováděcí modul.|
|Spark. Storage. memoryFraction|(výchozí ~ 60%) definuje velikost paměti, která je k dispozici pro uložení trvalých RDD.|
|Spark. renáhodný. memoryFraction|(výchozí ~ 20%) definuje množství paměti rezervované pro přemístění.|
|Spark. Storage. unrollFraction a Spark. Storage. safetyFraction|(celková hodnota ~ 30% z celkové paměti) – tyto hodnoty se používají interně Spark a neměly by se měnit.|

PŘÍZe řídí maximální součet paměti využívané kontejnery na jednotlivých uzlech Spark. Následující diagram znázorňuje vztahy jednotlivých uzlů mezi objekty konfigurace PŘÍZe a objekty Spark.

![Správa paměti PŘÍZe Spark](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Změnit parametry aplikace spuštěné v Jupyter Notebook

Clustery Spark v HDInsight ve výchozím nastavení zahrnují několik součástí. Každá z těchto součástí zahrnuje výchozí hodnoty konfigurace, které můžete podle potřeby přepsat.

|Komponenta |Popis|
|---|---|
|Spark Core|Spark Core, SQL Spark, rozhraní API pro streamování Sparku, GraphX a Apache Spark MLlib.|
|Anaconda|Správce balíčků Pythonu.|
|Apache Livy|Apache Spark REST API, které slouží k odesílání vzdálených úloh do clusteru HDInsight Spark.|
|Poznámkové bloky Jupyter a Apache Zeppelin|Interaktivní uživatelské rozhraní založené na prohlížeči pro interakci s clusterem Spark.|
|Ovladač ODBC|Propojuje Clustery Spark v HDInsight s nástroji business intelligence (BI), jako je Microsoft Power BI a Tableau.|

Pro aplikace běžící v Jupyter Notebook použijte `%%configure` příkaz k provedení změn konfigurace v rámci samotného poznámkového bloku. Tyto změny konfigurace budou aplikovány na úlohy Spark spouštěné z vaší instance poznámkového bloku. Udělejte tyto změny na začátku aplikace před spuštěním první buňky kódu. Změněná konfigurace se použije pro relaci Livy při jejím vytvoření.

> [!NOTE]  
> Chcete-li změnit konfiguraci v pozdější fázi aplikace, použijte `-f` parametr (Force). Veškerý průběh aplikace však bude ztracen.

Následující kód ukazuje, jak změnit konfiguraci aplikace spuštěné v Jupyter Notebook.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Závěr

Monitorujte základní nastavení konfigurace, abyste zajistili, že vaše úlohy Spark běží předvídatelným a vykonávajícím způsobem. Tato nastavení vám pomůžou určit nejlepší konfiguraci clusteru Spark pro vaše konkrétní úlohy.  Také budete muset monitorovat provádění dlouhotrvajících úloh Sparku, které jsou náročné na prostředky a nebo pro ně.  Nejběžnějším centrem pro výzvy k zatížení paměti z nesprávných konfigurací, jako jsou například nesprávně používané prováděcí moduly. Také dlouhotrvající operace a úlohy, které vedou k kartézském operací.

## <a name="next-steps"></a>Další kroky

* [Apache Hadoop součásti a verze, které jsou k dispozici v HDInsight?](../hdinsight-component-versioning.md)
* [Správa prostředků pro cluster Apache Spark v HDInsight](apache-spark-resource-manager.md)
* [Konfigurace Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Spouštění Apache Spark v Apache Hadoop nitě](https://spark.apache.org/docs/latest/running-on-yarn.html)
