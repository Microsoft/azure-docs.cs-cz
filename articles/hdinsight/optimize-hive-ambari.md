---
title: Optimalizace Apache Hive s Apache Ambari v Azure HDInsight
description: Ke konfiguraci a optimalizaci Apache Hive použijte webové uživatelské rozhraní Apache Ambari.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 69a4e769677b6f0200f4157305a3a125f82ee76d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864813"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>Optimalizace Apache Hive s Apache Ambari v Azure HDInsight

Apache Ambari je webové rozhraní pro správu a monitorování clusterů HDInsight. Úvod do webového uživatelského rozhraní Ambari najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

Následující části popisují možnosti konfigurace pro optimalizaci celkového výkonu Apache Hive.

1. Pokud chcete změnit parametry konfigurace podregistru, vyberte **podregistr** na postranním panelu služby.
1. Přejděte na kartu **Konfigurace** .

## <a name="set-the-hive-execution-engine"></a>Nastavení spouštěcího modulu pro podregistr

Podregistr poskytuje dva spouštěcí moduly: Apache Hadoop MapReduce a Apache TEZ. Tez je rychlejší než MapReduce. Clustery HDInsight Linux mají tez jako výchozí spouštěcí modul. Změna spouštěcího modulu:

1. Na kartě **Konfigurace** podregistru zadejte **spouštěcí modul** do pole Filtr.

    :::image type="content" source="./media/optimize-hive-ambari/ambari-search-execution.png" alt-text="Spouštěcí modul hledání Apache Ambari" border="true":::

1. Výchozí hodnota vlastnosti **optimalizace** je **tez**.

    :::image type="content" source="./media/optimize-hive-ambari/optimization-apache-tez.png" alt-text="Optimalizace – modul Apache Tez" border="true":::

## <a name="tune-mappers"></a>Ladit mapovače

Hadoop se pokusí rozdělit (*mapovat*) jeden soubor na více souborů a paralelní zpracování výsledných souborů. Počet mapovačů závisí na počtu rozdělení. Následující dva parametry konfigurace řídí počet rozdělení pro spouštěcí modul tez:

* `tez.grouping.min-size`: Nižší omezení velikosti seskupeného rozdělení s výchozí hodnotou 16 MB (16 777 216 bajtů).
* `tez.grouping.max-size`: Horní limit velikosti seskupeného rozdělení s výchozí hodnotou 1 GB (1 073 741 824 bajtů).

Pokud chcete zvýšit latenci, zvyšte podle pokynů výkonu oba tyto parametry a zvyšte tak propustnost.

Pokud třeba chcete nastavit čtyři úlohy mapovače pro velikost dat 128 MB, nastavili byste oba parametry na 32 MB každého (33 554 432 bajtů).

1. Chcete-li změnit parametry limitu, přejděte na kartu **Konfigurace** služby TEZ. Rozbalte panel **Obecné** a vyhledejte `tez.grouping.max-size` `tez.grouping.min-size` parametry a.

1. Nastavte oba parametry na **33 554 432** bajtů (32 MB).

    :::image type="content" source="./media/optimize-hive-ambari/apache-tez-grouping-size.png" alt-text="Velikosti seskupení tez Apache Ambari" border="true":::

Tyto změny mají vliv na všechny úlohy tez napříč serverem. Chcete-li získat optimální výsledek, vyberte příslušné hodnoty parametrů.

## <a name="tune-reducers"></a>Vyladění reduktorů

Apache ORC a přichycení nabízí vysoký výkon. Podregistr ale ve výchozím nastavení může mít příliš málo reduktorů, což způsobuje problémová místa.

Řekněme například, že máte velikost vstupních dat 50 GB. Tato data ve formátu ORC s kompresí s přichycením jsou 1 GB. Podregistr odhaduje počet reduktorů potřebných jako: (počet bajtů vstupu na mapovače/ `hive.exec.reducers.bytes.per.reducer` ).

Ve výchozím nastavení je v tomto příkladu čtyři reduktorů.

