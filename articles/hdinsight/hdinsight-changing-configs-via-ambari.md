---
title: Optimalizace konfigurace clusteru pomocí Ambari - Azure HDInsight | Microsoft Docs
description: Použijte webovému uživatelskému rozhraní Ambari ke konfiguraci a optimalizovat clusterů HDInsight.
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ashish
ms.openlocfilehash: f3c1edc767ab07bcdd8b09a0e40e291cbd1f3d9a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406178"
---
# <a name="use-ambari-to-optimize-hdinsight-cluster-configurations"></a>Optimalizace konfigurace clusteru HDInsight pomocí Ambari

HDInsight poskytuje clusterů systému Apache Hadoop pro zpracování velkých dat aplikace. Správa, monitorování a optimalizace tyto komplexní clustery s několika uzly může být náročné. [Apache Ambari](http://ambari.apache.org/) je webové rozhraní pro správu a sledování clusterů HDInsight Linux.  Pro clustery s Windows, použijte Ambari [REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

Úvod do používání webové uživatelské rozhraní Ambari, najdete v části [Správa clusterů HDInsight pomocí Ambari webového uživatelského rozhraní](hdinsight-hadoop-manage-ambari.md)

Přihlaste se k Ambari v `https://CLUSTERNAME.azurehdidnsight.net` pomocí svých přihlašovacích údajů clusteru. Na úvodní obrazovce zobrazí řídicí panel Přehled.

![Řídicí panel Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Webovému uživatelskému rozhraní Ambari slouží ke správě hostitelů, služby, výstrahy, konfigurace a zobrazení. Ambari nelze použít k vytvoření clusteru HDInsight, služby upgradu, spravovat zásobníky a verze, vyřadit z provozu nebo recommission hostitelů nebo přidání služeb do clusteru.

## <a name="manage-your-clusters-configuration"></a>Spravovat konfiguraci vašeho clusteru

Nastavení konfigurace pomáhají ladit konkrétní službu. K úpravě nastavení konfigurace služby, vyberte službu z **služby** bočním panelu (vlevo) a potom přejděte na **konfigurací** na stránce podrobností služby kartu.

![Bočním panelu služby](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Změnit velikost haldy NameNode Java

Velikost haldy NameNode Java závislá na mnoha faktorech, jako je například zatížení clusteru, počet souborů a čísla bloků. Výchozí velikost 1 GB dobře funguje s Většina clusterů, i když některé úlohy mohou vyžadovat více nebo méně paměti. 

Chcete-li změnit velikost haldy NameNode Java:

1. Vyberte **HDFS** z na bočním panelu služby a přejděte do **konfigurací** kartě.

    ![HDFS konfigurace](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

2. Najít nastavení **velikost haldy NameNode Java**. Můžete také **filtru** textového pole zadejte a najít konkrétní nastavení. Vyberte **pera** Ikona vedle názvu nastavení.

    ![Velikost haldy NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

3. Zadejte novou hodnotu do textového pole a stiskněte klávesu **Enter** uložte tuto změnu.

    ![Upravit velikost haldy NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

4. Velikost haldy NameNode Java se změní na 2 GB z 1 GB.

    ![Upravit velikost haldy NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

5. Uložte změny kliknutím na zelené **Uložit** tlačítko nahoře na obrazovce konfigurace.

    ![Uložit změny](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="hive-optimization"></a>Optimalizace Hive

Následující části popisují možnosti konfigurace pro optimalizaci celkový výkon Hive.

1. Chcete-li upravit parametry konfigurace Hive, vyberte **Hive** z na bočním panelu služby.
2. Přejděte na **konfigurací** kartě.

### <a name="set-the-hive-execution-engine"></a>Nastavit spouštěcí modul Hive

Hive poskytuje dva provádění stroje: MapReduce a Tez. Tez je rychlejší než MapReduce. Clustery HDInsight Linux mají Tez jako výchozí spuštění modul. Chcete-li změnit modul provádění:

1. V podregistru **konfigurací** , zadejte **modul pro spuštění** do pole Filtr.

    ![Spouštěcí modul vyhledávání](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

2. **Optimalizace** vlastnosti výchozí hodnota je **Tez**.

    ![Optimalizace - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Vyladění mappers

Hadoop pokusí rozdělení (*mapy*) jednoho souboru do více souborů a proces výsledná soubory paralelně. Počet mappers závisí na počtu rozdělení. Následující dvě konfigurační parametry jednotky počet rozdělení pro modulu Tez:

* `tez.grouping.min-size`: Nižší limit velikosti seskupené rozdělení, výchozí hodnotou 16 MB (16,777,216 bajtů).
* `tez.grouping.max-size`: Horní limit velikosti seskupené rozdělení, výchozí hodnotou 1 GB (1 073 741 824 bajtů).

Jako existuje výkonu pravidlo snížit oba tyto parametry zlepšit latenci, zvýšit pro větší propustnost.

Například čtyři mapper úlohy pro velikost dat 128 MB, můžete by nastavit oba parametry na 32 MB každý (33,554,432 bajtů).

1. Chcete-li upravit limit parametry, přejděte na **konfigurací** karta služby Tez. Rozbalte **Obecné** panelu a najděte `tez.grouping.max-size` a `tez.grouping.min-size` parametry.

2. Nastavte oba parametry na **33,554,432** bajtů (32 MB).

    ![Velikosti seskupení tez](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Tyto změny ovlivní všechny úlohy Tez přes server. Chcete-li získat výsledek optimální, zvolte odpovídající parametr hodnoty.

### <a name="tune-reducers"></a>Vyladění přechodky

ORC a Snappy nabízí vysoký výkon. Ale Hive může mít příliš málo přechodky ve výchozím nastavení, způsobuje kritická místa.

Řekněme například, že máte velikost vstupních dat 50 GB. Že formát dat v ORC s Tenhle komprese je 1 GB. Odhadne počet přechodky potřeby jako Hive: (počet bajtů vstup mappers / `hive.exec.reducers.bytes.per.reducer`).

Ve výchozím nastavení je tento příklad 4 přechodky.

`hive.exec.reducers.bytes.per.reducer` Parametr určuje počet bajtů zpracovaných za reduktorem. Výchozí hodnota je 64 MB. Ladění tuto hodnotu dolů zvyšuje stupně paralelního zpracování a může zvýšit výkon. Ladění příliš nízká může také vytvořit příliš mnoho přechodky, potenciálně nepříznivě ovlivňuje výkon. Tento parametr je na základě požadavků na konkrétní dat, nastavení komprese a dalších faktorech prostředí.

1. Chcete-li upravit parametr, přejděte na podregistr **konfigurací** vyhledat **dat za reduktorem** parametr na stránce nastavení.

    ![Data za reduktorem](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
2. Vyberte **upravit** změňte hodnotu na 128 MB (134,217,728 bajtů), a potom stiskněte klávesu **Enter** uložit.

    ![Data za reduktorem - upravit](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Zadaný vstupní množství 1 024 MB, 128 MB dat za reduktorem, existují 8 přechodky (1024/128).

3. Nesprávná hodnota pro parametr **dat za reduktorem** parametr může mít za následek velký počet přechodky, nepříznivě ovlivňuje výkon dotazů. Chcete-li omezit maximální počet přechodky, nastavte `hive.exec.reducers.max` na odpovídající hodnotu. Výchozí hodnota je 1009.

### <a name="enable-parallel-execution"></a>Povolit paralelní provádění

Dotaz Hive spouští v jedné nebo více fázích. Pokud nezávislé fázích může běžet paralelně, který se zvyšuje výkon dotazů.

1.  Chcete-li povolit provádění paralelního dotazu, přejděte na podregistr **konfigurace** kartě a vyhledejte `hive.exec.parallel` vlastnost. Výchozí hodnota je false. Změňte hodnotu na hodnotu true a potom stiskněte klávesu **Enter** uložte hodnotu.
 
2.  Chcete-li omezit počet úloh spustit souběžně, změňte `hive.exec.parallel.thread.number` vlastnost. Výchozí hodnota je 8.

    ![Hive paralelní exec](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Povolit vectorization

Hive zpracovává data po řádcích. Vectorization přesměruje Hive pro zpracování dat v blocích po 1 024 řádků než jeden řádek v čase. Vectorization platí pouze pro formát souboru ORC.

1. Chcete-li povolit spuštění vectorized dotazu, přejděte na podregistr **konfigurací** kartě a vyhledejte `hive.vectorized.execution.enabled` parametr. Výchozí hodnota je true pro Hive 0.13.0 nebo novější.
 
2. Chcete-li povolit vectorized spuštění na straně snižte dotazu, nastavte `hive.vectorized.execution.reduce.enabled` parametr na hodnotu true. Výchozí hodnota je false.

    ![Ke spouštění Hive vectorized](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Povolit optimalizace na základě nákladů (CBO)

Ve výchozím nastavení následuje Hive sadu pravidel najít jeden plán spuštění optimální dotazu. Optimalizace na základě nákladů (CBO) vyhodnotí více schématy při spuštění dotazu a přiřadí s náklady na každý plán, pak určuje nejlevnější plán při spuštění dotazu.

Chcete-li povolit CBO, přejděte na podregistr **konfigurací** kartě a vyhledejte `parameter hive.cbo.enable`, pak přejděte na přepínací tlačítko **na**.

![Konfigurace CBO](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Následující další konfigurační parametry zvýšit výkon dotazů Hive, pokud je povoleno CBO:

* `hive.compute.query.using.stats`

    Když nastaven na hodnotu true, Hive využívá statistiky, které jsou uložené v jeho metaúložiště k odpovědi na jednoduché dotazy jako `count(*)`.

    ![CBO statistiky](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Statistiky sloupce vytvářejí, když je povoleno CBO. Hive používá statistiky sloupce, které jsou uložené v metaúložiště, optimalizovat dotazy. Načítání statistiky sloupce pro každý sloupec trvá déle, pokud je vysoký počet sloupců. Pokud nastavíte na hodnotu false, toto nastavení zakáže načítání statistiky sloupce z metaúložiště.

    ![Hive statistiky sloupce set statistiky](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Oddíl základní statistické údaje, třeba počet řádků, velikost dat a velikost souboru jsou uloženy v metaúložiště. Pokud nastavíte na hodnotu true, oddíl, který statistiky jsou načtena z metaúložiště. Pokud má hodnotu NEPRAVDA, velikost souboru je načtených ze systému souborů a počet řádků, je načtena z řádků schématu.

    ![Hive statistiky oddílu set statistiky](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Povolit kompresi zprostředkující

Mapa úlohy vytvoření zprostředkující soubory, které jsou používány reduktorem úlohy. Zprostředkující komprese zmenší velikost pomocný soubor.

Úloh Hadoop jsou obvykle omezené vstupně-výstupní operace. Komprese dat urychlit vstupně-výstupních operací a celkové síťové přenosy.

K dispozici komprese typy jsou:

| Formát | Nástroj | Algoritmus | Přípona souboru | Rozdělitelné? |
| -- | -- | -- | -- | -- |
| GZIP | GZIP | DEFLATE | .gz | Ne |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Ano |
| LZO | Lzop | LZO | .lzo | Ano, pokud indexované |
| Tenhle | neuvedeno | Tenhle | Tenhle | Ne |

Obecně platí s metodu komprese rozdělitelné je důležité, v opačném případě bude vytvořen jen několik mappers. Pokud vstupní data je text, `bzip2` je nejlepší možnost. Pro formát ORC Snappy je nejrychlejší možnost komprese.

1. Chcete-li povolit kompresi zprostředkující, přejděte na podregistr **konfigurací** kartě a poté nastavte `hive.exec.compress.intermediate` parametr na hodnotu true. Výchozí hodnota je false.

    ![Komprimovat exec Hive zprostředkující](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]
    > Chcete-li komprimovat zprostředkující soubory, zvolte kompresní kodek s nižší procesoru náklady, i v případě, že kodek nemá vysokou komprese výstup.

2. Pokud chcete nastavit zprostředkující kompresní kodek, přidejte vlastní vlastnost `mapred.map.output.compression.codec` k `hive-site.xml` nebo `mapred-site.xml` souboru.

3. Přidání vlastního nastavení:

    a. Přejděte do podregistr **konfigurací** a vyberte **Upřesnit** kartě.

    b. V části **Upřesnit** kartě, vyhledejte a rozbalte **vlastní hive-site** podokně.

    c. Klikněte na odkaz **přidat vlastnost** v dolní části podokna vlastní hive-site.

    d. V okně Přidat vlastnost zadejte `mapred.map.output.compression.codec` jako klíč a `org.apache.hadoop.io.compress.SnappyCodec` jako hodnotu.

    e. Klikněte na tlačítko **Add** (Přidat).

    ![Vlastní vlastnost Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    To bude komprimovat pomocný soubor pomocí Tenhle komprese. Po přidání vlastnosti se zobrazí v podokně vlastní hive-site.

    > [!NOTE]
    > Tento postup změní `$HADOOP_HOME/conf/hive-site.xml` souboru.

### <a name="compress-final-output"></a>Komprimovat závěrečný výstup

Finální výstup Hive můžete také zkomprimovat.

1. Pokud chcete komprimovat finální výstup Hive, přejděte na podregistr **konfigurací** kartě a poté nastavte `hive.exec.compress.output` parametr na hodnotu true. Výchozí hodnota je false.

2. Zvolte kompresní kodek výstup, přidejte `mapred.output.compression.codec` vlastní vlastnosti do podokna hive lokality vlastní, jak je popsáno v předchozí části kroku 3.

    ![Vlastní vlastnost Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Povolit spekulativní provádění

Spekulativní provádění spouští počet duplicitní úlohy, aby bylo možné zjistit a blokovaných sledovací modul zpomalit spuštění úloh, při současném zvyšování celkové provádění úlohy pomocí optimalizace výsledky jednotlivých úloh.

Spekulativní spuštění by neměl být zapnut pro dlouhotrvající úlohy MapReduce s velkým množstvím vstup.

* Chcete-li povolit spekulativní provádění, přejděte na podregistr **konfigurací** kartě a poté nastavte `hive.mapred.reduce.tasks.speculative.execution` parametr na hodnotu true. Výchozí hodnota je false.

    ![Hive mapred snížit spekulativní spuštění úlohy](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Vyladění dynamických oddílů

Hive umožňuje vytváření dynamických oddílů po vložení záznamu do tabulky, bez předběžné definování každého oddílu. Toto je výkonné funkce, i když může vést k vytvoření velkého počtu oddílů a velký počet souborů pro každý oddíl.

1. Pro Hive udělat dynamických oddílů `hive.exec.dynamic.partition` hodnota parametru musí být true (výchozí).

2. Změnit režim dynamické oddílu na *striktní*. Alespoň jeden oddíl v striktní režim musí být statická. Zabrání se tak dotazů bez filtru oddílu v klauzuli WHERE, který je *striktní* brání dotazy, které kontrolovat všechny oddíly. Přejděte do podregistr **konfigurací** kartě a poté nastavte `hive.exec.dynamic.partition.mode` k **striktní**. Výchozí hodnota je **nonstrict**.
 
3. Pokud chcete omezit počet dynamických oddílů, který se má vytvořit, upravit "hive.exec.max.dynamic.partitions' parametr. Výchozí hodnota je 5 000.
 
4. Chcete-li omezit celkový počet dynamických oddílů na jeden uzel, změňte `hive.exec.max.dynamic.partitions.pernode`. Výchozí hodnota je 2000.

### <a name="enable-local-mode"></a>Povolit místní režim

Místní režim umožňuje Hive k plnění všech úloh úlohy na jednom počítači nebo někdy v jediném procesu. To zlepšuje výkon dotazů, pokud vstupní data je malá a nároky na spuštění úlohy pro dotazy odebírá významného procenta celkového spuštění dotazu.

Chcete-li povolit místní režim, přidejte `hive.exec.mode.local.auto` parametr panely vlastní hive-site, jak je popsáno v kroku 3 [povolit kompresi zprostředkující](#enable-intermediate-compression) části.

![Hive exec režimu místní automaticky](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Sada jeden MapReduce MultiGROUP podle

Pokud je tato vlastnost nastavena na hodnotu true, dotaz MultiGROUP podle s společné Seskupit podle klíče vygeneruje jednu úlohu MapReduce.  

Chcete-li toto chování, přidejte `hive.multigroupby.singlereducer` parametr do podokna hive lokality vlastní, jak je popsáno v kroku 3 [povolit kompresi zprostředkující](#enable-intermediate-compression) části.

![Hive, která nastavuje jednu MapReduce MultiGROUP](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Další optimalizace Hive

Následující části popisují další optimalizace související Hive, můžete nastavit.

#### <a name="join-optimizations"></a>Připojení k optimalizace

Je výchozím typem spojení v Hive *náhodně spojení*. V Hive speciální mappers číst vstupní a emitování dvojici klíč/hodnota spojení do pomocný soubor. Hadoop seřadí a sloučí těmto párům ve fázi náhodně. Tato fáze náhodně je nákladné. Výběr správné spojení založené na vašich dat může výrazně zlepšit výkon.

| Typ připojení | Kdy | Postupy | Nastavení Hive | Komentáře |
| -- | -- | -- | -- | -- |
| Náhodný výběr spojení | <ul><li>Výchozí volba</li><li>Vždy funguje</li></ul> | <ul><li>Čte z jedné z tabulek součástí</li><li>Kbelíků a řazení na klíč připojení</li><li>Odešle jedné sady jednotlivých reduce</li><li>Spojení se provádí na straně snižte</li></ul> | Žádné významné Hive nastavení potřebné | Pokaždé, když funguje |
| Mapy připojení | <ul><li>Vejde na jednu tabulku v paměti</li></ul> | <ul><li>Načte malé tabulky do paměti zatřiďovací tabulku</li><li>Datové proudy přes součástí velkých souborů</li><li>Spojí každý záznam z tabulky hash</li><li>Spojení jsou mapovačem samostatně</li></ul> | `hive.auto.confvert.join=true` | Velmi rychlé, ale omezená |
| Bucket sloučení řazení | Pokud jsou obě tabulky: <ul><li>Seřadit stejné</li><li>Bucketed stejné</li><li>Propojení na sloupci seřadit kategorizovaná</li></ul> | Každý proces: <ul><li>Čte blok z každé tabulky</li><li>Zpracuje řádek s nejnižší hodnotou</li></ul> | `hive.auto.convert.sortmerge.join=true` | Velmi efektivně |

#### <a name="execution-engine-optimizations"></a>Spuštění modulu optimalizace

Další doporučení pro optimalizaci modul provádění Hive:

| Nastavení | Doporučené | Výchozí HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | Hodnotu true = bezpečnější, pomalejší; false = rychlejší | false (nepravda) |
| `tez.am.resource.memory.mb` | Horní mez 4 GB pro většinu | Automaticky Laděná |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="pig-optimization"></a>Optimalizace pig

Pig vlastnosti můžete změnit z Ambari webového uživatelského rozhraní pro optimalizaci Pig dotazy. Úprava vlastností Pig z Ambari přímo upraví vlastnosti Pig v `/etc/pig/2.4.2.0-258.0/pig.properties` souboru.

1. Chcete-li upravit vlastnosti Pig, přejděte na Pig **konfigurací** kartě a potom rozbalte **rozšířené vlastnosti pig** podokně.

2. Najít, zrušte komentář u a změňte hodnotu vlastnosti, kterou chcete upravit.

3. Vyberte **Uložit** na pravé straně hlavní okno a uložte novou hodnotu. Některé vlastnosti může vyžadovat restartování služby.

    ![K rozšířeným vlastnostem pig](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]
> Nastavení úrovně relace přepsání hodnoty vlastností v `pig.properties` souboru.

### <a name="tune-execution-engine"></a>Vyladění modul pro spuštění

Dva provádění stroje jsou k dispozici pro spuštění skriptů Pig: MapReduce a Tez. Tez je modul optimalizované a je mnohem rychlejší než MapReduce.

1. K úpravě spouštěcí modul v **rozšířené vlastnosti pig** podokně najít vlastnost `exectype`.

2. Výchozí hodnota je **MapReduce**. Změňte ho na **Tez**.


### <a name="enable-local-mode"></a>Povolit místní režim

Podobá se Hive, místní režim se používá k urychlení úlohy s relativně menší množství dat.

1. Chcete-li povolit místní režim, nastavte `pig.auto.local.enabled` k **true**. Výchozí hodnota je false.

2. Úlohy s velikostí vstupní data méně než `pig.auto.local.input.maxbytes` hodnotu vlastnosti se považují za malé přenosy. Výchozí hodnota je 1 GB.


### <a name="copy-user-jar-cache"></a>Zkopírujte mezipaměti jar uživatele

Pig zkopíruje soubory JAR vyžadovanou UDF distribuované mezipaměti, aby byly dostupné pro uzly úloh. Tyto JAR často nemění. Pokud je povoleno, `pig.user.cache.enabled` nastavení umožňuje JAR umístit do mezipaměti na je znovu použijte pro úlohy spusťte stejným uživatelem. To vede k menší zvýšení výkonu úlohy.

1. Chcete-li povolit, nastavte `pig.user.cache.enabled` na hodnotu true. Výchozí hodnota je false.

2. Základní cesta uložené v mezipaměti JAR, nastavit `pig.user.cache.location` na základní cesta. Výchozí hodnota je `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Optimalizace výkonu pomocí nastavení paměti

Následující nastavení paměti může pomoci optimalizace výkonu skriptů Pig.

* `pig.cachedbag.memusage`: Množství paměti přidělené pro kontejner. Kontejner je kolekce řazené kolekce členů. Řazené kolekce členů je seřazený sadu pole a pole je úsek data. Pokud data v kontejner je nad rámec přidělenou paměť, je uniknout na disk. Výchozí hodnota je 0,2, která představuje 20 procent dostupné paměti. Tuto paměť je sdílet mezi všechny sáčků v aplikaci.

* `pig.spill.size.threshold`: Jsou uniknout sáčků větší než tato prahová hodnota velikosti přepadového (v bajtech) na disk. Výchozí hodnota je 5 MB.


### <a name="compress-temporary-files"></a>Komprimovat dočasné soubory

Pig generuje dočasných souborů během provádění úlohy. Komprimaci dočasných souborů výsledkem zvýšení výkonu při čtení nebo zápisu souborů na disk. Následující nastavení slouží ke komprimaci dočasných souborů.

* `pig.tmpfilecompression`: Pokud je nastavena hodnota true, povolí kompresi dočasný soubor. Výchozí hodnota je false.

* `pig.tmpfilecompression.codec`: Kodek komprese pro kompresi dočasné soubory. Doporučené komprese kodeky jsou LZO a Snappy pro nižší využití výkonu procesoru.

### <a name="enable-split-combining"></a>Povolit kombinování rozdělení

Když je povolené, malé soubory slučují pro méně úlohy mapy. To zvyšuje efektivitu úlohy s mnoho malých souborů. Chcete-li povolit, nastavte `pig.noSplitCombination` na hodnotu true. Výchozí hodnota je false.


### <a name="tune-mappers"></a>Vyladění mappers

Řídí počet mappers úpravou vlastnosti `pig.maxCombinedSplitSize`. Určuje velikost dat, které mají být zpracovány úlohou jeden mapy. Výchozí hodnota je výchozí velikost bloku na systém souborů. Zvýšení výsledkem hodnota ke snížení počtu úloh mapper.


### <a name="tune-reducers"></a>Vyladění přechodky

Počet přechodky se počítá na základě parametru `pig.exec.reducers.bytes.per.reducer`. Parametr určuje počet bajtů zpracovaných za reduktorem, ve výchozím nastavení 1 GB. Chcete-li omezit maximální počet přechodky, nastavte `pig.exec.reducers.max` vlastnost ve výchozím nastavení 999.


## <a name="hbase-optimization-with-the-ambari-web-ui"></a>Optimalizace HBase se webovému uživatelskému rozhraní Ambari

Konfigurace HBase se liší od **HBase konfigurací** kartě. Následující části popisují některé důležité konfigurační nastavení, které ovlivňují výkon HBase.

### <a name="set-hbaseheapsize"></a>Nastavit HBASE_HEAPSIZE

Velikost haldy HBase určuje maximální velikost haldy pro použití v megabajtech podle *oblast* a *hlavní* servery. Výchozí hodnota je 1 000 MB. To by měl být optimalizovaných pro zatížení clusteru.

1. Pokud chcete upravit, přejděte na **rozšířené HBase-env** podokno HBase **konfigurací** kartě a pak vyhledejte `HBASE_HEAPSIZE` nastavení.

2. Změňte výchozí hodnotu 5 000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Optimalizace pro čtení náročné úlohy

Následující konfigurace jsou důležité pro zlepšení výkonu pro čtení náročné úlohy.

#### <a name="block-cache-size"></a>Velikost mezipaměti bloku

Mezipaměti bloku, která je mezipaměti pro čtení. Jeho velikost se řídí `hfile.block.cache.size` parametr. Výchozí hodnota je 0.4, což je 40 procent celkové oblasti paměti serveru. Čím větší bloku velikost mezipaměti, bude rychlejší náhodné čtení.

1. Chcete-li tento parametr změnit, přejděte na **nastavení** ve HBase **konfigurací** kartě a vyhledejte **% RegionServer přidělit vyrovnávací paměť pro čtení**.

    ![Velikost mezipaměti bloku HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
2. Chcete-li změnit hodnotu, vyberte **upravit** ikonu.


#### <a name="memstore-size"></a>Velikost metody

Všechny úpravy jsou uložené ve vyrovnávací paměti, názvem *metody*. Tím se zvyšuje celkovou velikost dat, která můžete zapsat na disk v rámci jedné operace a jeho následné přístup urychlí na poslední úpravy. Velikost paměťového úložiště je definováno tyto dva parametry:

* `hbase.regionserver.global.memstore.UpperLimit`: Definuje maximální procento oblasti serveru, který můžete použít metody kombinaci.

* `hbase.regionserver.global.memstore.LowerLimit`: Definuje minimální procento oblasti serveru, který můžete použít metody kombinaci.

Za účelem optimalizace pro náhodné čtení, můžete snížit metody horní a dolní meze.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Počet řádků načtených při kontrole z disku

`hbase.client.scanner.caching` Nastavení definuje počet řádků čtení z disku, když `next` metoda je volána na skeneru.  Výchozí hodnota je 100. Tím vyšší číslo, tím menší počet Vzdálená volání provedeny z klienta na server oblast, výsledkem je rychlejší kontroly. Nicméně tím se zvýší přetížení paměti na straně klienta.

![HBase počet počet získaných řádků:](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]
> Nenastavujte hodnotu tak, aby čas mezi vyvolání další metody na skeneru je větší než je časový limit skener. Skener trvání časového limitu je definována `hbase.regionserver.lease.period` vlastnost.


### <a name="optimize-write-heavy-workloads"></a>Optimalizace zápisu náročné úlohy

Následující konfigurace jsou důležité pro zlepšení výkonu zápisu náročné úlohy.


#### <a name="maximum-region-file-size"></a>Oblast maximální velikost souboru

HBase ukládá data ve formátu souboru interní názvem *hfile –*. Vlastnost `hbase.hregion.max.filesize` definuje velikost jednoho hfile – pro oblast.  Oblast je rozdělit do dvou oblastí, pokud součet všech HFiles v oblasti je větší než toto nastavení.
 
![Maximální velikost souboru HBase HRegion](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Větší velikost souboru oblast, tím menší počet rozdělení. Můžete zvýšit velikost souboru k určení hodnoty, který má za následek maximální zápisu výkonu.


#### <a name="avoid-update-blocking"></a>Zabránění blokování aktualizace

* Vlastnost `hbase.hregion.memstore.flush.size` definuje velikost, kdy metody vyprazdňuje na disk. Výchozí velikost je 128 MB.

* Násobitel bloku oblast Hbase je definována `hbase.hregion.memstore.block.multiplier`. Výchozí hodnota je 4. Maximální povolený počet je 8.

* Pokud je metody blokuje HBase aktualizace (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bajtů.

    Aktualizace s výchozími hodnotami vyprázdnění velikost a násobitel bloku, jsou zablokovány, když paměťového úložiště je 128 * 4 = 512 MB. Pokud chcete zkrátit aktualizace blokování počet, zvýšit hodnotu `hbase.hregion.memstore.block.multiplier`.

![Násobitel bloku oblast HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Zadejte velikost metody

Velikost paměťového úložiště je definována `hbase.regionserver.global.memstore.UpperLimit` a `hbase.regionserver.global.memstore.LowerLimit` parametry. Nastavení těchto hodnot je rovno, které pro každý další snižuje pozastaví během zapíše (což také častější, abyste vyprázdnili) a má za následek vyšší zápisu.


### <a name="set-memstore-local-allocation-buffer"></a>Nastavit metody místní přidělení vyrovnávací paměti

Využití vyrovnávací paměti místní přidělení paměťového úložiště je určen vlastností `hbase.hregion.memstore.mslab.enabled`. Když povolené (pravda), tím zabráníte fragmentace haldy během operace zápisu těžká. Výchozí hodnota je true.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>Další postup

* [Správa clusterů HDInsight pomocí Ambari webového uživatelského rozhraní](hdinsight-hadoop-manage-ambari.md)
* [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
