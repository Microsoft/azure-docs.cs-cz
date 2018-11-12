---
title: Vytvoření vysoce dostupné úlohy streamování Sparku v YARNU – Azure HDInsight
description: Jak vytvořit Spark Streaming pro scénáře vysoké dostupnosti.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/26/2018
ms.openlocfilehash: 8f680b60a8f457e1a8619ac044798ff02df15694
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013645"
---
# <a name="create-high-availability-spark-streaming-jobs-with-yarn"></a>Vytvoření úlohy Spark Streaming vysokou dostupnost s YARN

Spark Streaming umožňuje implementovat škálovatelné, vysoce propustné odolné aplikace pro zpracování datových proudů. Aplikace Spark Streaming v clusteru HDInsight Spark se můžete připojit k řadě zdrojů dat, jako je Azure Event Hubs, Azure IoT Hub, Kafka, Flume, Twitter, ZeroMQ, nezpracovaná sockety TCP, nebo při sledování změn systému souborů HDFS. Spark Streaming podporuje odolnost proti chybám s zárukou, že danou událost je zpracována právě jednou, i při selhání uzlu.

Spark Streaming vytvoří dlouhotrvajících úloh, během které je možné použít transformace dat, a potom vydat výsledky systémy souborů, databází, řídicí panely a konzoly. Spark Streaming zpracovává micro dávek dat, tak, že první dávku událostí, které shromažďují za definovaný časový interval. V dalším kroku této služby batch se odesílají na zpracování a výstup. Časové intervaly batch se obvykle definují v zlomků sekund.