`hive.exec.reducers.bytes.per.reducer`Parametr určuje počet zpracovaných bajtů na zmenšení. Výchozí hodnota je 64 MB. Ladění této hodnoty dolů zvyšuje paralelismus a může zvýšit výkon. Příliš nízké ladění může také způsobit příliš mnoho reduktorů, což potenciálně negativně ovlivní výkon. Tento parametr vychází z vašich konkrétních požadavků na data, nastavení komprese a dalších faktorů prostředí.

1. Chcete-li změnit parametr, přejděte na kartu **Konfigurace** podregistru a na stránce nastavení vyhledejte parametr **data na omezení** .

    :::image type="content" source="./media/optimize-hive-ambari/ambari-data-per-reducer.png" alt-text="Data Apache Ambari na zmenšení" border="true":::

1. Vyberte **Upravit** pro úpravu hodnoty na 128 MB (134 217 728 bajtů) a pak stiskněte **ENTER** pro uložení.

    :::image type="content" source="./media/optimize-hive-ambari/data-per-reducer-edited.png" alt-text="Ambari data na redukci – upraveno" border="true":::
  
    Při zadání velikosti 1 024 MB s 128 MB dat na redukci je osm reduktorů (1024/128).

1. Nesprávná hodnota pro parametr **data na snížení** hodnoty může mít za následek velký počet reduktorů, což negativně ovlivní výkon dotazů. Pro omezení maximálního počtu reduktorů nastavte `hive.exec.reducers.max` na odpovídající hodnotu. Výchozí hodnota je 1009.

## <a name="enable-parallel-execution"></a>Povolit paralelní provádění

Dotaz na podregistr se spustí v jedné nebo několika fázích. Pokud je možné nezávislé fáze spustit paralelně, což zvýší výkon dotazů.

1. Chcete-li povolit paralelní provádění dotazů, přejděte na kartu **Konfigurace** podregistru a vyhledejte `hive.exec.parallel` vlastnost. Výchozí hodnota je False. Změňte hodnotu na true a potom stisknutím klávesy **ENTER** hodnotu uložte.

1. Chcete-li omezit počet úloh, které mají být spuštěny paralelně, upravte `hive.exec.parallel.thread.number` vlastnost. Výchozí hodnota je 8.

    :::image type="content" source="./media/optimize-hive-ambari/apache-hive-exec-parallel.png" alt-text="Apache Hive exec paralelní zobrazení" border="true":::

## <a name="enable-vectorization"></a>Povolit vektorování

Podregistr zpracovává řádek data řádku. Rozvektorování směruje podregistr za účelem zpracování dat v blocích 1 024 řádků, nikoli v jednom řádku. Vektory lze použít pouze pro formát souboru ORC.

1. Chcete-li povolit vektorové provádění dotazů, přejděte na kartu **Konfigurace** podregistru a vyhledejte `hive.vectorized.execution.enabled` parametr. Výchozí hodnota je true pro podregistr 0.13.0 nebo novější.

1. Chcete-li povolit parametrizované spouštění pro možnost zmenšení dotazu, nastavte `hive.vectorized.execution.reduce.enabled` parametr na hodnotu true. Výchozí hodnota je False.

    :::image type="content" source="./media/optimize-hive-ambari/hive-vectorized-execution.png" alt-text="Apache Hive vektorové spuštění" border="true":::

## <a name="enable-cost-based-optimization-cbo"></a>Povolit optimalizaci na základě nákladů (CBO)

Ve výchozím nastavení používá podregistr sadu pravidel pro vyhledání jednoho optimálního plánu spuštění dotazu. Optimalizace na základě nákladů (CBO) vyhodnocuje více plánů pro spuštění dotazu. A přiřadí každému plánu náklady a pak určí plán nejlevnější pro spuštění dotazu.

Pokud chcete povolit CBO, přejděte na nastavení konfigurace **podregistru**  >    >   a vyhledejte **optimalizaci na základě nákladů** a pak přepněte přepínací tlačítko na **zapnuto**.

