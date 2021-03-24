---
title: Vysoce dostupné úlohy streamování Sparku v VLÁKNech – Azure HDInsight
description: Jak nastavit streamování Apache Spark pro scénář s vysokou dostupností ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 602fa1cab71a797dd25aca263e0c6a9f2aa616bb
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870224"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Vytváření úloh streamování s vysokou dostupností Apache Spark s využitím PŘÍZe

[Apache Spark](https://spark.apache.org/) Streamování umožňuje implementovat škálovatelné a vysoce odolné aplikace odolné proti chybám pro zpracování datových proudů. V clusteru HDInsight Spark můžete připojit aplikace pro streamování Sparku k různým druhům zdrojů dat, jako je Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), RAW TCP Sockets, nebo sledováním změn v systému souborů [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) . Streamování Spark podporuje odolnost proti chybám a zaručuje, že jakákoliv událost se zpracovává přesně jednou, a to i v případě selhání uzlu.

Streamování Spark vytvoří dlouhotrvající úlohy, během kterých můžete použít transformace na data a pak výsledky vložit do systému souborů, databází, řídicích panelů a konzoly. Streamování Sparku zpracovává mikrodávkování dat tím, že nejprve shromažďuje dávky událostí v rámci definovaného časového intervalu. V dalším kroku se tato dávka pošle na zpracování a výstup. Časové intervaly dávky jsou obvykle definovány ve zlomcích sekundy.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-streaming.png" alt-text="Streamování Sparku" border="false":::

## <a name="dstreams"></a>DStreams

Streamování Spark představuje souvislý datový proud dat pomocí *datového proudu diskretizovaný* (DStream). Tento DStream se dá vytvořit ze vstupních zdrojů, jako je Event Hubs nebo Kafka, nebo použitím transformací na jiném DStream. Když událost dorazí do vaší aplikace streamování Sparku, událost se uloží spolehlivě. To znamená, že jsou data události replikována, aby k nim bylo kopírováno více uzlů. Tím se zajistí, že selhání jakéhokoli jednoho uzlu nebude mít za následek ztrátu vaší události.

Spark Core používá *odolné distribuované datové sady* (RDD). RDD distribuuje data napříč několika uzly v clusteru, kde každý uzel obecně udržuje svá data v paměti, aby se dosáhlo co nejlepšího výkonu. Každý RDD představuje události shromážděné v intervalu dávky. Po uplynutí intervalu dávky vytvoří streamování Spark nové RDD obsahující všechna data v tomto intervalu. Tato nepřetržitá sada RDD je shromažďována do DStream. Aplikace pro streamování Spark zpracovává data uložená v RDD každé dávky.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-dstream.png" alt-text="Spark DStream" border="false":::

## <a name="spark-structured-streaming-jobs"></a>Strukturované úlohy streamování Sparku

Strukturované streamování Sparku bylo zavedeno v Spark 2,0 jako analytický modul pro použití při streamování strukturovaných dat. Strukturované streamování Sparku používá rozhraní API modulu Batch SparkSQL. Podobně jako u streamování Sparku používá strukturované streamování Spark své výpočty přes nepřetržité doručení mikrodávkám dat. Strukturované streamování Spark představuje datový proud dat v podobě vstupní tabulky s neomezenými řádky. To znamená, že vstupní tabulka se dál zvětšuje, protože dorazíte na nová data. Tato vstupní tabulka se průběžně zpracovává dlouho běžícím dotazem a výsledky se zapisují do výstupní tabulky.

:::image type="content" source="./media/apache-spark-streaming-high-availability/structured-streaming.png" alt-text="Strukturované streamování Sparku" border="false":::

Ve strukturovaném streamování přicházejí data do systému a okamžitě se ingestují do vstupní tabulky. Zapisujete dotazy, které provádějí operace s touto vstupní tabulkou. Výstup dotazu vydává jinou tabulku, která se nazývá tabulka výsledků. Tabulka výsledků obsahuje výsledky dotazu, ze kterého kreslíte data pro odeslání do externího úložiště dat, jako je relační databáze. *Interval triggeru* nastavuje časování pro zpracování dat ze vstupní tabulky. Ve výchozím nastavení strukturované streamování zpracovává data hned po doručení. Můžete ale také nakonfigurovat Trigger tak, aby běžel v delším intervalu, takže streamovaná data se zpracují v dávkách založených na čase. Data v tabulce výsledků se můžou aktualizovat pokaždé, když jsou nová data, takže budou zahrnovat všechna výstupní data od začátku dotazu na streamování (*režim* připojení), nebo může obsahovat jenom data, která jsou od posledního zpracování dotazu (*režim připojení*) nová.

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Vytváření úloh streamování Spark odolných proti chybám

Pokud chcete vytvořit vysoce dostupné prostředí pro úlohy streamování Sparku, začněte kódováním jednotlivých úloh pro obnovení v případě selhání. Tyto úlohy samoobslužného obnovování jsou odolné proti chybám.

RDD mají několik vlastností, které pomáhají vysokou dostupnost a úlohy streamování Sparku s odolností proti chybám:

* Dávky vstupních dat uložených v RDD jako DStream se automaticky replikují v paměti pro odolnost proti chybám.
* Data ztracená kvůli selhání pracovního procesu je možné přepočítat z replikovaných vstupních dat na různých pracovních procesech, pokud jsou tyto pracovní uzly k dispozici.
* K rychlému zotavení proti chybám může docházet během jedné sekundy, protože obnovení z chyb/stragglers probíhá prostřednictvím výpočtu v paměti.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Přesně po sémantikě pomocí Spark streamování

Chcete-li vytvořit aplikaci, která každou událost zpracuje jednou (a pouze jednou), zvažte, jak všechny systémové body po selhání restartují po problému a jak se můžete vyhnout ztrátě dat. Právě jednou sémantika vyžaduje, aby žádná data neztratila žádná data, a zpracování zprávy je znovu spuštěno, bez ohledu na to, kde k selhání dojde. Přečtěte si téma [Vytvoření úloh pro streamování Sparku s právě jedním zpracováním událostí](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Streamování Sparku a Apache Hadoop nitě

V HDInsight je práce clusteru koordinována *ještě jiným vyjednáváním prostředků* (příze). Návrh vysoké dostupnosti pro streamování Spark zahrnuje techniky pro streamování Sparku a také pro PŘÍZové komponenty.  Příklad konfigurace pomocí PŘÍZe je uveden níže.

:::image type="content" source="./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png" alt-text="Architektura PŘÍZe" border="false":::

Následující části popisují faktory návrhu této konfigurace.

### <a name="plan-for-failures"></a>Plánování selhání

Chcete-li vytvořit konfiguraci PŘÍZe pro vysokou dostupnost, měli byste naplánovat možného vykonavatele nebo selhání ovladače. Některé úlohy streamování Sparku zahrnují taky požadavky na záruku dat vyžadující dodatečnou konfiguraci a nastavení. Například aplikace streamování může mít obchodní požadavek na záruku ztráty dat s nulovou ztrátou, a to bez ohledu na chybu, ke které dochází v systému pro streamování nebo cluster HDInsight.

Pokud se **vykonavatel** nepovede, jeho úkoly a příjemci se automaticky restartují, takže se nepotřebuje žádná změna konfigurace.

Pokud ale **ovladač** selže, všechny jeho přidružené vykonavatele selžou a všechny přijaté bloky a výsledky výpočtů budou ztraceny. Chcete-li provést obnovení při selhání ovladače, použijte *kontrolní body DStream* , jak je popsáno v tématu [vytváření úloh Spark streamování s právě jedním zpracováním událostí](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). Kontrolní body DStream pravidelně ukládají *orientovaný acyklického graf* (DAG) DStreams do úložiště odolného proti chybám, jako je Azure Storage.  Vytváření kontrolních bodů umožňuje strukturované streamování Sparku restartovat ovladač, který selhal, z informací kontrolního bodu.  Po restartování tohoto ovladače se spustí nové prováděcí moduly a také se znovu spustí přijímače.

Postup obnovení ovladačů pomocí kontrolního bodu DStream:

* Nakonfigurujte automatické restartování ovladače u PŘÍZe s nastavením konfigurace `yarn.resourcemanager.am.max-attempts` .
* Nastavte adresář kontrolního bodu v systému souborů kompatibilním s HDFS pomocí `streamingContext.checkpoint(hdfsDirectory)` .
* Restrukturalizujte zdrojový kód, aby se pro obnovení používaly kontrolní body, například:

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

* Nakonfigurujte ztracené obnovení dat tak, že povolíte Protokol WAL (Write-to-před) s `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` a zakážete replikaci v paměti pro vstupní DStreams s `StorageLevel.MEMORY_AND_DISK_SER` .

Pokud chcete vytvořit souhrn pomocí kontrolního bodu a spolehlivých přijímačů + WAL +, budete moct doručovat "nejméně jednou" obnovení dat:

* Právě jednou, pokud se přijatá data neztratí a výstupy jsou buď idempotentní, nebo transakční.
* Právě jednou s novým přímým přístupem Kafka, který používá Kafka jako replikovaný protokol, namísto použití přijímačů nebo WALs.

### <a name="typical-concerns-for-high-availability"></a>Typické otázky pro vysokou dostupnost

* Je obtížnější monitorovat úlohy streamování než dávkové úlohy. Úlohy streamování Sparku jsou obvykle dlouhotrvající a PŘÍZe neagreguje protokoly, dokud se nedokončí úloha.  Během upgradu aplikace nebo Sparku dojde ke ztrátě kontrolních bodů Sparku a během upgradu budete muset vymazat adresář kontrolního bodu.

* Nakonfigurujte režim clusteru PŘÍZe tak, aby spouštěl ovladače i v případě, že dojde k chybě klienta. Nastavení automatického restartování pro ovladače:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Prostředí Spark a uživatelské rozhraní pro streamování Spark mají konfigurovatelný systém metrik. Můžete také použít další knihovny, například grafit/Grafana, ke stažení metrik řídicích panelů, jako je například "Počet zpracovaných záznamů", "paměť/GC na ovladače & vykonavatelé", "celkové zpoždění", "využití clusteru" a tak dále. Ve strukturovaném streamování verze 2,1 nebo vyšší můžete použít `StreamingQueryListener` k získání dalších metrik.

* Měli byste segmentovat dlouhotrvající úlohy.  Když se do clusteru odešle aplikace streamování Sparku, musí se definovat fronta PŘÍZe, kde je úloha spuštěná. Můžete použít [Plánovač kapacity příze](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) k odeslání dlouhotrvajících úloh do samostatných front.

* Řádně vypněte aplikaci streamování. Pokud jsou vaše posuny známé a veškerý stav aplikace je uložen externě, můžete aplikaci streamování zastavit na příslušném místě. Jednou z technik je použít "zavěšení vláken" ve Sparku, a to tak, že každých *n* sekund zkontrolujete externí příznak. Můžete také použít *soubor značky* , který je VYTVOŘEN v HDFS při spuštění aplikace, a poté jej odebrat, když chcete zastavit. Pro přístup k souboru značek použijte samostatné vlákno v aplikaci Spark, které volá kód podobný tomuto:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Další kroky

* [Přehled streamování Apache Spark](apache-spark-streaming-overview.md)
* [Vytváření úloh Apache Spark streamování s právě jednou zpracováním událostí](apache-spark-streaming-exactly-once.md)
* [Dlouhotrvající úlohy streamování Apache Spark v PŘÍZi](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Strukturované streamování: sémantika odolná proti chybám](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Diskretizovaný Streams: model Fault-Tolerant pro zpracování škálovatelného streamování](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
