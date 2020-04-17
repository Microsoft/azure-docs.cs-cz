---
title: Apache Ambari pro optimalizaci konfigurací clusteru - Azure HDInsight
description: Webové uživatelské rozhraní Apache Ambari slouží ke konfiguraci a optimalizaci clusterů Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: c88882175ff256300dee486e680a9b63e9a65c99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532494"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Použití Apache Ambari k optimalizaci konfigurací clusterů HDInsight

HDInsight poskytuje [clustery Apache Hadoop](./hadoop/apache-hadoop-introduction.md) pro rozsáhlé aplikace pro zpracování dat. Správa, monitorování a optimalizace těchto komplexních clusterů s více uzlmi může být náročná. [Apache Ambari](https://ambari.apache.org/) je webové rozhraní pro správu a monitorování HDInsight Linux clusterů.  Pro clustery Windows použijte [rozhraní Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

Úvod k používání webového uživatelského rozhraní Ambari najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Přihlaste se k `https://CLUSTERNAME.azurehdidnsight.net` Ambari pomocí přihlašovacích údajů clusteru. Na úvodní obrazovce se zobrazí přehledový řídicí panel.

![Zobrazen uživatelský řídicí panel Apache Ambari](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Webové uživatelské uživatelské nastavení Ambari se používá ke správě hostitelů, služeb, výstrah, konfigurací a zobrazení. Ambari nelze použít k vytvoření clusteru HDInsight nebo k upgradu služeb. Také nelze spravovat zásobníky a verze, vyřazení z provozu nebo opětovné uvedení do provozu hostitelů nebo přidat služby do clusteru.

## <a name="manage-your-clusters-configuration"></a>Správa konfigurace clusteru

Nastavení konfigurace pomáhají vyladit konkrétní službu. Chcete-li změnit nastavení konfigurace služby, vyberte službu z postranního panelu **Služby** (vlevo). Potom přejděte na kartu **Configs** na stránce podrobností služby.

![Postranní panel služby Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Změna velikosti haldy jazyka Java NameNode

Velikost haldy Jazyka Java namenode závisí na mnoha faktorech, jako je například zatížení clusteru. Také počet souborů a počet bloků. Výchozí velikost 1 GB funguje dobře s většinou clusterů, i když některé úlohy mohou vyžadovat více nebo méně paměti.

Změna velikosti haldy jazyka Java NameNode:

1. Z postranního panelu Služby vyberte **HDFS** a přejděte na kartu **Konfigurace.**

    ![Konfigurace Apache Ambari HDFS](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Najít nastavení **NameNode Java haldy velikosti**. Můžete také použít textové pole **filtru** k zadání a vyhledání konkrétního nastavení. Vedle názvu nastavení vyberte ikonu **pera.**

    ![Apache Ambari NameNode Java velikost haldy](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Do textového pole zadejte novou hodnotu a stisknutím **klávesy Enter** uložte změnu.

    ![Ambari Upravit NameNode Java haldy velikost1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. Velikost haldy Jazyka Java namenode se změní na 1 GB z 2 GB.

    ![Upravená velikost haldy Java NameNode2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Uložte změny kliknutím na zelené tlačítko **Uložit** v horní části konfigurační obrazovky.

    !['Apache Ambari uložit konfigurace'](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Optimalizace Apache Hive

Následující části popisují možnosti konfigurace pro optimalizaci celkového výkonu Apache Hive.

1. Chcete-li upravit parametry konfigurace Hive, vyberte **příkaz Hive** z postranního panelu Služby.
1. Přejděte na kartu **Konfigurace.**

### <a name="set-the-hive-execution-engine"></a>Nastavení prováděcího modulu Hive

Hive poskytuje dva prováděcí motory: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) a [Apache TEZ](https://tez.apache.org/). Tez je rychlejší než MapReduce. HdInsight Linux clustery mají Tez jako výchozí prováděcí modul. Změna prováděcího modulu:

1. Na kartě **Configs** hive zadejte **modul spuštění** do pole filtru.

    ![Apache Ambari Vyhledávač](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. Výchozí hodnota **vlastnosti Optimalizace** je **Tez**.

    ![Optimalizace - Motor Apache Tez](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Naladění mapovačů

Hadoop se pokusí rozdělit (*mapa*) jeden soubor do více souborů a zpracovávat výsledné soubory paralelně. Počet mapovačů závisí na počtu rozdělení. Následující dva parametry konfigurace řídí počet rozdělení pro modul spuštění Tez:

* `tez.grouping.min-size`: Dolní limit velikosti seskupeného rozdělení s výchozí hodnotou 16 MB (16 777 216 bajtů).
* `tez.grouping.max-size`: Horní limit velikosti seskupeného rozdělení s výchozí hodnotou 1 GB (1 073 741 824 bajtů).

Jako vodítko výkonu nižší oba tyto parametry ke zlepšení latence, zvýšení pro větší propustnost.

Chcete-li například nastavit čtyři úlohy mapovače pro velikost dat 128 MB, nastavíte oba parametry na 32 MB (33 554 432 bajtů).

1. Chcete-li upravit parametry limitu, přejděte na kartu **Configs** služby Tez. Rozbalte panel **Obecné** a `tez.grouping.max-size` `tez.grouping.min-size` vyhledejte parametry a.

1. Nastavte oba parametry na **33 554 432** bajtů (32 MB).

    ![Apache Ambari Tez skupinovací velikosti](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Tyto změny ovlivní všechny úlohy Tez na celém serveru. Chcete-li dosáhnout optimálního výsledku, zvolte příslušné hodnoty parametrů.

### <a name="tune-reducers"></a>Naladění reduktorů

[Apache ORC](https://orc.apache.org/) a [Snappy](https://google.github.io/snappy/) oba nabízejí vysoký výkon. Hive však může mít příliš málo reduktorů ve výchozím nastavení, což způsobuje kritické body.

Řekněme například, že máte vstupní data velikost 50 GB. Tato data ve formátu ORC s kompresí Snappy jsou 1 GB. Hive odhaduje počet reduktorů potřebných jako: (počet bajtů `hive.exec.reducers.bytes.per.reducer`vstup mappers / ).

S výchozím nastavením je v tomto příkladu čtyři reduktory.

Parametr `hive.exec.reducers.bytes.per.reducer` určuje počet bajtů zpracovaných na reduktor. Výchozí hodnota je 64 MB. Ladění této hodnoty dolů zvyšuje paralelismus a může zlepšit výkon. Příliš nízké ladění může také způsobit příliš mnoho reduktorů, což může mít nepříznivý vliv na výkon. Tento parametr je založen na vašich konkrétních požadavcích na data, nastavení komprese a dalších faktorech prostředí.

1. Chcete-li parametr upravit, přejděte na kartu **Konfigurace** úlu a na stránce Nastavení vyhledejte parametr **Data na redukční rzuž.**

    ![Apache Ambari Data za reduktor](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Vyberte **Upravit,** chcete-li hodnotu upravit na 128 MB (134 217 728 bajtů), a stisknutím **klávesy Enter** ji uložte.

    ![Ambari Data za reduktor - upraveno](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Vzhledem k velikosti vstupu 1 024 MB s 128 MB dat na reduktor existuje osm reduktorů (1024/128).

1. Nesprávná hodnota parametru **Data na redukční** může mít za následek velký počet reduktorů, což nepříznivě ovlivňuje výkon dotazu. Chcete-li omezit maximální počet `hive.exec.reducers.max` reduktorů, nastavte na příslušnou hodnotu. Výchozí hodnota je 1009.

### <a name="enable-parallel-execution"></a>Povolit paralelní provádění

Dotaz Hive je spuštěn v jedné nebo více fázích. Pokud nezávislé fáze lze spustit paralelně, to zvýší výkon dotazu.

1. Chcete-li povolit paralelní provádění **Config** dotazů, přejděte `hive.exec.parallel` na kartu Konfigurace hive a vyhledejte vlastnost. Výchozí hodnota je False. Změňte hodnotu na hodnotu true a stisknutím **klávesy Enter** hodnotu uložte.

1. Chcete-li omezit počet úloh, které `hive.exec.parallel.thread.number` mají být spuštěny paralelně, upravte vlastnost. Výchozí hodnota je 8.

    ![Paralelní displej Apache Hive exec](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Povolit vektorizaci

Hive zpracovává data řádek po řádku. Vectorization přesměruje Hive ke zpracování dat v blocích 1 024 řádků, nikoli po jednom řádku. Vektorizace je použitelná pouze pro formát souboru ORC.

1. Chcete-li povolit spuštění vektorizovaného dotazu, přejděte na `hive.vectorized.execution.enabled` kartu **Configs** hive a vyhledejte parametr. Výchozí hodnota platí pro Hive 0.13.0 nebo novější.

1. Chcete-li povolit vektorizované spuštění pro snížit `hive.vectorized.execution.reduce.enabled` stranu dotazu, nastavte parametr na true. Výchozí hodnota je False.

    ![Apache Hive vektorizované provedení](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Povolit optimalizaci založenou na nákladech (CBO)

Ve výchozím nastavení Hive následuje sadu pravidel najít jeden optimální plán spuštění dotazu. Optimalizace založená na nákladech (CBO) vyhodnocuje více plánů pro spuštění dotazu. A přiřadí náklady na každý plán, pak určuje nejlevnější plán pro spuštění dotazu.

Chcete-li povolit cbo, přejděte do**nastavení** **konfigurace** >  **hive** > a najděte **možnost Povolit optimalizátor založený na nákladech**a poté přepněte přepínací tlačítko **na Zapnuto**.

![Optimalizátor hdinsight založený na nákladech](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

Následující další parametry konfigurace zvyšují výkon dotazu Hive, když je povolen cbo:

* `hive.compute.query.using.stats`

    Když je nastavena na hodnotu true, Hive používá `count(*)`statistiky uložené v metastore odpovědět na jednoduché dotazy, jako je .

    ![Výpočetní dotaz Apache Hive pomocí statistik](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Statistiky sloupců jsou vytvořeny, pokud je povolen cbo. Hive používá statistiky sloupců, které jsou uloženy v metastore, k optimalizaci dotazů. Načítání statistiky sloupců pro každý sloupec trvá déle, když je vysoký počet sloupců. Pokud je nastavena na false, toto nastavení zakáže načítání statistiky sloupců z metastore.

    ![Statistiky Apache Hive stanovují statistiky sloupců](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Základní statistiky oddílů, jako je počet řádků, velikost dat a velikost souboru, jsou uloženy v metastore. Pokud je nastavena na hodnotu true, statistiky oddílů jsou načteny z metastore. Pokud false, velikost souboru je načten ze systému souborů. A počet řádků je načten ze schématu řádku.

    ![Statistiky úlu stanovují statistiky oddílů](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Povolit zprostředkující kompresi

Mapování úloh vytvořit zprostředkující soubory, které jsou používány reduktoru úkoly. Zprostředkující komprese zmenší mezilehlou velikost souboru.

Úlohy Hadoop jsou obvykle i / o úzká místa. Komprese dat může urychlit vstupně-va a celkový přenos sítě.

Dostupné typy komprese jsou:

| Formát | Nástroj | Algoritmus | Přípona souboru | Splittable? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | Deflaci | `.gz` | Ne |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Ano |
| LZO | `Lzop` | LZO | `.lzo` | Ano, pokud je indexováno |
| Elegantní | – | Elegantní | Elegantní | Ne |

Obecně platí, že s kompresní metodou splittable je důležité, jinak bude vytvořeno několik mapovačů. Pokud vstupní data je `bzip2` text, je nejlepší volbou. Pro formát ORC je Snappy nejrychlejší možností komprese.

1. Chcete-li povolit zprostředkující **Configs** kompresi, přejděte `hive.exec.compress.intermediate` na kartu Konfigurace úlu a nastavte parametr na hodnotu true. Výchozí hodnota je False.

    !['Hive exec komprese meziprodukt'](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Chcete-li komprimovat zprostředkující soubory, zvolte kompresní kodek s nižšími náklady na procesor, a to i v případě, že kodek nemá vysoký kompresní výstup.

1. Chcete-li nastavit zprostředkující kompresní `hive-site.xml` `mapred-site.xml` kodek, přidejte vlastní vlastnost `mapred.map.output.compression.codec` do souboru nebo.

1. Přidání vlastního nastavení:

    a. Přejděte na stránku **Hive** > **Configs** > **Advanced** > Custom**hive - site**.

    b. V dolní části podokna Vlastní podregistr-web vyberte **Přidat vlastnost...**

    c. V okně Přidat vlastnost `mapred.map.output.compression.codec` zadejte `org.apache.hadoop.io.compress.SnappyCodec` jako klíč a jako hodnotu.

    d. Vyberte **Přidat**.

    !['Apache Hive vlastní vlastnost přidat'](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Toto nastavení zkomprimuje zprostředkující soubor pomocí komprese Snappy. Jakmile je vlastnost přidána, zobrazí se v podokně Vlastní podregistr webu.

    > [!NOTE]  
    > Tento postup upraví `$HADOOP_HOME/conf/hive-site.xml` soubor.

### <a name="compress-final-output"></a>Komprese konečného výstupu

Konečný výstup Hive lze také komprimovat.

1. Chcete-li komprimovat konečný výstup Hive, přejděte na kartu `hive.exec.compress.output` **Configs** hive a nastavte parametr na hodnotu true. Výchozí hodnota je False.

1. Chcete-li zvolit výstupní kompresní kodek, přidejte `mapred.output.compression.codec` vlastní vlastnost do podokna Vlastní podregistr-web, jak je popsáno v kroku 3 předchozí části.

    ![Apache Hive vlastní vlastnost add2](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Povolit spekulativní provádění

Spekulativní provádění spustí určitý počet duplicitních úloh pro detekci a zamítnutí seznamu pomalu běžící nástroj sledování úloh. Při zlepšování celkového provádění úloh optimalizací výsledků jednotlivých úkolů.

Spekulativní provádění by neměla být zapnuta pro dlouhotrvající MapReduce úkoly s velkým množstvím vstupů.

* Chcete-li povolit spekulativní provádění, přejděte na kartu **Configs** hive a nastavte parametr na hodnotu `hive.mapred.reduce.tasks.speculative.execution` true. Výchozí hodnota je False.

    !['Hive mapred snížit úkoly spekulativní provedení'](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Vyladění dynamických oddílů

Hive umožňuje vytváření dynamických oddílů při vkládání záznamů do tabulky bez předdefinování každého oddílu. Tato schopnost je výkonnou funkcí. I když to může mít za následek vytvoření velkého počtu oddílů. A velký počet souborů pro každý oddíl.

1. Pro Hive dělat dynamické oddíly, hodnota parametru `hive.exec.dynamic.partition` by měla být true (výchozí).

1. Změňte režim dynamického oddílu na *přísný*. V přísném režimu musí být alespoň jeden oddíl statický. Toto nastavení zabraňuje dotazy bez filtru oddílu v klauzuli WHERE, to znamená, že *přísné* zabraňuje dotazy, které prohledává všechny oddíly. Přejděte na kartu **Konfigurace** úlu a `hive.exec.dynamic.partition.mode` nastavte na **striktní**. Výchozí hodnota je **nestriktní**.

1. Chcete-li omezit počet dynamických oddílů, `hive.exec.max.dynamic.partitions` které mají být vytvořeny, upravte parametr. Výchozí hodnota je 5000.

1. Chcete-li omezit celkový počet dynamických oddílů na uzel, upravte . `hive.exec.max.dynamic.partitions.pernode` Výchozí hodnota je 2000.

### <a name="enable-local-mode"></a>Povolit místní režim

Místní režim umožňuje Hive dělat všechny úkoly úlohy na jednom počítači. Nebo někdy v jednom procesu. Toto nastavení zlepšuje výkon dotazu, pokud jsou vstupní data malá. A režie spuštění úlohy pro dotazy spotřebovává významné procento celkové spuštění dotazu.

Chcete-li povolit `hive.exec.mode.local.auto` místní režim, přidejte parametr do panelu Vlastní podregistr-lokalita, jak je vysvětleno v kroku 3 části [Povolit zprostředkující kompresi.](#enable-intermediate-compression)

![Apache Hive exec režim místní auto](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Nastavit jednu funkci MapReduce MultiGROUP BY

Pokud je tato vlastnost nastavena na hodnotu true, dotaz MultiGROUP BY s běžnými klíči podle skupiny vygeneruje jednu úlohu MapReduce.  

Chcete-li toto `hive.multigroupby.singlereducer` chování povolit, přidejte parametr do podokna Vlastní podregistr webu, jak je vysvětleno v kroku 3 části [Povolit zprostředkující kompresi.](#enable-intermediate-compression)

![Sada hive single MapReduce MultiGROUP BY](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Další optimalizace hive

Následující části popisují další optimalizace související s Hive, které můžete nastavit.

#### <a name="join-optimizations"></a>Optimalizace připojení

Výchozí typ spojení v Úlu je *náhodné spojení*. V Hive speciální mapovače číst vstup a vyzařují spojit klíč/hodnotu dvojice do zprostředkující soubor. Hadoop třídí a slučuje tyto páry ve fázi náhodného přehrávání. Tato fáze shuffle je drahá. Výběr správného spojení na základě vašich dat může výrazně zlepšit výkon.

| Typ spojení | Kdy | Postup | Nastavení úlu | Komentáře |
| --- | --- | --- | --- | --- |
| Náhodné připojení | <ul><li>Výchozí volba</li><li>Vždy funguje</li></ul> | <ul><li>Čtení z části jedné z tabulek</li><li>Kbelíky a řazení na klíč Připojit</li><li>Odešle jeden kbelík na každé snížení</li><li>Spojení se provádí na straně Snížit</li></ul> | Není potřeba žádné významné nastavení Hive | Funguje pokaždé |
| Mapové spojení | <ul><li>Jedna tabulka se vejde do paměti</li></ul> | <ul><li>Přečte malou tabulku do tabulky hash paměti</li><li>Datové proudy přes část velkého souboru</li><li>Připojí každý záznam z tabulky hash.</li><li>Spojení jsou pouze mapovač</li></ul> | `hive.auto.confvert.join=true` | Rychlé, ale omezené |
| Kontejner sloučení řazení | Pokud jsou obě tabulky: <ul><li>Seřazeno stejně</li><li>Bucketed stejné</li><li>Spojení v seřazeném/kývnutém sloupci</li></ul> | Každý proces: <ul><li>Čte kbelík z každé tabulky</li><li>Zpracuje řádek s nejnižší hodnotou.</li></ul> | `hive.auto.convert.sortmerge.join=true` | Efektivní |

#### <a name="execution-engine-optimizations"></a>Optimalizace prováděcích motorů

Další doporučení pro optimalizaci modulu provádění Hive:

| Nastavení | Doporučené | Výchozí hodnota HDInsight |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | Pravda = bezpečnější, pomalejší; false = rychlejší | false (nepravda) |
| `tez.am.resource.memory.mb` | 4 GB horní mez pro většinu | Automaticky naladěné |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Apache Pig optimalizace

[Vlastnosti Apache Pig](https://pig.apache.org/) lze upravit z webového uživatelského rozhraní Ambari pro ladění dotazů Pig. Úprava vlastností prasete z Ambari přímo upravuje `/etc/pig/2.4.2.0-258.0/pig.properties` vlastnosti pig v souboru.

1. Chcete-li upravit vlastnosti pig, přejděte na kartu Pig **Configs** a rozbalte podokno **Upřesnit vlastnosti prasete.**

1. Vyhledejte, odkomentujte a změňte hodnotu vlastnosti, kterou chcete upravit.

1. Vyberte **Uložit** na pravé horní straně okna, chcete-li uložit novou hodnotu. Některé vlastnosti mohou vyžadovat restartování služby.

    ![Pokročilé vlastnosti prasat Apache](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Všechna nastavení na úrovni relace přepíší hodnoty vlastností v souboru. `pig.properties`

### <a name="tune-execution-engine"></a>Naladit prováděcí modul

Dva prováděcí motory jsou k dispozici pro spuštění pig skripty: MapReduce a Tez. Tez je optimalizovaný motor a je mnohem rychlejší než MapReduce.

1. Chcete-li upravit modul provádění, najděte vlastnost `exectype`v podokně Rozšířené **vlastnosti prasete** .

1. Výchozí hodnota je **MapReduce**. Změňte jej na **Tez**.

### <a name="enable-local-mode"></a>Povolit místní režim

Podobně jako Hive se místní režim používá k urychlení úloh s relativně menším množstvím dat.

1. Chcete-li povolit `pig.auto.local.enabled` místní režim, nastavte hodnotu **true**. Výchozí hodnota je False.

1. Úlohy s menší velikostí `pig.auto.local.input.maxbytes` vstupních dat, než je hodnota vlastnosti, jsou považovány za malé úlohy. Výchozí hodnota je 1 GB.

### <a name="copy-user-jar-cache"></a>Kopírovat mezipaměť uživatelských jar

Prase zkopíruje soubory JAR vyžadované udfs do distribuované mezipaměti, aby byly k dispozici pro uzly úloh. Tyhle sklenice se často nemění. Pokud je `pig.user.cache.enabled` povoleno, nastavení umožňuje, aby byly nádoby umístěny do mezipaměti a znovu je použít pro úlohy spouštěné stejným uživatelem. Toto nastavení má za následek menší zvýšení výkonu úlohy.

1. Chcete-li `pig.user.cache.enabled` povolit, nastavte na hodnotu true. Výchozí hodnotou je hodnota false.

1. Chcete-li nastavit základní cestu sklenic uložených v mezipaměti, nastavte `pig.user.cache.location` na základní cestu. Výchozí formát je `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>Optimalizace výkonu pomocí nastavení paměti

Následující nastavení paměti může pomoci optimalizovat výkon pig skriptu.

* `pig.cachedbag.memusage`: Množství paměti dané sáčku. Taška je sbírka n-tic. Řazená kolekce členů je uspořádaná sada polí a pole je část dat. Pokud jsou data v sáčku mimo danou paměť, jsou vylita na disk. Výchozí hodnota je 0,2, což představuje 20 procent dostupné paměti. Tato paměť je sdílena ve všech pytlích v aplikaci.

* `pig.spill.size.threshold`: Tašky větší než tento práh velikosti rozlití (v bajtů) jsou rozlité na disk. Výchozí hodnota je 5 MB.

### <a name="compress-temporary-files"></a>Komprese dočasných souborů

Pig generuje dočasné soubory během provádění úlohy. Komprese dočasných souborů má za následek zvýšení výkonu při čtení nebo zápisu souborů na disk. Následující nastavení lze použít ke kompresi dočasných souborů.

* `pig.tmpfilecompression`: Pokud je true, povolí dočasnou kompresi souborů. Výchozí hodnota je False.

* `pig.tmpfilecompression.codec`: Kompresní kodek, který se má použít pro kompresi dočasných souborů. Doporučené kompresní kodeky jsou [LZO](https://www.oberhumer.com/opensource/lzo/) a Snappy pro nižší využití procesoru.

### <a name="enable-split-combining"></a>Povolit rozdělení kombinování

Pokud je tato možnost povolena, malé soubory jsou kombinovány pro méně mapových úloh. Toto nastavení zvyšuje efektivitu úloh s mnoha malými soubory. Chcete-li `pig.noSplitCombination` povolit, nastavte na hodnotu true. Výchozí hodnota je False.

### <a name="tune-mappers"></a>Naladění mapovačů

Počet mapovačů je řízen úpravou `pig.maxCombinedSplitSize`vlastnosti . Tato vlastnost určuje velikost dat, která mají být zpracována jedním úkolem mapy. Výchozí hodnota je výchozí velikost bloku souborového systému. Zvýšení této hodnoty má za následek nižší počet mapovač úkoly.

### <a name="tune-reducers"></a>Naladění reduktorů

Počet reduktorů se vypočítá `pig.exec.reducers.bytes.per.reducer`na základě parametru . Parametr určuje počet bajtů zpracovaných na reduktor, ve výchozím nastavení 1 GB. Chcete-li omezit maximální počet reduktorů, nastavte `pig.exec.reducers.max` vlastnost, ve výchozím nastavení 999.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Apache HBase optimalizace s ambari webové uživatelského ui

[Konfigurace Apache HBase](https://hbase.apache.org/) je upravena z karty **HBase Configs.** Následující části popisují některá důležitá nastavení konfigurace, která ovlivňují výkon HBase.

### <a name="set-hbase_heapsize"></a>Nastavit HBASE_HEAPSIZE

Velikost haldy HBase určuje maximální množství haldy, která má být použita v megabajtech podle *oblastí* a *hlavních* serverů. Výchozí hodnota je 1 000 MB. Tato hodnota by měla být naladěna pro zatížení clusteru.

1. Chcete-li ji upravit, přejděte do podokna **Rozšířené hbase-env** na `HBASE_HEAPSIZE` kartě **Konfigurace** HBase a najděte nastavení.

1. Změňte výchozí hodnotu na 5 000 MB.

    !['Apache Ambari HBase paměti haldy'](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Optimalizace úloh s vysokým zatížením

Následující konfigurace jsou důležité pro zlepšení výkonu úlohy náročné na čtení.

#### <a name="block-cache-size"></a>Blokovat velikost mezipaměti

Bloková mezipaměť je mezipaměť pro čtení. Jeho velikost je `hfile.block.cache.size` řízena parametrem. Výchozí hodnota je 0,4, což je 40 procent celkové paměti serveru oblasti. Čím větší je velikost mezipaměti bloku, tím rychlejší bude náhodné čtení.

1. Chcete-li tento parametr upravit, přejděte na kartu **Nastavení** na kartě **Konfigurace** HBase a vyhledejte **% serveru RegionServer přidělených do vyrovnávacích pamětí pro čtení**.

    ![Velikost mezipaměti bloku paměti Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Chcete-li hodnotu změnit, vyberte ikonu **Upravit.**

#### <a name="memstore-size"></a>Velikost memstore

Všechny úpravy jsou uloženy ve vyrovnávací paměti, nazývané *Memstore*. Tato vyrovnávací paměť zvyšuje celkové množství dat, která lze zapsat na disk v jedné operaci. Také urychluje přístup k nedávným úpravám. Velikost Memstore je definována následujícími dvěma parametry:

* `hbase.regionserver.global.memstore.UpperLimit`: Definuje maximální procento serveru oblasti, které memstore v kombinaci lze použít.

* `hbase.regionserver.global.memstore.LowerLimit`: Definuje minimální procento serveru oblasti, které memstore kombinované použít.

Chcete-li optimalizovat pro náhodné čtení, můžete snížit memstore horní a dolní hranice.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Počet řádků načtených při skenování z disku

Nastavení `hbase.client.scanner.caching` definuje počet řádků přečtených z `next` disku, když je metoda volána na skeneru.  Výchozí hodnota je 100. Čím vyšší číslo, tím méně vzdálených volání z klienta na server oblasti, výsledkem je rychlejší prohledávání. Toto nastavení však také zvýší tlak paměti na klienta.

![Apache HBase počet načtených řádků](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Nenastavujte hodnotu tak, aby doba mezi vyvoláním další metody na skeneru byla větší než časový rozsah skeneru. Doba trvání časového času skeneru je definována vlastností. `hbase.regionserver.lease.period`

### <a name="optimize-write-heavy-workloads"></a>Optimalizace úloh náročných na zápis

Následující konfigurace jsou důležité pro zlepšení výkonu úlohy náročné na zápis.

#### <a name="maximum-region-file-size"></a>Maximální velikost souboru oblasti

HBase ukládá data ve formátu interního souboru s názvem *HFile*. Vlastnost `hbase.hregion.max.filesize` definuje velikost jednoho HFile pro oblast.  Oblast je rozdělena do dvou oblastí, pokud je součet všech HFiles v oblasti větší než toto nastavení.

!['Apache HBase HRegion max velikost souboru'](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Čím větší je velikost souboru oblasti, tím menší je počet rozdělení. Můžete zvětšit velikost souboru k určení hodnoty, která má za následek maximální výkon zápisu.

#### <a name="avoid-update-blocking"></a>Vyhněte se blokování aktualizací

* Vlastnost `hbase.hregion.memstore.flush.size` definuje velikost, při které je Memstore vyprázdněn na disk. Výchozí velikost je 128 MB.

* Multiplikátor bloku oblasti HBase je definován . `hbase.hregion.memstore.block.multiplier` Výchozí hodnota je 4. Maximální povolená hodnota je 8.

* HBase blokuje aktualizace, pokud je`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`Memstore ( ) bajtů.

    S výchozí hodnoty vyprázdnění velikosti a blok multiplikátor, aktualizace jsou blokovány, když Memstore je 128 * 4 = 512 MB ve velikosti. Chcete-li snížit počet blokování aktualizací, zvyšte hodnotu . `hbase.hregion.memstore.block.multiplier`

![Multiplikátor bloku oblasti Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Definovat velikost memstore

Velikost memstore je `hbase.regionserver.global.memstore.UpperLimit` definována parametry a. `hbase.regionserver.global.memstore.LowerLimit` Nastavení těchto hodnot, které se navzájem rovnají, snižuje pauzy během zápisů (také způsobuje častější vyprázdnění) a vede ke zvýšení výkonu zápisu.

### <a name="set-memstore-local-allocation-buffer"></a>Nastavit místní alokační vyrovnávací paměť Memstore

Využití místní alokační vyrovnávací paměti `hbase.hregion.memstore.mslab.enabled`Memstore je určeno vlastností . Pokud je povoleno (true), toto nastavení zabraňuje fragmentaci haldy během operace těžké zápisu. Výchozí hodnotou je hodnota true.

![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