:::image type="content" source="./media/optimize-hive-ambari/hdinsight-cbo-config.png" alt-text="Optimalizátor založený na cenách HDInsight" border="true":::

Následující dodatečné parametry konfigurace zvyšují výkon dotazů na podregistr, pokud je povolená možnost CBO:

* `hive.compute.query.using.stats`

    Při nastavení na hodnotu true používá podregistr ve svých metastore statistiku k zodpovězení jednoduchých dotazů `count(*)` , jako je.

    :::image type="content" source="./media/optimize-hive-ambari/hive-compute-query-using-stats.png" alt-text="Apache Hive výpočetní dotaz s použitím statistik" border="true":::

* `hive.stats.fetch.column.stats`

    Statistiky sloupce se vytvoří, když je povolený CBO. Pro optimalizaci dotazů používá podregistr statistiku sloupce, které jsou uložené v metastore. Načtení statistiky sloupců pro každý sloupec trvá déle, pokud je počet sloupců vysoký. Když se nastaví na false, toto nastavení zakáže načítání statistik sloupce z metastore.

    :::image type="content" source="./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png" alt-text="Statistiky sloupce Apache Hive sady statistik" border="true":::

* `hive.stats.fetch.partition.stats`

    Základní statistické údaje o oddílech, jako je počet řádků, velikost dat a velikost souboru, jsou uloženy v metastore. Pokud je nastavená hodnota true, Statistika oddílu se načte z metastore. V případě hodnoty false je velikost souboru načtena ze systému souborů. A počet řádků je načten ze schématu řádku.

    :::image type="content" source="./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png" alt-text="Statistiky podregistru – Statistika oddílů sady" border="true":::

## <a name="enable-intermediate-compression"></a>Povolit mezilehlé komprese

Úkoly mapování vytvářejí mezilehlé soubory, které jsou používány úlohami pro snížení. Mezilehlé Komprese zmenší velikost mezilehlého souboru.

Úlohy Hadoop jsou obvykle kritické pro vstupně-výstupní operace. Komprimace dat může zrychlit vstupně-výstupní operace a celkový přenos v síti.

K dispozici jsou tyto typy komprese:

| Formát | Nástroj | Algoritmus | Přípona souboru | Rozdělitelné? |
| --- | --- | --- | --- | --- |
| GZIP | GZIP | DEFLATE | `.gz` | No |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Yes |
| LZO | `Lzop` | LZO | `.lzo` | Ano, pokud je indexovaný |
| Snappy | – | Snappy | Snappy | No |

Jako obecné pravidlo je důležité mít rozdělenou část kompresní metody, jinak se vytvoří několik mapovačů. Pokud jsou vstupní data text, `bzip2` je to nejlepší možnost. V případě formátu ORC je přichycení nejrychlejší možnost komprese.

1. Chcete-li povolit mezilehlé komprese, přejděte na kartu **Konfigurace** podregistru a nastavte `hive.exec.compress.intermediate` parametr na hodnotu true. Výchozí hodnota je False.

    :::image type="content" source="./media/optimize-hive-ambari/hive-exec-compress-intermediate.png" alt-text="' Podregistr exec Compressed Intermediate '" border="true":::

    > [!NOTE]  
    > Chcete-li zkomprimovat mezilehlé soubory, vyberte Kompresní kodek s nižšími náklady na procesor, a to i v případě, že kodek nemá vysoký kompresní výstup.

1. Chcete-li nastavit pomocný kodek komprese, přidejte `mapred.map.output.compression.codec` do `hive-site.xml` souboru nebo vlastní vlastnost `mapred-site.xml` .

