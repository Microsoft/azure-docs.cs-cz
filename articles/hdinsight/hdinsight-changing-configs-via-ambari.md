---
title: Optimalizace konfigurace clusterů s Ambari – Azure HDInsight
description: Konfigurace a Optimalizace clusterů HDInsight pomocí webového uživatelského rozhraní Ambari.
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: ashish
ms.openlocfilehash: b16020ec421eb077dca8318c765834e6f64f3235
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600593"
---
# <a name="use-ambari-to-optimize-hdinsight-cluster-configurations"></a>Optimalizace konfigurace clusterů HDInsight pomocí Ambari

HDInsight nabízí clustery systému Apache Hadoop pro rozsáhlé zpracování dat aplikace. Správa, monitorování a optimalizace tyto komplexní clustery s několika uzly může být náročné. [Apache Ambari](http://ambari.apache.org/) je webové rozhraní pro správu a monitorování clusterů HDInsight s Linuxem.  V případě clusterů Windows pomocí Ambari [rozhraní REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

Úvod do pomocí webového uživatelského rozhraní Ambari, naleznete v tématu [HDInsight Správa clusterů pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md)

Přihlaste se k Ambari v `https://CLUSTERNAME.azurehdidnsight.net` pomocí svých přihlašovacích údajů clusteru. Na úvodní obrazovce se zobrazí řídicího panelu s přehledem.

![Řídicí panel Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Webové uživatelské rozhraní Ambari slouží ke správě hostitelů, služby, výstrahy, konfigurace a zobrazení. Ambari může t použije k vytvoření clusteru HDInsight, upgrade služby, spravovat zásobníky a verze, vyřadit z provozu nebo recommission hostitele nebo přidání služby do clusteru.

## <a name="manage-your-clusters-configuration"></a>Spravovat konfiguraci vašeho clusteru

Nastavení konfigurace pomoct vyladit konkrétní službu. Pokud chcete změnit nastavení konfigurace služby, vyberte službu z **služby** postranního panelu (na levé straně) a potom přejděte na **Configs** karty na stránce podrobností služby.

![Boční panel služby](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Změnit velikost haldy NameNode Java

Velikost haldy NameNode Java, závisí na mnoha faktorech, jako je zatížení v clusteru, počet souborů a čísel bloky. Výchozí velikost 1 GB pracuje s Většina clusterů, i když některé úlohy může vyžadovat více nebo méně paměti. 

Chcete-li změnit velikost haldy NameNode Java:

1. Vyberte **HDFS** postranním panelu služby a přejděte **Configs** kartu.

    ![Konfigurace HDFS](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

1. Najít nastavení **velikost haldy NameNode Java**. Můžete také použít **filtr** textového pole zadejte a vyhledejte konkrétní nastavení. Vyberte **pera** ikonu vedle názvu nastavení.

    ![Velikost haldy NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

1. Zadejte novou hodnotu do textového pole a stiskněte klávesu **Enter** uložte změnu.

    ![Upravit velikost haldy NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

1. Velikost haldy NameNode Java se změní na 2 GB od 1 GB.

    ![Upravit velikost haldy NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Uložte změny kliknutím na zelený **Uložit** tlačítko nahoře na obrazovce pro konfiguraci.

    ![Uložit změny](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="hive-optimization"></a>Optimalizace Hive

Následující části popisují možnosti konfigurace pro optimalizaci celkový výkon Hive.

1. Chcete-li upravit parametry konfigurace Hive, vyberte **Hive** postranním panelu služby.
1. Přejděte **Configs** kartu.

### <a name="set-the-hive-execution-engine"></a>Nastavit spouštěcí modul Hive

Hive obsahuje dva motory spuštění: MapReduce a Tez. Tez je rychlejší než MapReduce. Clustery HDInsight Linux mají jako výchozí prováděcí modul Tez. Chcete-li změnit prováděcí modul:

1. V podregistru **Configs** kartu, zadejte **prováděcí modul** v poli filtru.

    ![Hledání prováděcího modulu](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

1. **Optimalizace** výchozí hodnota této vlastnosti je **Tez**.

    ![Optimalizace - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Vyladění mapovačů

Hadoop se pokusí rozdělit (*mapy*) jednoho souboru do více souborů a proces výsledné soubory paralelně. Počet mapovačů závisí na počtu rozdělení. Následující dvě konfigurační parametry jednotky počet rozdělení pro prováděcí modul Tez:

* `tez.grouping.min-size`: Nižší omezení velikosti seskupené rozdělení s výchozí hodnotou 16 MB (16,777,216 bajtů).
* `tez.grouping.max-size`: Horní omezení velikosti seskupené rozdělení s výchozí hodnotou 1 GB (1 073 741 824 bajtů).

Jako výkonu říci snížit oba tyto parametry, chcete-li zlepšit latenci, zvýšit pro větší propustnost.

Například pokud chcete nastavit čtyři úkoly Mapovač dat velikost 128 MB, nastavíte oba parametry na 32 MB každý (33,554,432 bajtů).

1. Upravit parametry limitu, přejděte **Configs** kartu Tez služby. Rozbalte **Obecné** panelu a vyhledejte `tez.grouping.max-size` a `tez.grouping.min-size` parametry.

1. Nastavte oba parametry na **33,554,432** bajtů (32 MB).

    ![Velikosti seskupení tez](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Tyto změny se projeví všechny úlohy Tez napříč serveru. K získání optimálního výsledku, vyberte odpovídající parametr hodnoty.

### <a name="tune-reducers"></a>Vyladění reduktorů

ORC a Snappy nabízí vysoký výkon. Ale Hive pravděpodobně příliš málo reduktorů ve výchozím nastavení, příčinou kritických bodů.

Řekněme například, že mají velikost vstupních dat 50 GB. Formát dat v ORC se Tenhle komprese je 1 GB. Hive odhady počtu reduktorů potřebné jako: (počet bajtů vstup mapovačů / `hive.exec.reducers.bytes.per.reducer`).

Tento příklad s výchozím nastavením je 4 reduktorů.

`hive.exec.reducers.bytes.per.reducer` Parametr určuje počet bajty zpracovaných za redukční funkci. Výchozí hodnota je 64 MB. Ladění tuto hodnotu dolů zvyšuje paralelismu a může zlepšit výkon. Optimalizace příliš nízké, by mohla vést příliš mnoho reduktorů potenciálně nepříznivý vliv na výkon. Tento parametr je založené na požadavcích konkrétní data, nastavení komprese a dalších faktorech prostředí.

1. Upravte parametr, přejděte na podregistr **Configs** kartu a najít **dat za redukční funkci** parametr na stránce nastavení.

    ![Data pro jednotlivé redukční funkci](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
1. Vyberte **upravit** upravte hodnotu na 128 MB (134,217,728 bajtů), a potom stiskněte klávesu **Enter** uložte.

    ![Data pro jednotlivé redukční funkci – úpravy](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Zadaný vstupní velikost 1 024 MB, 128 MB dat za redukční funkci, existují 8 reduktorů (1024/128).

1. Nesprávná hodnota pro **dat za redukční funkci** parametr může mít za následek velký počet reduktorů, což nepříznivě ovlivňuje výkon dotazů. Chcete-li omezit maximální počet reduktorů, nastavte `hive.exec.reducers.max` na odpovídající hodnotu. Výchozí hodnota je 1009.

### <a name="enable-parallel-execution"></a>Povolit paralelní provádění

Dotaz Hive spouští v jedné nebo více fázích. Pokud nezávislé fáze můžete spustit paralelně, který zvyšuje výkon dotazů.

1.  Pokud chcete povolit paralelní provádění dotazů, přejděte na podregistr **Config** kartě a vyhledejte `hive.exec.parallel` vlastnost. Výchozí hodnota je false. Změňte hodnotu na true a potom stiskněte klávesu **Enter** uložte hodnotu.
 
1.  Chcete-li omezit počet k paralelnímu spouštění úloh, upravte `hive.exec.parallel.thread.number` vlastnost. Výchozí hodnota je 8.

    ![Hive paralelní exec](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Povolte vektorizaci

Hive zpracovává data řádek po řádku. Vektorizace přesměruje Hive ke zpracování dat v blocích po 1 024 řádků než jeden řádek v čase. Vektorizace platí pouze pro formát souborů ORC.

1. Pokud chcete povolit spuštění vektorizovaných dotazu, přejděte na podregistr **Configs** kartu, vyhledejte `hive.vectorized.execution.enabled` parametru. Výchozí hodnota je true pro Hive 0.13.0 nebo novější.
 
1. Pokud chcete povolit vektorizovaných provádění dotazu na straně snížit, nastavte `hive.vectorized.execution.reduce.enabled` parametr na hodnotu true. Výchozí hodnota je false.

    ![Spuštění Hive vektorizovaná](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Povolit optimalizaci na základě nákladů (CBO)

Ve výchozím nastavení se řídí sadu pravidel k vyhledání jednoho plánu spuštění optimální dotaz Hive. Optimalizaci na základě nákladů (CBO) vyhodnocuje více plánů při spuštění dotazu a přiřadí s náklady na každý plán, pak určuje nejlevnější plán při spuštění dotazu.

Pokud chcete povolit CBO, přejděte na podregistr **Configs** kartu a vyhledejte `parameter hive.cbo.enable`, přepněte se na přepínací tlačítko **na**.

![CBO config](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Následující další konfigurační parametry zvýšení výkonu dotazů Hive obrysů CBO:

* `hive.compute.query.using.stats`

    Když nastavenou na hodnotu true, Hive používá statistické údaje uložené v jeho metastore zodpovědět jednoduchých dotazů, jako je `count(*)`.

    ![Statistika CBO](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Statistiky sloupce vytvářejí, když je povolené CBO. Hive používá statistiky sloupce, které jsou uloženy v metastore, optimalizovat dotazy. Načítání statistiky sloupce pro každý sloupec trvá déle, když je vysoká. počet sloupců. Pokud je nastavený na hodnotu false, toto nastavení zakáže načítání statistiky sloupce z metastore.

    ![Hive statistiky sloupce set statistiky](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Základním oddílu statistiky, jako je počet řádků, velikost dat a velikost souboru jsou uloženy v metastore. Při nastavení na hodnotu true, je oddíl, který statistiky jsou načtena z metastore. Pokud má hodnotu NEPRAVDA, velikost souboru je načtený ze systému souborů a počet řádků je načtena z řádků schématu.

    ![Hive statistiky oddílu set statistiky](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Povolit kompresi zprostředkující

Mapa úlohy vytvoření dočasné soubory, které jsou používány úlohy redukční funkci. Zprostředkující komprese zmenší velikost pomocný soubor.

Úlohy systému Hadoop jsou obvykle bottlenecked vstupně-výstupních operací. Komprese dat můžou urychlit vstupně-výstupní operace a celkový přenos v síti.

K dispozici kompresní typy jsou:

| Formát | Nástroj | algoritmus | Přípona souboru | Rozdělitelné? |
| -- | -- | -- | -- | -- |
| GZIP | GZIP | DEFLATE | .gz | Ne |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Ano |
| LZO | Lzop | LZO | .lzo | Ano, pokud indexovat |
| Tenhle | neuvedeno | Tenhle | Tenhle | Ne |

Jako obecné pravidlo je důležité mít metodu komprese umožňujícím rozdělení, jinak bude velmi málo mapovačů vytvořen. Pokud jsou vstupní data text, `bzip2` je nejlepší možností. Pro formát ORC Snappy je nejrychlejší možnost komprese.

1. Povolit kompresi zprostředkující, přejděte na podregistr **Configs** kartu a potom nastavte `hive.exec.compress.intermediate` parametr na hodnotu true. Výchozí hodnota je false.

    ![Komprese exec Hive zprostředkující](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]
    > Pokud chcete komprimovat zprostředkující soubory, zvolte kompresní kodek s nižší náklady, procesoru, i v případě, že t kódu kodek mít vysokou kompresi výstup.

1. Chcete-li nastavit zprostředkující kompresní kodek, přidat vlastní vlastnost `mapred.map.output.compression.codec` k `hive-site.xml` nebo `mapred-site.xml` souboru.

1. Chcete-li přidat vlastní nastavení:

    a. Přejděte na podregistr **Configs** kartě a vyberte **Upřesnit** kartu.

    b. V části **Upřesnit** kartu, vyhledejte a rozbalte **vlastní csrf hive** podokně.

    c. Klikněte na odkaz **přidat vlastnost** v dolní části podokna vlastní hive-site.

    d. V okně Přidat vlastnost zadejte `mapred.map.output.compression.codec` jako klíč a `org.apache.hadoop.io.compress.SnappyCodec` jako hodnotu.

    e. Klikněte na tlačítko **Add** (Přidat).

    ![Vlastní vlastnost Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    To se komprimovat pomocný soubor pomocí Tenhle komprese. Po přidání vlastnosti se zobrazí v podokně vlastní hive-site.

    > [!NOTE]
    > Tento postup upravuje `$HADOOP_HOME/conf/hive-site.xml` souboru.

### <a name="compress-final-output"></a>Komprese závěrečný výstup

Finální výstup Hive můžete také zkomprimují.

1. Pokud chcete komprimovat závěrečný výstup Hive, přejděte na podregistr **Configs** kartu a potom nastavte `hive.exec.compress.output` parametr na hodnotu true. Výchozí hodnota je false.

1. Chcete-li zvolit kompresní kodek výstup, přidejte `mapred.output.compression.codec` vlastní vlastnost do podokna hive lokality vlastní, jak je popsáno v kroku 3 předchozí části.

    ![Vlastní vlastnost Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Povolit spekulativního spouštění

Spekulativního spouštění spustí řadu duplicitní úlohy, aby bylo možné zjistit a seznam zakázaných sledovací modul pomalých úloh, při současném zvyšování celkové provádění úlohy optimalizace výsledky jednotlivých úkolů.

Nesmí obsahovat více t spekulativního spouštění možné zapnout pro dlouhotrvající úlohy MapReduce s velkým množstvím vstup.

* Pokud chcete povolit spekulativního spouštění, přejděte na podregistr **Configs** kartu a potom nastavte `hive.mapred.reduce.tasks.speculative.execution` parametr na hodnotu true. Výchozí hodnota je false.

    ![Hive mapred omezení spekulativního spouštění úlohy](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Vyladění dynamických oddílů

Hive umožňuje vytvářet dynamické oddíly po vložení záznamu do tabulky, bez předběžné definování každého oddílu. Toto je výkonná funkce, i když to může vést k vytvoření velkého počtu oddílů a velký počet souborů pro každý oddíl.

1. Pro Hive na dynamické oddíly `hive.exec.dynamic.partition` hodnota parametru by měl mít hodnotu true (výchozí).

1. Změnit režim dynamické oddílu, který chcete *striktní*. Nejméně jeden oddíl ve striktním režimu, musí být statické. To zabraňuje dotazy bez filtru oddílu v klauzuli WHERE, tedy *striktní* brání dotazy, které prohledávat všechny oddíly. Přejděte na podregistr **Configs** kartu a potom nastavte `hive.exec.dynamic.partition.mode` k **striktní**. Výchozí hodnota je **nonstrict**.
 
1. Chcete-li omezit počet dynamických oddílů, který se má vytvořit, upravit `hive.exec.max.dynamic.partitions` parametru. Výchozí hodnota je 5 000.
 
1. Chcete-li omezit celkový počet dynamických oddílů podle počtu uzlů, upravte `hive.exec.max.dynamic.partitions.pernode`. Výchozí hodnota je 2000.

### <a name="enable-local-mode"></a>Povolit místní režim

Místní režim umožňuje Hive k plnění všech úloh úlohy na jednom počítači, nebo někdy také v jediném procesu. To zvyšuje výkon dotazů, pokud je malá vstupní data a režijní náklady na spouštění úlohy pro dotazy využívá významného procenta celkového provádění dotazu.

Chcete-li povolit místní režim, přidejte `hive.exec.mode.local.auto` parametr hive lokality panel vlastní, jak je popsáno v kroku 3 [povolit kompresi zprostředkující](#enable-intermediate-compression) oddílu.

![Hive automaticky místní režim exec](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Sada jeden MapReduce MultiGROUP podle

Když se tato vlastnost nastavena na hodnotu true, dotaz MultiGROUP podle s společné klíče Group generuje jedné úlohy MapReduce.  

Chcete-li povolit toto chování, přidejte `hive.multigroupby.singlereducer` parametr do podokna hive lokality vlastní, jak je popsáno v kroku 3 [povolit kompresi zprostředkující](#enable-intermediate-compression) oddílu.

![Hive nastavit jeden MapReduce MultiGROUP](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Další optimalizace Hive

Následující části popisují další optimalizace související Hive, můžete nastavit.

#### <a name="join-optimizations"></a>Připojte se k optimalizace

Výchozí typ spojení v Hive *shuffle spojení*. V Hive speciální mapovačů čtení vstupu a generovat dvojici klíč/hodnota spojení do pomocný soubor. Hadoop seřadí a sloučí tyto dvojice ve fázi náhodně. Tato fáze shuffle je nákladný. Výběr správné spojení na základě vašich dat může výrazně zlepšit výkon.

| Typ připojení | Kdy | Jak | Nastavení hivu | Komentáře |
| -- | -- | -- | -- | -- |
| Shuffle spojení | <ul><li>Výchozí volba</li><li>Vždy funguje.</li></ul> | <ul><li>Čte z část jednu z tabulek</li><li>Kontejnery a řazení na klíč spojení</li><li>Odešle každý zmenšit jeden interval.</li><li>Spojení se provádí na straně zmenšit</li></ul> | Žádné významné Hive nastavení potřebné | Pokaždé, když funguje |
| Spojení map | <ul><li>Můžete přizpůsobit jedné tabulky v paměti</li></ul> | <ul><li>Načte malé tabulky do tabulky hash v paměti</li><li>Datové proudy přes část velkého souboru</li><li>Připojí každý záznam z tabulky hash</li><li>Spojení jsou mapovačem samostatně</li></ul> | `hive.auto.confvert.join=true` | Velmi rychlé zpracování, ale omezené |
| Kbelíku slučování řazení | Pokud jsou obě tabulky: <ul><li>Seřazené stejné</li><li>Kterých rozdělit stejné</li><li>Propojení na seřazený/kterých rozdělit sloupec</li></ul> | Každý proces: <ul><li>Přečte kbelíky z každé tabulky</li><li>Zpracuje řádek s nejnižší hodnotou</li></ul> | `hive.auto.convert.sortmerge.join=true` | Velmi efektivní |

#### <a name="execution-engine-optimizations"></a>Spuštění modulu optimalizace

Další doporučení pro optimalizaci prováděcí modul Hive:

| Nastavení | Doporučené | Výchozí HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | True = bezpečnější, pomalejší; false = rychleji | false (nepravda) |
| `tez.am.resource.memory.mb` | 4 GB horní mez pro většinu | Automaticky Laděná |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="pig-optimization"></a>Optimalizace pig

Pig vlastnosti lze změnit webové uživatelské rozhraní vyladění dotazů Pig Ambari. Úpravy vlastností Pig z Ambari přímo upraví vlastnosti Pig `/etc/pig/2.4.2.0-258.0/pig.properties` souboru.

1. K úpravě vlastností Pig, přejděte Pig **Configs** kartu a potom rozbalte **rozšířené vlastnosti pig** podokně.

1. Najít, zrušte komentář a změňte hodnotu vlastnosti, kterou chcete upravit.

1. Vyberte **Uložit** v horní pravé části okna uložte novou hodnotu. Některé vlastnosti může vyžadovat restartování služby.

    ![Upřesňující vlastnosti pig](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]
> Jakékoli nastavení na úrovni relace přepisují hodnoty vlastností v `pig.properties` souboru.

### <a name="tune-execution-engine"></a>Ladění prováděcího modulu

Dva moduly provádění jsou k dispozici pro spuštění skriptů Pig: MapReduce a Tez. Tez je modul optimalizovaný a je mnohem rychlejší než MapReduce.

1. Úprava spouštěcí modul v **rozšířené vlastnosti pig** podokně vyhledejte vlastnost `exectype`.

1. Výchozí hodnota je **MapReduce**. Změňte ho na **Tez**.


### <a name="enable-local-mode"></a>Povolit místní režim

Podobně jako Hive, místní režim se používá k urychlení úlohy s relativně menší množství dat.

1. Chcete-li povolit místní režim, nastavte `pig.auto.local.enabled` k **true**. Výchozí hodnota je false.

1. Úlohy velikost vstupních dat menší než `pig.auto.local.input.maxbytes` hodnota vlastnosti jsou považovány za malé úlohy. Výchozí hodnota je 1 GB.


### <a name="copy-user-jar-cache"></a>Zkopírujte soubor jar mezipaměti uživatele

Pig zkopíruje soubory JAR vyžadované uživatelem definovanými funkcemi distribuované mezipaměti je zpřístupnit pro uzly úlohy. Tyto kromě souborů JAR často nemění. Pokud je povoleno, `pig.user.cache.enabled` nastavení umožňuje kromě souborů JAR budou umístěny v mezipaměti je znovu použít pro úlohy spustit stejným uživatelem. Výsledkem je dílčí nárůst výkon úlohy.

1. Chcete-li povolit, nastavte `pig.user.cache.enabled` na hodnotu true. Výchozí hodnota je false.

1. Chcete-li nastavena základní cesta v mezipaměti JAR, nastavte `pig.user.cache.location` základní cestu. Výchozí hodnota je `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Optimalizujte výkon pomocí nastavení paměti

Následující nastavení paměti může pomoct optimalizovat výkon skriptů Pig.

* `pig.cachedbag.memusage`: Velikost přidělené paměti na kontejner. Kontejner je kolekce řazených kolekcí členů. Řazená kolekce členů je uspořádaný sadu polí a část dat je pole. Pokud jsou data v kontejner nad rámec přidělená paměť, je přesahovat na disk. Výchozí hodnota je 0.2, který představuje 20 procent dostupné paměti. Tuto paměť je sdílen mezi všechny kontejnery objektů a dat v aplikaci.

* `pig.spill.size.threshold`: Jsou přesahovat kontejnery objektů a dat větší než tato prahová hodnota velikosti přelití (v bajtech) na disk. Výchozí hodnota je 5 MB.


### <a name="compress-temporary-files"></a>Komprimaci dočasných souborů

Pig generuje dočasných souborů během provádění úlohy. Komprese dočasné soubory způsobí zvýšení výkonu při čtení nebo zápis do souborů na disk. Tato nastavení slouží k komprimaci dočasných souborů.

* `pig.tmpfilecompression`: V případě hodnoty true umožňuje komprese dočasný soubor. Výchozí hodnota je false.

* `pig.tmpfilecompression.codec`: Kompresní kodek pro kompresi dočasné soubory. Kodeky doporučené komprese jsou LZO a Snappy nižší využití procesoru.

### <a name="enable-split-combining"></a>Povolit kombinování rozdělení

Při povolení malých souborů zkombinují méně úkolů mapy. Tím se zlepšuje efektivitu úlohy s velkým množstvím malých souborů. Chcete-li povolit, nastavte `pig.noSplitCombination` na hodnotu true. Výchozí hodnota je false.


### <a name="tune-mappers"></a>Vyladění mapovačů

Počet mapovačů je řízen pomocí úpravy vlastnosti `pig.maxCombinedSplitSize`. Určuje množství dat ke zpracování úlohou jednu mapu. Výchozí hodnota je výchozí velikost bloku ze systému souborů. Zvýšení výsledkem hodnota ke snížení počtu úloh mapper.


### <a name="tune-reducers"></a>Vyladění reduktorů

Počet reduktorů se počítá na základě parametru `pig.exec.reducers.bytes.per.reducer`. Parametr určuje počet bajty zpracovaných za redukční funkci, ve výchozím nastavení 1 GB. Chcete-li omezit maximální počet reduktorů, nastavte `pig.exec.reducers.max` vlastnosti ve výchozím nastavení 999.


## <a name="hbase-optimization-with-the-ambari-web-ui"></a>Optimalizace HBase pomocí webového uživatelského rozhraní Ambari

Konfigurace HBase se liší od **HBase Configs** kartu. Následující části popisují některé důležité konfigurační nastavení, které ovlivňují výkon HBase.

### <a name="set-hbaseheapsize"></a>Nastavte HBASE_HEAPSIZE

Velikost haldy HBase určuje maximální velikost haldy pro použití v megabajtech podle *oblasti* a *hlavní* servery. Výchozí hodnota je 1 000 MB. To by měly být vyladěné pro zatížení clusteru.

1. Pokud chcete upravit, přejděte na **pokročilé HBase-env** podokně HBase **Configs** kartu a pak vyhledejte `HBASE_HEAPSIZE` nastavení.

1. Změňte výchozí hodnotu na 5 000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Optimalizace úlohy náročné na čtení

Následující konfigurace jsou důležité pro zlepšení výkonu úlohy náročné na čtení.

#### <a name="block-cache-size"></a>Velikost mezipaměti bloku

Mezipaměti bloku, která je mezipaměti pro čtení. Řídí jeho velikost `hfile.block.cache.size` parametru. Výchozí hodnota je 0.4, což je 40 procent celkové oblasti paměti serveru. Čím větší blok velikost mezipaměti, tím rychleji náhodné čtení bude.

1. Chcete-li změnit tento parametr, přejděte na **nastavení** kartu HBase **Configs** kartu a potom vyhledejte **% RegionServer přidělené vyrovnávací paměti pro čtení**.

    ![Velikost mezipaměti bloku HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
1. Chcete-li změnit hodnotu, vyberte **upravit** ikonu.


#### <a name="memstore-size"></a>Velikost paměťového úložiště

Všechny změny se ukládají do vyrovnávací paměti, volá se *paměťového úložiště*. Tím se zvyšuje celkové množství dat, který může být zapsaný na disk v rámci jedné operace a jeho všechny další přístupy urychluje na poslední úpravy. Velikost paměťového úložiště je definován následující dva parametry:

* `hbase.regionserver.global.memstore.UpperLimit`: Definuje maximální procento oblastním serveru, který můžete použít kombinaci paměťového úložiště.

* `hbase.regionserver.global.memstore.LowerLimit`: Definuje minimální procento oblastním serveru, který můžete použít kombinaci paměťového úložiště.

Optimalizovat pro náhodné čtení, můžete snížit paměťového úložiště horní a dolní limity.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Počet řádků, které jsou vyvolány při kontrole z disku

`hbase.client.scanner.caching` Nastavení definuje počet řádků, čtení z disku, když `next` metoda je volána na skeneru.  Výchozí hodnota je 100. Vyšší číslo, tím menší počet Vzdálená volání provedené od klienta na oblastním serveru, což vede k prohledávání rychlejší. Ale to se zvýší tlaku na paměť na straně klienta.

![HBase počet načtených řádků](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]
> Nenastavujte hodnotu tak, že doba mezi volání další metody na skeneru je delší než časový limit skeneru. Skener trvání časového limitu je definován `hbase.regionserver.lease.period` vlastnost.


### <a name="optimize-write-heavy-workloads"></a>Optimalizace úlohy náročné na zápis

Následující konfigurace jsou důležité pro zlepšení výkonu úlohy náročné na zápis.


#### <a name="maximum-region-file-size"></a>Oblast maximální velikost souboru

HBase ukládá data v interním formátu volá *hfile –*. Vlastnost `hbase.hregion.max.filesize` definuje velikost jednoho hfile – pro oblast.  Oblast je rozdělený do dvou oblastí, pokud součet všech HFiles v oblasti je větší než toto nastavení.
 
![Maximální velikost souboru HBase HRegion](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Čím větší je velikost souboru oblast, čím menší počet rozdělení. Můžete zvýšit velikost souboru k určení hodnoty, že výsledkem maximální zápis výkonu.


#### <a name="avoid-update-blocking"></a>Předcházet zablokování aktualizace

* Vlastnost `hbase.hregion.memstore.flush.size` definuje velikost, ve kterém se vyprázdní paměťového úložiště na disk. Výchozí velikost je 128 MB.

* Násobitel bloku oblasti Hbase je definován `hbase.hregion.memstore.block.multiplier`. Výchozí hodnota je 4. Maximální povolený počet je 8.

* Pokud paměťového úložiště je blokuje HBase aktualizace (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bajtů.

    S výchozími hodnotami vyprázdnění velikosti a blok násobitel jsou blokovány aktualizace při paměťového úložiště je 128 * 4 = 512 MB. Ke snížení aktualizace blokování počet, zvyšte hodnotu `hbase.hregion.memstore.block.multiplier`.

![Násobitel bloku oblasti HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Definovat metody velikost

Velikost paměťového úložiště je definován `hbase.regionserver.global.memstore.UpperLimit` a `hbase.regionserver.global.memstore.LowerLimit` parametry. Nastavení tyto hodnoty stejné, které pro každý další snižuje pozastavení během zapíše (také způsobí častější vyprazdňování) a zápis vyšší výkon.


### <a name="set-memstore-local-allocation-buffer"></a>Sada paměťového úložiště místního přidělení vyrovnávací paměti

Využití vyrovnávací paměti místního přidělení paměťového úložiště je určen vlastností `hbase.hregion.memstore.mslab.enabled`. Když je povoleno (pravda), to fragmentaci haldy během operace zápisu náročné. Výchozí hodnota je true.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>Další postup

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md)
* [Rozhraní Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
