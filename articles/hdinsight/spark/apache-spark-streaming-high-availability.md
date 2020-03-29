---
title: Vysoce dostupné úlohy streamování Spark v YARN - Azure HDInsight
description: Jak nastavit Apache Spark Streaming pro scénář s vysokou dostupností v Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: ac51b77e1ffc2b476b0a73dac9b6917552a86ce4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807149"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Vytvářejte vysoce dostupnost úloh apache spark streaming s YARN

[Apache Jiskra](https://spark.apache.org/) Streamování umožňuje implementovat škálovatelné aplikace s vysokou propustností a odolností proti chybám pro zpracování datových proudů. Aplikace streamování Spark v clusteru HDInsight Spark můžete připojit k různým druhům zdrojů dat, jako jsou Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), raw TCP sockets nebo sledováním souborů [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) pro změny. Spark Streaming podporuje odolnost proti chybám se zárukou, že každá daná událost je zpracována přesně jednou, a to i při selhání uzlu.

Spark Streaming vytváří dlouhotrvající úlohy, během kterých můžete použít transformace na data a pak tlačit výsledky do souborových systémů, databází, řídicích panelů a konzoly. Spark Streaming zpracovává mikrodávky dat tím, že nejprve shromažďuje dávku událostí v definovaném časovém intervalu. Dále je tato dávka odeslána ke zpracování a výstupu. Časové intervaly dávky jsou obvykle definovány ve zlomcích sekundy.

![Streamování jiskry](./media/apache-spark-streaming-high-availability/apache-spark-streaming.png)

## <a name="dstreams"></a>DStreamy

Spark Streaming představuje nepřetržitý proud dat pomocí *diskretized datového proudu* (DStream). Tento DStream lze vytvořit ze vstupních zdrojů, jako je Event Hubs nebo Kafka, nebo použitím transformace na jiném DStream. Když událost dorazí do aplikace Spark Streaming, událost je uložena spolehlivým způsobem. To znamená, že data události je replikována tak, aby více uzlů mít kopii. Tím je zajištěno, že selhání libovolného uzlu nebude mít za následek ztrátu události.

Jádro Spark používá *odolné distribuované datové sady* (RDD). RdD distribuují data mezi více uzly v clusteru, kde každý uzel obecně udržuje data zcela v paměti pro nejlepší výkon. Každý RDD představuje události shromážděné v průběhu dávkového intervalu. Po uplynutí dávkového intervalu spark streaming vytvoří nový RDD obsahující všechna data v tomto intervalu. Tato souvislá sada RDDs je shromažďována do DStream. Aplikace Spark Streaming zpracovává data uložená v rdd každé dávky.

![Jiskra Dstream](./media/apache-spark-streaming-high-availability/apache-spark-dstream.png)

## <a name="spark-structured-streaming-jobs"></a>Úlohy strukturovaného streamování spark

Strukturované streamování Spark bylo zavedeno ve Spark 2.0 jako analytický modul pro použití ve strukturovaných datech pro streamování. Spark Structured Streaming používá sparksql dávkování motoru API. Stejně jako u streamování Spark, Spark Structured Streaming spouští své výpočty přes neustále příchozí mikrodávky dat. Strukturované streamování Spark představuje datový proud jako vstupní tabulku s neomezeným počtem řádků. To znamená vstupní tabulka nadále roste jako nová data dorazí. Tato vstupní tabulka je průběžně zpracovávána dlouhotrvajícím dotazem a výsledky jsou zapsány do výstupní tabulky.

![Strukturované streamování jiskry](./media/apache-spark-streaming-high-availability/structured-streaming.png)

Ve strukturovaném streamování data dorazí do systému a je okamžitě ingestována do vstupní tabulky. Psát dotazy, které provádějí operace proti této vstupní tabulky. Výstup dotazu poskytuje další tabulku nazvanou Tabulka výsledků. Tabulka výsledků obsahuje výsledky dotazu, ze kterého nakreslíte data k odeslání do externího úložiště dat, jako je relační databáze. *Interval aktivační události* nastavuje časování při zpracování dat ze vstupní tabulky. Ve výchozím nastavení strukturované streamování zpracuje data ihned po jejich doručení. Můžete však také nakonfigurovat aktivační událost pro spuštění v delším intervalu, takže data streamování jsou zpracována v dávkách založených na čase. Data v tabulce výsledků mohou být aktualizována pokaždé, když jsou nová data, takže obsahují všechna výstupní data od začátku*dotazu*streamování ( úplný režim ), nebo mohou obsahovat pouze data, která jsou nová od posledního zpracování*dotazu*( režim připojení ).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Vytvářejte úlohy streamování Spark odolné proti chybám

Chcete-li vytvořit vysoce dostupné prostředí pro úlohy streamování Spark, začněte kódováním jednotlivých úloh pro obnovení v případě selhání. Taková samozotavující se úloha je odolná proti chybám.

RdD mají několik vlastností, které pomáhají vysoce dostupným úlohám spark streamingu odolným proti chybám:

* Dávky vstupních dat uložených v RDD jako DStream jsou automaticky replikovány v paměti pro odolnost proti chybám.
* Data ztracená z důvodu selhání pracovního procesu lze přepočítat z replikovaných vstupních dat na různých pracovnících, pokud jsou k dispozici tyto pracovní uzly.
* Rychlé zotavení po chybě může dojít během jedné sekundy, jako zotavení z chyb /opozdilce se stane prostřednictvím výpočtu v paměti.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Přesně jednou sémantika s Streamování spark

Chcete-li vytvořit aplikaci, která zpracovává každou událost jednou (a pouze jednou), zvažte, jak se po problému restartují všechny systémové body selhání a jak se můžete vyhnout ztrátě dat. Přesně jednou sémantiku vyžadovat, aby žádná data ztracena v libovolném bodě a že zpracování zpráv je restartovatelný, bez ohledu na to, kde dojde k selhání. Viz [Vytvoření úloh streamování Spark s přesně jednou zpracování událostí](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark Streaming a Apache Hadoop YARN

V HDInsight, clusterpráce je koordinována *ještě další zdroj vyjednavač* (YARN). Navrhování vysoké dostupnosti pro streamování Spark zahrnuje techniky pro streamování Spark a také pro komponenty YARN.  Příklad konfigurace pomocí YARN je uveden níže.

![Architektura YARN](./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png)

Následující části popisují aspekty návrhu pro tuto konfiguraci.

### <a name="plan-for-failures"></a>Plán selhání

Chcete-li vytvořit konfiguraci YARN pro vysokou dostupnost, měli byste naplánovat možné selhání vykonavatele nebo ovladače. Některé úlohy streamování Spark také zahrnují požadavky na záruku dat, které vyžadují další konfiguraci a nastavení. Například aplikace streamování může mít obchodní požadavek na záruku nulové ztráty dat navzdory jakékoli chybě, ke které dochází v hostitelském streamovacím systému nebo clusteru HDInsight.

Pokud **vykonavatel** selže, jeho úlohy a přijímače jsou automaticky restartovány Spark, takže není nutná žádná změna konfigurace.

Pokud se však **ovladač** nezdaří, všechny přidružené prováděcí moduly se nezdaří a všechny přijaté bloky a výsledky výpočtu jsou ztraceny. Chcete-li se zotavit z selhání ovladače, použijte *kontrolní body DStream,* jak je popsáno v [dokumentu Create Spark Streaming s přesně jednou zpracování událostí](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). Kontrolní body DStream pravidelně ukládá *směrovaný acyklický graf* (DAG) DStreams do úložiště odolného proti chybám, jako je Azure Storage.  Vytváření kontrolních bodů umožňuje strukturované streamování spark restartovat neúspěšný ovladač z informací kontrolního bodu.  Tento ovladač restartuje nové vykonavatele a také restartuje přijímače.

Obnovení ovladačů pomocí kontrolních bodů DStream:

* Nakonfigurujte automatické restartování ovladače `yarn.resourcemanager.am.max-attempts`v aplikaci YARN pomocí nastavení konfigurace .
* Nastavte adresář kontrolního bodu v systému `streamingContext.checkpoint(hdfsDirectory)`souborů kompatibilním s rozhraním HDFS s aplikací .
* Restrukturalizovat zdrojový kód pro použití kontrolních bodů pro obnovení, například:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Nakonfigurujte ztracené obnovení dat povolením `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`protokolu wal (write-ahead) s aplikací `StorageLevel.MEMORY_AND_DISK_SER`a zakažte replikaci v paměti pro vstupní přenosy dat s aplikací .

Chcete-li shrnout, pomocí checkpointing + WAL + spolehlivé přijímače, budete moci dodat "alespoň jednou" obnovu dat:

* Přesně jednou, pokud se neztratí přijatá data a výstupy jsou idempotentní nebo transakční.
* Přesně jednou, s novým přístupem Kafka Direct, který používá Kafka jako replikovaný protokol, spíše než pomocí přijímačů nebo WALs.

### <a name="typical-concerns-for-high-availability"></a>Typické obavy z vysoké dostupnosti

* Je obtížnější sledovat úlohy streamování než dávkové úlohy. Spark Streaming úlohy jsou obvykle dlouhotrvající a YARN není agregovat protokoly, dokud úloha nedokončí.  Kontrolní body Spark se ztratí během upgradu aplikace nebo Spark a během upgradu budete muset vymazat adresář kontrolního bodu.

* Nakonfigurujte režim clusteru YARN tak, aby spouštěl ovladače i v případě, že klient selže. Nastavení automatického restartování ovladačů:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark a Spark Streaming UI mají konfigurovatelný systém metrik. Můžete také použít další knihovny, jako je Graphite/Grafana ke stažení metriky řídicího panelu, jako je například 'num záznamy zpracovány', 'paměť/GC využití na ovladače & vykonavatele', 'celkové zpoždění', 'využití clusteru', a tak dále. Ve strukturovaném streamování verze 2.1 `StreamingQueryListener` nebo vyšší můžete získat další metriky.

* Měli byste segmentace dlouhotrvající úlohy.  Při odeslání aplikace Spark Streaming do clusteru musí být definována fronta YARN, ve které je úloha spuštěna. [Plánovač kapacity YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) můžete použít k odeslání dlouhotrvajících úloh do samostatných front.

* Řádně vypněte aplikaci pro streamování. Pokud jsou známy vaše posuny a všechny stavy aplikace jsou uloženy externě, pak můžete programově zastavit aplikaci streamování na příslušném místě. Jednou z technik je použití "závit háčky" v Spark, kontrolou externí příznak *každých n* sekund. Můžete také použít *soubor značky,* který je vytvořen na HDFS při spuštění aplikace, pak odebrán, když chcete zastavit. Pro přístup k souboru značky použijte v aplikaci Spark samostatné vlákno, které volá kód podobný tomuto:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Další kroky

* [Přehled streamování Apache Spark](apache-spark-streaming-overview.md)
* [Vytvářejte úlohy Apache Spark Streaming s právě jednou zpracováním událostí](apache-spark-streaming-exactly-once.md)
* [Dlouhotrvající apache spark streamingové úlohy na YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Strukturované streamování: Sémantiku odolnou proti chybám](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Diskretizované streamy: Model odolný proti chybám pro škálovatelné zpracování datových proudů](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