1. Přidání vlastního nastavení:

    a. Přejděte ke **konfiguraci podregistru**  >    >  **Upřesnit**  >  **vlastní podregistr – lokalita**.

    b. V dolní části podokna vlastní podregistr-web vyberte **Přidat vlastnost...**

    c. V okně Přidat vlastnost zadejte `mapred.map.output.compression.codec` jako klíč a `org.apache.hadoop.io.compress.SnappyCodec` jako hodnotu.

    d. Vyberte **Přidat**.

    :::image type="content" source="./media/optimize-hive-ambari/hive-custom-property.png" alt-text="Přidat vlastní vlastnost Apache Hive" border="true":::

    Toto nastavení komprimuje mezilehlé soubory pomocí přichycení. Po přidání této vlastnosti se tato vlastnost zobrazí v podokně vlastní podregistr-Web.

    > [!NOTE]  
    > Tento postup upraví `$HADOOP_HOME/conf/hive-site.xml` soubor.

## <a name="compress-final-output"></a>Komprimovat konečný výstup

Konečný výstup podregistru je také možné zkomprimovat.

1. Chcete-li zkomprimovat konečný výstup podregistru, přejděte na kartu **Konfigurace** podregistru a nastavte `hive.exec.compress.output` parametr na hodnotu true. Výchozí hodnota je False.

1. Chcete-li zvolit výstupní Kompresní kodek, přidejte `mapred.output.compression.codec` vlastní vlastnost do podokna vlastní web podregistr, jak je popsáno v kroku 3 v předchozí části.

    :::image type="content" source="./media/optimize-hive-ambari/hive-custom-property2.png" alt-text="Add2 vlastní vlastnosti Apache Hive" border="true":::

## <a name="enable-speculative-execution"></a>Povolit spekulativní provádění

Spekulativní spuštění spouští určitý počet duplicitních úloh ke zjištění a zamítnutí seznamu pomalých spuštěných úloh. A zároveň zlepšuje celkové provádění úloh optimalizací jednotlivých výsledků úkolů.

Spekulativní provádění by nemělo být zapnuté pro dlouhotrvající MapReduce úlohy s velkým množstvím vstupu.

* Chcete-li povolit spekulativní provádění, přejděte na kartu **Konfigurace** podregistru a nastavte `hive.mapred.reduce.tasks.speculative.execution` parametr na hodnotu true. Výchozí hodnota je False.

    :::image type="content" source="./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png" alt-text="' Mapred podregistru snižuje spekulativní provádění úkolů '" border="true":::

## <a name="tune-dynamic-partitions"></a>Ladit dynamické oddíly

Podregistr umožňuje vytvářet dynamické oddíly při vkládání záznamů do tabulky, aniž by bylo nutné předdefinovat každý oddíl. Tato možnost je výkonná funkce. I když může dojít k vytvoření velkého počtu oddílů. A pro každý oddíl je to velký počet souborů.

1. Pro dynamické oddíly v podregistru `hive.exec.dynamic.partition` by měla být hodnota parametru true (výchozí).

1. Změňte režim dynamického oddílu na *stricted*. V přísném režimu musí být alespoň jeden oddíl statický. Toto nastavení zabraňuje dotazům bez filtru oddílů v klauzuli WHERE, což znamená, že metoda *Strict* zabraňuje dotazům, které kontrolují všechny oddíly. Přejděte na kartu **Konfigurace** podregistru a nastavte `hive.exec.dynamic.partition.mode` na hodnotu **Strict**. Výchozí hodnota je **nestriktní**.

1. Chcete-li omezit počet dynamických oddílů, které mají být vytvořeny, upravte `hive.exec.max.dynamic.partitions` parametr. Výchozí hodnota je 5000.

1. Chcete-li omezit celkový počet dynamických oddílů na uzel, upravte `hive.exec.max.dynamic.partitions.pernode` . Výchozí hodnota je 2000.

## <a name="enable-local-mode"></a>Povolit místní režim

Místní režim umožňuje podregistru provádět všechny úlohy v jednom počítači. Nebo někdy v jednom procesu. Toto nastavení zlepšuje výkon dotazů, pokud jsou vstupní data malá. A režie při spouštění úloh pro dotazy spotřebovává významné procento z celkového spuštění dotazu.

