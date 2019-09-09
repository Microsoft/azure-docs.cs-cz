---
title: Optimalizace konfigurací clusterů pomocí Apache Ambari – Azure HDInsight
description: Ke konfiguraci a optimalizaci clusterů Azure HDInsight použijte webové uživatelské rozhraní Apache Ambari.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 57e5eef8563bca0e27024d6ff5a46b983273df07
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810658"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Optimalizace konfigurací clusteru HDInsight pomocí Apache Ambari

HDInsight poskytuje [Apache Hadoop](https://hadoop.apache.org/) clusterů pro aplikace pro zpracování velkých objemů dat. Správa, monitorování a optimalizace těchto komplexních clusterů s více uzly může být náročné. [Apache Ambari](https://ambari.apache.org/) je webové rozhraní pro správu a monitorování clusterů HDInsight Linux.  V případě clusterů Windows použijte [REST API Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

Úvod k používání webového uživatelského rozhraní Ambari najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md) .

Přihlaste se k `https://CLUSTERNAME.azurehdidnsight.net` Ambari s přihlašovacími údaji clusteru. Úvodní obrazovka zobrazuje řídicí panel přehled.

![Řídicí panel Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Webové uživatelské rozhraní Ambari lze použít ke správě hostitelů, služeb, výstrah, konfigurací a zobrazení. Ambari se nedá použít k vytvoření clusteru HDInsight, upgradu služeb, správě zásobníků a verzí, vyřazení hostitelů nebo přeřazení z provozu nebo k přidání služeb do clusteru.

## <a name="manage-your-clusters-configuration"></a>Správa konfigurace clusteru

Nastavení konfigurace pomůžou ladit konkrétní službu. Chcete-li upravit nastavení konfigurace služby, vyberte službu na postranním panelu **služby** (vlevo) a pak přejděte na kartu **Konfigurace** na stránce s podrobnostmi o službě.

![Boční panel služeb](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Úprava velikosti haldy Java NameNode

Velikost haldy Java NameNode závisí na mnoha faktorech, jako je zatížení clusteru, počet souborů a počet bloků. Výchozí velikost 1 GB funguje ve většině clusterů, i když některé úlohy můžou vyžadovat více nebo méně paměti. 

Úprava velikosti haldy NameNode Java:

1. Z bočního panelu služeb vyberte **HDFS** a přejděte na kartu **Konfigurace** .

    ![Konfigurace HDFS](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

1. Najděte nastavení **Velikost haldy Java NameNode**. K zadání a vyhledání konkrétního nastavení můžete použít také textové pole **Filtr** . Vyberte ikonu **pera** vedle názvu nastavení.

    ![Velikost haldy Java NameNode](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

1. Do textového pole zadejte novou hodnotu a stisknutím klávesy **ENTER** změny uložte.

    ![Upravit size1 haldy NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

1. Velikost haldy Java NameNode se změní na 1 GB ze 2 GB.

    ![Upravená size2 haldy NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Uložte změny kliknutím na zelené tlačítko **Uložit** v horní části obrazovky konfigurace.

    ![Uložit změny](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="apache-hive-optimization"></a>Optimalizace Apache Hive

Následující části popisují možnosti konfigurace pro optimalizaci celkového výkonu Apache Hive.

1. Pokud chcete změnit parametry konfigurace podregistru, vyberte **podregistr** na postranním panelu služby.
1. Přejděte na kartu **Konfigurace** .

### <a name="set-the-hive-execution-engine"></a>Nastavení spouštěcího modulu pro podregistr

Podregistr poskytuje dva spouštěcí moduly: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) a [Apache tez](https://tez.apache.org/). Tez je rychlejší než MapReduce. Clustery HDInsight Linux mají tez jako výchozí spouštěcí modul. Změna spouštěcího modulu:

1. Na kartě **Konfigurace** podregistru zadejte **spouštěcí modul** do pole Filtr.

    ![Spouštěcí modul hledání](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

1. Výchozí hodnota vlastnosti **optimalizace** je **tez**.

    ![Optimalizace – tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Ladit mapovače

Hadoop se pokusí rozdělit (*mapovat*) jeden soubor na více souborů a paralelní zpracování výsledných souborů. Počet mapovačů závisí na počtu rozdělení. Následující dva parametry konfigurace řídí počet rozdělení pro spouštěcí modul tez:

* `tez.grouping.min-size`: Dolní mez velikosti seskupeného rozdělení s výchozí hodnotou 16 MB (16 777 216 bajtů).
* `tez.grouping.max-size`: Horní limit velikosti seskupeného rozdělení s výchozí hodnotou 1 GB (1 073 741 824 bajtů).

Jako pravidlo výkonu je potřeba snížit počet obou těchto parametrů, aby se zvýšila latence, zvýšení propustnosti.

Pokud třeba chcete nastavit čtyři úlohy mapovače pro velikost dat 128 MB, nastavili byste oba parametry na 32 MB každého (33 554 432 bajtů).

1. Chcete-li změnit parametry limitu, přejděte na kartu **Konfigurace** služby TEZ. Rozbalte panel **Obecné** a vyhledejte `tez.grouping.max-size` parametry a `tez.grouping.min-size` .

1. Nastavte oba parametry na **33 554 432** bajtů (32 MB).

    ![Velikosti seskupení tez](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Tyto změny mají vliv na všechny úlohy tez napříč serverem. Chcete-li získat optimální výsledek, vyberte příslušné hodnoty parametrů.

### <a name="tune-reducers"></a>Vyladění reduktorů

[Apache ORC](https://orc.apache.org/) a [přichycení](https://google.github.io/snappy/) nabízí vysoký výkon. Podregistr ale ve výchozím nastavení může mít příliš málo reduktorů, což způsobuje problémová místa.

Řekněme například, že máte velikost vstupních dat 50 GB. Tato data ve formátu ORC s kompresí s přichycením jsou 1 GB. Podregistr odhaduje počet reduktorů potřebných jako: (počet bajtů vstupu na mapovače/ `hive.exec.reducers.bytes.per.reducer`).

Ve výchozím nastavení je tento příklad 4 reduktorů.

`hive.exec.reducers.bytes.per.reducer` Parametr určuje počet zpracovaných bajtů na zmenšení. Výchozí hodnota je 64 MB. Ladění této hodnoty dolů zvyšuje paralelismus a může zvýšit výkon. Příliš nízké ladění může také způsobit příliš mnoho reduktorů, což potenciálně negativně ovlivní výkon. Tento parametr vychází z vašich konkrétních požadavků na data, nastavení komprese a dalších faktorů prostředí.

1. Chcete-li změnit parametr, přejděte na kartu **Konfigurace** podregistru a na stránce nastavení vyhledejte parametr **data na omezení** .

    ![Data na zmenšení](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
1. Vyberte **Upravit** pro úpravu hodnoty na 128 MB (134 217 728 bajtů) a pak stiskněte **ENTER** pro uložení.

    ![Data na redukci – upraveno](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Při zadání velikosti 1 024 MB s 128 MB dat na redukci je 8 reduktorů (1024/128).

1. Nesprávná hodnota pro parametr **data na snížení** hodnoty může mít za následek velký počet reduktorů, což negativně ovlivní výkon dotazů. Pro omezení maximálního počtu reduktorů nastavte `hive.exec.reducers.max` na odpovídající hodnotu. Výchozí hodnota je 1009.

### <a name="enable-parallel-execution"></a>Povolit paralelní provádění

Dotaz na podregistr se spustí v jedné nebo několika fázích. Pokud je možné nezávislé fáze spustit paralelně, což zvýší výkon dotazů.

1.  Chcete-li povolit paralelní provádění dotazů, přejděte na kartu **Konfigurace** podregistru a vyhledejte `hive.exec.parallel` vlastnost. Výchozí hodnota je false. Změňte hodnotu na true a potom stisknutím klávesy **ENTER** hodnotu uložte.
 
1.  Chcete-li omezit počet úloh, které mají být spuštěny paralelně, upravte `hive.exec.parallel.thread.number` vlastnost. Výchozí hodnota je 8.

    ![Podregistr exec Parallel](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Povolit vektorování

Podregistr zpracovává řádek data řádku. Rozvektorování směruje podregistr za účelem zpracování dat v blocích 1 024 řádků, nikoli v jednom řádku. Vektory lze použít pouze pro formát souboru ORC.

1. Chcete-li povolit vektorové provádění dotazů, přejděte na kartu **Konfigurace** podregistru a vyhledejte `hive.vectorized.execution.enabled` parametr. Výchozí hodnota je true pro podregistr 0.13.0 nebo novější.
 
1. Chcete-li povolit `hive.vectorized.execution.reduce.enabled` parametrizované spouštění pro možnost zmenšení dotazu, nastavte parametr na hodnotu true. Výchozí hodnota je false.

    ![Vykonání vektorového podregistru](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Povolit optimalizaci na základě nákladů (CBO)

Ve výchozím nastavení používá podregistr sadu pravidel pro vyhledání jednoho optimálního plánu spuštění dotazu. Optimalizace na základě nákladů (CBO) vyhodnocuje více plánů pro spuštění dotazu a přiřazuje každému plánu náklady. pak určí plán nejlevnější pro spuštění dotazu.

Pokud chcete povolit CBO, přejděte na kartu **Konfigurace** podregistru a vyhledejte `parameter hive.cbo.enable`a pak přepněte přepínací tlačítko na **zapnuto**.

![CBO konfigurace](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Následující dodatečné parametry konfigurace zvyšují výkon dotazů na podregistr, pokud je povolená možnost CBO:

* `hive.compute.query.using.stats`

    Při nastavení na hodnotu true používá podregistr ve svých metastore statistiku k zodpovězení jednoduchých dotazů `count(*)`, jako je.

    ![Statistiky CBO](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Statistiky sloupce se vytvoří, když je povolený CBO. Pro optimalizaci dotazů používá podregistr statistiku sloupce, které jsou uložené v metastore. Načtení statistiky sloupců pro každý sloupec trvá déle, pokud je počet sloupců vysoký. Když se nastaví na false, toto nastavení zakáže načítání statistik sloupce z metastore.

    ![Statistiky podregistru sady statistik](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Základní statistické údaje o oddílech, jako je počet řádků, velikost dat a velikost souboru, jsou uloženy v metastore. Když je nastavená hodnota true, Statistika oddílu se načte z metastore. V případě hodnoty false je velikost souboru načtena ze systému souborů a počet řádků je načten ze schématu řádku.

    ![Statistiky podregistru – Statistika oddílů sady](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Povolit mezilehlé komprese

Úkoly mapování vytvářejí mezilehlé soubory, které jsou používány úlohami pro snížení. Mezilehlé Komprese zmenší velikost mezilehlého souboru.

Úlohy Hadoop jsou obvykle kritické pro vstupně-výstupní operace. Komprimace dat může zrychlit vstupně-výstupní operace a celkový přenos v síti.

K dispozici jsou tyto typy komprese:

| Formát | Tool | Algoritmus | Přípona souboru | Rozdělitelné? |
| -- | -- | -- | -- | -- |
| GZIP | GZIP | DEFLATE | .gz | Ne |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Ano |
| LZO | Lzop | LZO | .lzo | Ano, pokud je indexovaný |
| Snappy | Není k dispozici | Snappy | Snappy | Ne |

Jako obecné pravidlo je důležité mít rozdělenou část kompresní metody, jinak se vytvoří velmi málo mapovačů. Pokud jsou vstupní data text, `bzip2` je to nejlepší možnost. V případě formátu ORC je přichycení nejrychlejší možnost komprese.

1. Chcete-li povolit mezilehlé komprese, přejděte na kartu **Konfigurace** podregistru a nastavte `hive.exec.compress.intermediate` parametr na hodnotu true. Výchozí hodnota je false.

    ![Komprimace komprimace v podregistru exec](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Chcete-li zkomprimovat mezilehlé soubory, vyberte Kompresní kodek s nižšími náklady na procesor, a to i v případě, že kodek nemá vysoký kompresní výstup.

1. Chcete-li nastavit pomocný kodek komprese, přidejte `mapred.map.output.compression.codec` `hive-site.xml` do souboru nebo `mapred-site.xml` vlastní vlastnost.

1. Přidání vlastního nastavení:

    a. Přejděte na kartu **Konfigurace** podregistru a vyberte kartu **Upřesnit** .

    b. Na kartě **Upřesnit** vyhledejte a rozbalte podokno **vlastní podregistr-web** .

    c. V dolní části podokna vlastní podregistr-site klikněte na odkaz **Přidat vlastnost** .

    d. V okně Přidat vlastnost zadejte `mapred.map.output.compression.codec` jako klíč a `org.apache.hadoop.io.compress.SnappyCodec` jako hodnotu.

    e. Klikněte na **Přidat**.

    ![Vlastní vlastnost podregistru](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Tím se zkomprimuje mezilehlé soubory pomocí přichycení. Po přidání této vlastnosti se tato vlastnost zobrazí v podokně vlastní podregistr-Web.

    > [!NOTE]  
    > Tento postup upraví `$HADOOP_HOME/conf/hive-site.xml` soubor.

### <a name="compress-final-output"></a>Komprimovat konečný výstup

Konečný výstup podregistru je také možné zkomprimovat.

1. Chcete-li zkomprimovat konečný výstup podregistru, přejděte na kartu **Konfigurace** podregistru a nastavte `hive.exec.compress.output` parametr na hodnotu true. Výchozí hodnota je false.

1. Chcete-li zvolit výstupní Kompresní kodek, přidejte `mapred.output.compression.codec` vlastní vlastnost do podokna vlastní web podregistr, jak je popsáno v kroku 3 v předchozí části.

    ![Vlastní vlastnost podregistru](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Povolit spekulativní provádění

Spekulativní spuštění spouští určitý počet duplicitních úloh, aby bylo možné detekovat a zajišťovat pomalý a zakázaný Nástroj pro sledování úloh a zároveň zlepšuje celkové provádění úloh optimalizací jednotlivých výsledků úkolů.

Spekulativní provádění by nemělo být zapnuté pro dlouhotrvající MapReduce úlohy s velkým množstvím vstupu.

* Chcete-li povolit spekulativní provádění, přejděte na kartu **Konfigurace** podregistru a nastavte `hive.mapred.reduce.tasks.speculative.execution` parametr na hodnotu true. Výchozí hodnota je false.

    ![Mapred podregistru snižuje spekulativní provádění úloh](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Ladit dynamické oddíly

Podregistr umožňuje vytvářet dynamické oddíly při vkládání záznamů do tabulky, aniž by bylo nutné předdefinovat každý oddíl a každý oddíl. Jedná se o výkonnou funkci, i když to může způsobit vytvoření velkého počtu oddílů a velkého počtu souborů pro každý oddíl.

1. Pro dynamické oddíly v `hive.exec.dynamic.partition` podregistru by měla být hodnota parametru true (výchozí).

1. Změňte režim dynamického oddílu na *stricted*. V přísném režimu musí být alespoň jeden oddíl statický. To zabraňuje dotazům bez filtru oddílů v klauzuli WHERE, což znamená, že metoda *Strict* brání dotazům, které kontrolují všechny oddíly. Přejděte na kartu **Konfigurace** podregistru a nastavte na hodnotu `hive.exec.dynamic.partition.mode` **Strict**. Výchozí hodnota je **nestriktní**.
 
1. Chcete-li omezit počet dynamických oddílů, které mají být vytvořeny, `hive.exec.max.dynamic.partitions` upravte parametr. Výchozí hodnota je 5000.
 
1. Chcete-li omezit celkový počet dynamických oddílů na uzel, upravte `hive.exec.max.dynamic.partitions.pernode`. Výchozí hodnota je 2000.

### <a name="enable-local-mode"></a>Povolit místní režim

Místní režim umožňuje, aby podregistr prováděl všechny úlohy úlohy na jednom počítači, nebo někdy v jednom procesu. To zlepšuje výkon dotazů, pokud jsou vstupní data malá a režie spouštění úloh pro dotazy spotřebovává významné procento celkového spuštění dotazu.

Chcete-li povolit místní režim, `hive.exec.mode.local.auto` přidejte parametr do panelu vlastní podregistr-site, jak je vysvětleno v kroku 3 oddílu [Povolení mezilehlé komprese](#enable-intermediate-compression) .

![Místní režim automatického režimu exec pro podregistr](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Nastavit jeden MapReduce pro více skupin podle

Pokud je tato vlastnost nastavená na hodnotu true, vytvoří se ve více skupinách dotaz s běžnými klíči Group by jedna úloha MapReduce.  

Chcete-li toto chování povolit, `hive.multigroupby.singlereducer` přidejte parametr do podokna vlastní podregistr-site, jak je vysvětleno v kroku 3 oddílu [Povolení mezilehlé komprese](#enable-intermediate-compression) .

![Jeden MapReduce pro více skupin v podmnožině](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Další optimalizace pro podregistr

Následující části popisují další optimalizace týkající se podregistru, které můžete nastavit.

#### <a name="join-optimizations"></a>Sloučit optimalizace

Výchozím typem spojení v podregistru je *náhodné spojení*. V podregistru speciální mapovače přečtou vstup a vygeneruje dvojici klíč/hodnota JOIN k mezilehlému souboru. Hadoop seřadí a sloučí tyto páry v náhodně připravené fázi. Tato fáze náhodného zpracování je náročná. Výběr správného spojení na základě vašich dat může významně zlepšit výkon.

| Typ připojení | Kdy | Použití | Nastavení podregistru | Komentáře |
| -- | -- | -- | -- | -- |
| Náhodně připojit | <ul><li>Výchozí volba</li><li>Vždy funguje</li></ul> | <ul><li>Čtení z části jedné z tabulek</li><li>Intervaly a řazení podle klávesy JOIN</li><li>Každé omezení pošle jednu sadu.</li><li>Spojení se provádí na straně snížení</li></ul> | Není potřeba žádné významné nastavení podregistru | Funguje kdykoli |
| Připojit k mapě | <ul><li>Jedna tabulka se může vejít do paměti.</li></ul> | <ul><li>Přečte malou tabulku do tabulky hash paměti.</li><li>Streamování prostřednictvím části velkého souboru</li><li>Spojí každý záznam z zatřiďovací tabulky.</li><li>Spojení jsou pouze mapovačem.</li></ul> | `hive.auto.confvert.join=true` | Velmi rychlé, ale omezené |
| Seřadit slučovací interval | Pokud jsou obě tabulky: <ul><li>Řazeno stejným</li><li>Je rozdělené na stejné</li><li>Spojení s seřazeným/rozdaným sloupcem</li></ul> | Každý proces: <ul><li>Přečte z každé tabulky kontejner.</li><li>Zpracuje řádek s nejnižší hodnotou.</li></ul> | `hive.auto.convert.sortmerge.join=true` | Velmi efektivní |

#### <a name="execution-engine-optimizations"></a>Optimalizace prováděcího modulu

Další doporučení pro optimalizaci spouštěcího modulu podregistru:

| Nastavení | Doporučené | Výchozí nastavení HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | True = bezpečnější, pomalejší; false = rychlejší | false |
| `tez.am.resource.memory.mb` | horní mez velikosti 4 GB pro většinu | Automaticky laděné |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Optimalizace vepřového sádla Apache

Vlastnosti pro [Apache prasete](https://pig.apache.org/) se dají upravit z webového uživatelského rozhraní Ambari pro optimalizaci dotazů na prasaty. Úprava vlastností prasete z Ambari přímo upravuje vlastnosti prasete v `/etc/pig/2.4.2.0-258.0/pig.properties` souboru.

1. Chcete-li změnit vlastnosti prasete, přejděte na kartu **Konfigurace** prasete a potom rozbalte podokno **Upřesnit prase – vlastnosti** .

1. Vyhledejte, zrušte komentář a změňte hodnotu vlastnosti, kterou chcete upravit.

1. V pravém horním rohu okna vyberte **Uložit** a uložte novou hodnotu. Některé vlastnosti mohou vyžadovat restartování služby.

    ![Pokročilý prasečí – vlastnosti](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]  
> Všechna nastavení na úrovni relace přepíšou hodnoty vlastností v `pig.properties` souboru.

### <a name="tune-execution-engine"></a>Modul pro spouštění ladění

Ke spouštění skriptů prasete jsou k dispozici dva spouštěcí moduly: MapReduce a TEZ. Tez je optimalizovaný modul a je mnohem rychlejší než MapReduce.

1. Chcete-li upravit spouštěcí modul, vyhledejte `exectype`v podokně **Upřesnit prase – vlastnosti** .

1. Výchozí hodnota je **MapReduce**. Změňte ji na **tez**.


### <a name="enable-local-mode"></a>Povolit místní režim

Podobně jako u podregistru se používá místní režim k urychlení úloh s relativně menšími objemy dat.

1. Pokud chcete povolit místní režim, nastavte `pig.auto.local.enabled` na **true**. Výchozí hodnota je false.

1. Úlohy, jejichž velikost vstupních dat je menší než `pig.auto.local.input.maxbytes` hodnota vlastnosti, se považují za malé úlohy. Výchozí hodnota je 1 GB.


### <a name="copy-user-jar-cache"></a>Kopírovat mezipaměť jar uživatele

Prase kopíruje soubory JAR vyžadované UDF do distribuované mezipaměti a zpřístupňuje je pro uzly úloh. Tyto JAR se často nemění. Pokud je tato `pig.user.cache.enabled` možnost povolená, umožňuje, aby se jar umístil do mezipaměti pro opakované použití pro úlohy spouštěné stejným uživatelem. Výsledkem je menší zvýšení výkonu úlohy.

1. Pokud ho chcete povolit `pig.user.cache.enabled` , nastavte na true. Výchozí hodnota je false.

1. Chcete-li nastavit základní cestu jar uložených v mezipaměti, `pig.user.cache.location` nastavte na základní cestu. Výchozí hodnota je `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Optimalizace výkonu s nastavením paměti

Následující nastavení paměti může přispět k optimalizaci výkonu skriptu prasete.

* `pig.cachedbag.memusage`: Množství paměti přidělené kontejneru. Penalta je kolekce řazených kolekcí členů. Řazená kolekce členů je uspořádaná sada polí a pole je částí dat. Pokud jsou data v kontejneru mimo přidělenou paměť, přechází na disk. Výchozí hodnota je 0,2, která představuje 20 procent dostupné paměti. Tato paměť se sdílí napříč všemi penaltami v aplikaci.

* `pig.spill.size.threshold`: Penalty větší než tato prahová hodnota velikosti pro přesahy (v bajtech) se přecházejí na disk. Výchozí hodnota je 5 MB.


### <a name="compress-temporary-files"></a>Komprimovat dočasné soubory

Při provádění úlohy vygeneruje prase dočasné soubory. Při komprimaci dočasných souborů dojde ke zvýšení výkonu při čtení nebo zápisu souborů na disk. Pro komprimaci dočasných souborů lze použít následující nastavení.

* `pig.tmpfilecompression`: V případě hodnoty true umožňuje dočasnou kompresi souboru. Výchozí hodnota je false.

* `pig.tmpfilecompression.codec`: Kompresní kodek, který se použije pro komprimaci dočasných souborů. Doporučené kompresní kodeky jsou [LZO](https://www.oberhumer.com/opensource/lzo/) a přichycení k nižšímu využití procesoru.

### <a name="enable-split-combining"></a>Povolit kombinování rozdělení

Pokud je povoleno, malé soubory jsou zkombinovány pro méně úlohy mapy. Tím se zlepší efektivita úloh s mnoha malými soubory. Pokud ho chcete povolit `pig.noSplitCombination` , nastavte na true. Výchozí hodnota je false.


### <a name="tune-mappers"></a>Ladit mapovače

Počet mapovačů je řízen úpravou vlastnosti `pig.maxCombinedSplitSize`. Určuje velikost dat, která mají být zpracována jednou mapou úlohy. Výchozí hodnota je výchozí velikost bloku systému souborů. Zvýšení této hodnoty vede ke snížení počtu úloh mapovače.


### <a name="tune-reducers"></a>Vyladění reduktorů

Počet reduktorů se vypočítá na základě parametru `pig.exec.reducers.bytes.per.reducer`. Parametr určuje počet zpracovaných bajtů na redukci, ve výchozím nastavení 1 GB. Chcete-li omezit maximální počet reduktorů, nastavte `pig.exec.reducers.max` vlastnost ve výchozím nastavení 999.


## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Optimalizace pro Apache HBA s webovým uživatelským rozhraním Ambari

Konfigurace [Apache HBA](https://hbase.apache.org/) je upravena na kartě **Konfigurace HBA** . Následující části popisují některá z důležitých nastavení konfigurace, která ovlivňují výkon adaptérů HBA.

### <a name="set-hbase_heapsize"></a>Nastavení HBASE_HEAPSIZE

Velikost haldy HBA určuje maximální velikost haldy, která bude použita v megabajtech podle *oblasti* a *hlavních* serverů. Výchozí hodnota je 1 000 MB. To by mělo být vyladěno pro zatížení clusteru.

1. Pokud ho chcete upravit, přejděte do podokna **Advanced HBA-ENV** na kartě **Konfigurace** HBA a `HBASE_HEAPSIZE` pak vyhledejte nastavení.

1. Změňte výchozí hodnotu na 5 000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Optimalizace úloh pro čtení a vysoké zatížení

Následující konfigurace jsou důležité pro zlepšení výkonu úloh náročných na čtení.

#### <a name="block-cache-size"></a>Velikost mezipaměti bloků

Bloková mezipaměť je mezipaměť pro čtení. Jeho velikost je ovládána `hfile.block.cache.size` parametrem. Výchozí hodnota je 0,4, což je 40 procent celkové paměti serveru oblasti. Čím větší je velikost mezipaměti bloků, tím rychlejší náhodné čtení bude.

1. Chcete-li tento parametr změnit, přejděte na kartu **Nastavení** na kartě **Konfigurace** HBA a pak vyhledejte **% z RegionServer přidělených pro vyrovnávací paměti pro čtení**.

    ![Velikost mezipaměti bloků HBA](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
1. Chcete-li změnit hodnotu, vyberte ikonu **Upravit** .


#### <a name="memstore-size"></a>Velikost setSize paměťového úložiště

Všechny úpravy jsou uloženy v vyrovnávací paměti, která se označuje jako *setSize paměťového úložiště*. Tím se zvýší celková množství dat, která se dají zapsat na disk v rámci jedné operace, a zrychlí následný přístup k nedávným úpravám. Velikost setSize paměťového úložiště je definována následujícími dvěma parametry:

* `hbase.regionserver.global.memstore.UpperLimit`: Definuje maximální procento serveru oblasti, který může setSize paměťového úložiště kombinovat.

* `hbase.regionserver.global.memstore.LowerLimit`: Definuje minimální procento serveru oblasti, který může setSize paměťového úložiště kombinovat.

Pro optimalizaci pro náhodná čtení můžete snížit horní a dolní limit setSize paměťového úložiště.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Počet řádků načtených při kontrole z disku

Nastavení definuje počet řádků načtených z disku `next` při volání metody na skeneru. `hbase.client.scanner.caching`  Výchozí hodnota je 100. Čím vyšší je počet vzdálených volání z klienta na server oblasti, což vede k rychlejšímu prověřování. Tím se ale taky zvýší tlak paměti na klientovi.

![Počet načtených řádků HBA](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Nenastavujte hodnotu tak, aby čas mezi voláním další metody na skeneru byl větší než časový limit skeneru. Doba trvání časového limitu skeneru je `hbase.regionserver.lease.period` definována vlastností.


### <a name="optimize-write-heavy-workloads"></a>Optimalizace náročných úloh pro zápis

Následující konfigurace jsou důležité pro zlepšení výkonu náročných úloh.


#### <a name="maximum-region-file-size"></a>Maximální velikost souboru oblasti

HBA ukládají data do interního formátu souboru s názvem *HFile*. Vlastnost `hbase.hregion.max.filesize` definuje velikost jednoho HFile pro oblast.  Oblast je rozdělena do dvou oblastí, pokud je součet všech HFiles v oblasti větší než toto nastavení.
 
![HBA HRegion Max velikost souboru](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Čím větší je velikost souboru oblasti, tím menší je počet rozdělení. Velikost souboru můžete zvětšit a určit tak hodnotu, která bude mít za následek maximální výkon zápisu.


#### <a name="avoid-update-blocking"></a>Vyhnout se blokování aktualizací

* Vlastnost `hbase.hregion.memstore.flush.size` definuje velikost, při které je setSize paměťového úložiště vyprázdněna na disk. Výchozí velikost je 128 MB.

* Multiplikátor bloku pro oblasti HBA je definován `hbase.hregion.memstore.block.multiplier`. Výchozí hodnota je 4. Maximální povolená hodnota je 8.

* HBA zablokuje aktualizace, pokud setSize paměťového úložiště bajty`hbase.hregion.memstore.flush.size`( * `hbase.hregion.memstore.block.multiplier`).

    S výchozími hodnotami velikosti vyprázdnění a násobitele bloku jsou aktualizace zablokované, pokud setSize paměťového úložiště je 128 × 4 = 512 MB. Chcete-li snížit počet blokování aktualizací, zvyšte hodnotu `hbase.hregion.memstore.block.multiplier`.

![Multiplikátor bloku pro oblasti HBA](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Definovat velikost setSize paměťového úložiště

Velikost setSize paměťového úložiště je definována `hbase.regionserver.global.memstore.UpperLimit` parametry a. `hbase.regionserver.global.memstore.LowerLimit` Nastavení těchto hodnot je stejné jako u každého dalšího, při zápisu dojde k pozastavení, což také způsobuje častější vyprazdňování a vede k vyššímu výkonu zápisu.


### <a name="set-memstore-local-allocation-buffer"></a>Nastavit místní vyrovnávací paměť pro přidělení setSize paměťového úložiště

Využití vyrovnávací paměti pro místní přidělování setSize paměťového úložiště je určeno vlastností `hbase.hregion.memstore.mslab.enabled`. Pokud je povoleno (true), zabrání se tím fragmentaci haldy během operace silného zápisu. Výchozí hodnota je true.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>Další postup

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