![Streamování Sparku](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>Diskretizovanými streamy

Nepřetržitý datový proud dat pomocí Spark Streaming představuje *diskretizovaný stream* (DStream). Tato DStream lze vytvořit ze vstupních zdrojů, jako jsou Event Hubs a Kafka, nebo použití transformací na jiné DStream. Pokud události dorazí v aplikaci Spark Streaming, události se ukládají spolehlivě. To znamená dat událostí, která se replikuje tak, aby více uzlů jeho kopii. Tím se zajistí, že nebude selhání jakéhokoli jednoho způsobit ztrátu události.

Spark core používá *odolné distribuovaných datových sad* (Rdd). Rdd distribuci dat napříč několika uzly v clusteru, kde každý uzel obvykle udržuje svoje data kompletně v paměti pro zajištění nejlepšího výkonu. Každý RDD představuje událostí shromážděných v intervalu služby batch. Když batch interval uplyne, Spark Streaming poskytne nové RDD obsahující všechna data v tomto intervalu. Tato spojité sady Rdd se shromáždí DStream. Aplikace Spark Streaming zpracovává data uložená v každé dávce RDD.

![Spark DStream](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Strukturované streamování Sparku úlohy

Strukturované streamování Sparku byla zavedena v Spark 2.0 jako analytický modul pro použití v datových proudů strukturovaná data. Strukturované streamování Sparku používá SparkSQL dávkování modulu rozhraní API. Stejně jako u Spark Streaming, strukturovaného streamování Sparku se spustí jeho výpočty přes průběžně přicházejících micro dávek dat. Strukturované streamování Sparku představuje datový proud jako vstupní tabulku s neomezený počet řádků. To znamená, že vstupní tabulky se stále rozrůstá příchodu nových dat. Tato tabulka vstup průběžně zpracovává dlouho probíhající dotazy a výsledky jsou zapsané do výstupní tabulky.

![Strukturovaného streamování Sparku](./media/apache-spark-streaming-high-availability/structured-streaming.png)

Ve strukturované streamování data dorazí na systém a ingestuje se hned do vstupní tabulky. Můžete psát dotazy, které provádějí operace s tabulkou tento vstup. Výstup dotazu vrátí jinou tabulku, volá se v tabulce výsledků. Tabulka výsledků obsahuje výsledky dotazu, z něhož nakreslíte data k odeslání do externího úložiště dat relační databáze. *Aktivační událost interval* nastaví časování při zpracování dat ze vstupní tabulky. Ve výchozím nastavení, strukturované streamování tato data zpracuje hned po doručení. Můžete však také nakonfigurovat aktivační událost pro spuštění na delší interval, takže zpracování streamovaných dat v dávkách podle času. Data v tabulce výsledků mohou být zcela aktualizují pokaždé, když je nová data, aby od začátku datového proudu dotazu obsahuje všechny výstupní data (*úplný režim*), nebo může obsahovat jenom data, která je nový od posledního čas zpracování dotazu (*režimu připojení*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Vytvořením odolné proti chybám úloh Spark Streaming

Vytvoření prostředí s vysokou dostupností pro vaše úlohy Spark Streaming, spuštění kódu pro jednotlivé úlohy obnovení v případě selhání. Takové samoobslužné obnovení úlohy jsou odolné proti chybám.

Rdd mít několik vlastností, které pomáhají vysoce dostupné a odolné proti chybám úloh Spark Streaming:

* Dávky vstupní data uložená v Rdd jako DStream se automaticky replikují v paměti pro odolnost proti chybám.
* Data byla ztracena. z důvodu chyby pracovního procesu můžete přepočítány z replikované vstupní data v různých pracovních procesech, jako jsou k dispozici tyto uzly pracovního procesu.
* Rychlé obnovení selhání může dojít v rámci jedné sekundy, jak zotavení z chyb/podezřelých hodnot se stane prostřednictvím výpočty v paměti.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Přesně-jednou sémantiku službou Spark Streaming

Chcete-li vytvořit aplikaci, která zpracovává každé události (pouze jednou a jednou), vezměte v úvahu jak restartuje všechny body selhání systému se vyskytl problém, a jak se můžete vyhnout ztrátě dat. Přesně – Jakmile sémantiku vyžaduje, aby se neztratí kdykoli a zpracování této zprávy je restartování, bez ohledu na to, kde dojde k selhání. Zobrazit [vytvořit streamování Sparku s přesně úlohy-událostí zpracování](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-yarn"></a>Streamování Sparku a YARN

V HDInsight, cluster pracovní koordinuje přes *ještě další Resource Negotiator* (YARN). Návrh vysoké dostupnosti a Spark Streaming zahrnuje techniky pro streamování Sparku a také pro součásti YARN.  Příklad konfigurace pomocí YARN je uveden níže. 

![Architektura YARN](./media/apache-spark-streaming-high-availability/yarn-arch.png)

Následující části popisují aspekty návrhu pro tuto konfiguraci.

### <a name="plan-for-failures"></a>Plán pro selhání

Vytvořit konfiguraci YARN pro vysokou dostupnost, je třeba naplánovat možného selhání exekutor nebo ovladač. Některé úlohy Spark Streaming také zahrnovat požadavky na záruční údaje, které vyžadují další konfigurace a nastavení. Streamování aplikací může mít například pracovní procesy nula data ztrátu zaručit, bez ohledu na všechny chyby, ke které dochází v hostitelském systému streamování nebo clusteru HDInsight.

Pokud **prováděcí modul** selže, její úkoly a příjemci jsou ve Sparku automaticky restartuje, tak se nezměnila konfigurační potřeby.

Nicméně pokud **ovladač** nezdaří, pak všechny jeho přidružené moduly provádění selže a budou ztraceny všechny přijaté bloky a výsledky výpočtů. K zotavení z chyby ovladačů, použijte *vytváření kontrolních bodů DStream* jak je popsáno v [vytvořit streamování Sparku s přesně úlohy-událostí zpracování](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). Vytváření kontrolních bodů DStream pravidelně uloží *orientovaného acyklického grafu* (DAG) z diskretizovanými streamy do úložiště odolné proti chybám, jako je Azure Storage.  Vytváření kontrolních bodů umožňuje strukturované streamování Sparku k restartování neúspěšných ovladače z informací kontrolního bodu.  Tento ovladač restart spustí nové moduly provádění a zároveň restartuje příjemců.

Ovladače s DStream vytváření kontrolních bodů obnovení:

* Konfigurovat automatické ovladač restartování na základě YARNU s nastavením konfigurace `yarn.resourcemanager.am.max-attempts`.
* Nastavit adresář kontrolního bodu v systému souborů HDFS kompatibilního s `streamingContext.checkpoint(hdfsDirectory)`.
* Reorganizaci zdrojový kód a použít kontrolní body obnovení, například:

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

* Konfigurace obnovení ztracených dat povolením zápisu dávky protokolů (WAL) s `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`a zakažte replikaci v paměti pro vstupní diskretizovanými streamy s `StorageLevel.MEMORY_AND_DISK_SER`.

Souhrnně řečeno, pomocí vytváření kontrolních bodů, WAL + spolehlivé příjemci, budete moct doručovat "alespoň jednou" obnovení dat:

* Právě jedno, tak dlouho, dokud nedojde ke ztrátě dat přijatých a výstupy jsou buď idempotentní nebo transakční.
* Právě jednou s nový Kafka přímý přístup využívající Kafka jako replikované protokolu, spíš než příjemci nebo WALs.

### <a name="typical-concerns-for-high-availability"></a>Typické aspekty vysoké dostupnosti

* Je obtížnější sledování datových proudů úloh než dávkových úloh Hive. Spark Streaming úlohy jsou obvykle dlouhodobé a YARN nebude agregovat protokoly, dokud se úloha nedokončí.  Spark kontrolní body jsou ztraceny během aplikací nebo aktualizací Spark a budete muset vymazat adresář kontrolního bodu během upgradu.

* Konfigurovat režim clusteru YARN pro spuštění ovladače i v případě, že klient nebude. Nastavení automatické restartování pro ovladače:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Sparku a Spark Streaming UI mít systém konfigurovatelné metriky. Můžete použít také další knihovny, jako je například grafitová/Grafana stáhnout řídicí panel metrik, jako je počet záznamů zpracována, 'Využití paměti a uvolňování paměti na ovladač a moduly provádění', 'celkový počet delay', "využití clusteru" a tak dále. V strukturované streamování verze 2.1 nebo novější, můžete použít `StreamingQueryListener` shromažďovat další metriky.

* By měl segmentovat dlouhotrvajících úloh.  Při odeslání aplikace Spark Streaming do clusteru, musí být definován YARN fronty, pokud úloha běží. Můžete použít [Plánovač kapacity YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) odesílat dlouho běžící úlohy do samostatné fronty.

* Korektně vypnout streamování aplikace. Pokud vaše posuny se ví, a všechny stav aplikace je uložená externě, můžete prostřednictvím kódu programu zastavit streamování aplikace v příslušném umístění. Jednou z metod je použití "vlákno zachytávání" ve Sparku, tak, že zkontrolujete pro externí příznak každý *n* sekund. Můžete také použít *značkovací soubor* , který je vytvořen v HDFS při spouštění aplikace, pak odebrat, pokud chcete zastavit. Značka souboru přístup použijte samostatném vlákně v aplikaci Spark, který volá kód podobný tomuto:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Další postup

* [Přehled streamování Sparku](apache-spark-streaming-overview.md)
* [Vytvoření úlohy streamování Sparku s přesně-událostí zpracování](apache-spark-streaming-exactly-once.md)
* [Dlouho běžící Streamovacích úloh na základě YARNU Sparku](http://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [Strukturované streamování: Sémantiku odolný vůči chybám odolné](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Diskretizovaný datových proudů: Odolné proti chybám Model pro zpracování škálovatelné Stream](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