Chcete-li povolit místní režim, přidejte `hive.exec.mode.local.auto` parametr do panelu vlastní podregistr-site, jak je vysvětleno v kroku 3 oddílu [Povolení mezilehlé komprese](#enable-intermediate-compression) .

:::image type="content" source="./media/optimize-hive-ambari/hive-exec-mode-local-auto.png" alt-text="Místní auto v režimu Apache Hive exec" border="true":::

## <a name="set-single-mapreduce-multigroup-by"></a>Nastavit jeden MapReduce pro více skupin podle

Pokud je tato vlastnost nastavená na hodnotu true, vytvoří se ve více skupinách dotaz s běžnými klíči Group by jedna úloha MapReduce.  

Chcete-li toto chování povolit, přidejte `hive.multigroupby.singlereducer` parametr do podokna vlastní podregistr-site, jak je vysvětleno v kroku 3 oddílu [Povolení mezilehlé komprese](#enable-intermediate-compression) .

:::image type="content" source="./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png" alt-text="Jeden MapReduce pro více skupin v podmnožině" border="true":::

## <a name="additional-hive-optimizations"></a>Další optimalizace pro podregistr

Následující části popisují další optimalizace týkající se podregistru, které můžete nastavit.

### <a name="join-optimizations"></a>Sloučit optimalizace

Výchozím typem spojení v podregistru je *náhodné spojení*. V podregistru speciální mapovače přečtou vstup a vygeneruje dvojici klíč/hodnota JOIN k mezilehlému souboru. Hadoop seřadí a sloučí tyto páry v náhodně připravené fázi. Tato fáze náhodného zpracování je náročná. Výběr správného spojení na základě vašich dat může významně zlepšit výkon.

| Typ spojení | Když | Jak | Nastavení podregistru | Komentáře |
| --- | --- | --- | --- | --- |
| Náhodně připojit | <ul><li>Výchozí volba</li><li>Vždy funguje</li></ul> | <ul><li>Čtení z části jedné z tabulek</li><li>Intervaly a řazení podle klávesy JOIN</li><li>Každé omezení pošle jednu sadu.</li><li>Spojení se provádí na straně snížení</li></ul> | Není potřeba žádné významné nastavení podregistru | Funguje kdykoli |
| Připojit k mapě | <ul><li>Jedna tabulka se může vejít do paměti.</li></ul> | <ul><li>Přečte malou tabulku do tabulky hash paměti.</li><li>Streamování prostřednictvím části velkého souboru</li><li>Spojí každý záznam z zatřiďovací tabulky.</li><li>Spojení jsou pouze mapovačem.</li></ul> | `hive.auto.confvert.join=true` | Rychlá, ale omezená |
| Seřadit slučovací interval | Pokud jsou obě tabulky: <ul><li>Řazeno stejným</li><li>Je rozdělené na stejné</li><li>Spojení s seřazeným/rozdaným sloupcem</li></ul> | Každý proces: <ul><li>Přečte z každé tabulky kontejner.</li><li>Zpracuje řádek s nejnižší hodnotou.</li></ul> | `hive.auto.convert.sortmerge.join=true` | Odrážející |

### <a name="execution-engine-optimizations"></a>Optimalizace prováděcího modulu

Další doporučení pro optimalizaci spouštěcího modulu podregistru:

| Nastavení | Doporučeno | Výchozí nastavení HDInsight |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | True = bezpečnější, pomalejší; false = rychlejší | false (nepravda) |
| `tez.am.resource.memory.mb` | horní mez velikosti 4 GB pro většinu | Automaticky laděné |
| `tez.session.am.dag.submit.timeout.secs` | 300 + | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000 + | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40 000 + | 20000 |

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optimalizace dotazů Apache Hivu v Azure HDInsightu](./hdinsight-hadoop-optimize-hive-query.md)
* [Optimalizace clusterů](./optimize-hive-ambari.md)
* [Optimalizace Apache HBase](./optimize-hbase-ambari.md)
* [Optimalizace Apache Pigu](./optimize-pig-ambari.md)
