---
title: Vytvoření vysoce dostupné úlohy streamování Spark v YARN - Azure HDInsight | Microsoft Docs
description: Jak nastavit vysílání datového proudu Spark pro scénář vysokou dostupností.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: 0a738d7e26384523e9da9c8c79e12729330fe6f7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="create-high-availability-spark-streaming-jobs-with-yarn"></a>Vytvořit úlohy streamování Spark vysoké dostupnosti se YARN

Vysílání datového proudu Spark umožňuje implementovat škálovatelné, vysokou propustností, odolný proti chybám aplikací pro zpracování datových proudů. Vysílání datového proudu Spark aplikace v clusteru HDInsight Spark můžete připojovat k řadě datových zdrojů, například Azure Event Hubs, Azure IoT Hub, Kafka, Flume, Twitter, ZeroMQ, nezpracovaná sockets TCP, nebo sledování změn systému souborů HDFS. Vysílání datového proudu Spark podporuje odolnost proti chybám s záruku, že danou událost je přesně jedno zpracování i s selhání uzlu.

Vysílání datového proudu Spark, vytvoří se dlouho běžící úlohy, během které je možné použít transformace dat, a pak nabízená výsledky systémy, databáze, řídicí panely a konzole. Vysílání datového proudu Spark zpracovávání micro dávek dat, první shromažďování dávky události v definované časovém intervalu. V dalším kroku tohoto batch se odesílají na pro zpracování a výstup. Časové intervaly batch jsou obvykle definovány v zlomků sekund.

![Vysílání datového proudu Spark](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStreams

Vysílání datového proudu Spark představuje nepřetržitý proud dat pomocí *diskretizovaný datového proudu* (DStream). Tento DStream lze vytvořit ze vstupního zdroje jako Event Hubs nebo Kafka, nebo použití transformace na jiné DStream. Když událost přijde aplikace vysílání datového proudu Spark, uloží se událost spolehlivě. Data události se tedy replikují tak, aby více uzlů jeho kopii. Tím se zajistí, že selhání žádné jeden uzel nebude vést ke ztrátě události.

Základní Spark používá *odolné distribuované datové sady* (RDDs). RDDs distribuci dat mezi několika uzly v clusteru, kde každý uzel obecně udržuje jeho data zcela v paměti pro nejlepší výkon. Každý RDD představuje událostí shromážděných v intervalu batch. Jakmile batch uplyne, vysílání datového proudu Spark vytvoří nové RDD obsahující všechna data v tomto intervalu. Tato spojité sady RDDs se shromažďují do DStream. Aplikace se vysílání datového proudu Spark zpracuje data uložená v každé dávce RDD.

![Spark DStream](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Strukturované datové proudy Spark úlohy

Strukturované datové proudy Spark byla zavedena v Spark 2.0 jako modul analytické pro použití v datových proudů strukturovaná data. Strukturované datové proudy Spark používá SparkSQL dávkování modul rozhraní API. Stejně jako u vysílání datového proudu Spark, Spark strukturovaných streamování spustí jeho výpočty přes nepřetržitě přicházejících micro dávek dat. Strukturované datové proudy Spark představuje datový proud jako tabulku vstup se neomezený počet řádků. To znamená vstupní tabulky se stále rostoucí jako dorazí nová data. Tato tabulka vstup nepřetržitě zpracovává dlouhotrvající dotazu a výsledky jsou zapsané do výstupní tabulky.

![Spark strukturovaná streamování](./media/apache-spark-streaming-high-availability/structured-streaming.png)

Ve strukturovaných streamování data dorazí na systém a je okamžitě konzumována do tabulky vstup. Můžete psát dotazy, které provádějí operace s tabulkou tento vstup. Výstup dotazu dává jiné tabulky, volá se v tabulce výsledků. Tabulka výsledků obsahuje výsledky dotazu, ze kterého kreslení data k odeslání do externího úložiště dat relační databáze. *Aktivační událost interval* nastaví časování při zpracování dat z tabulky vstup. Ve výchozím nastavení strukturovaných streamování zpracuje data, jakmile doručení. Můžete však také nakonfigurovat spouštějí na delší interval, takže zpracování dat v dávkách založené na čase. Data v tabulce výsledků mohou být zcela aktualizovat pokaždé, když je nová data, aby od začátku streamování dotaz obsahuje všechny výstupní data (*dokončení režimu*), nebo může obsahovat jenom data, která je nového od minulého čas zpracování dotazu (*režim připojení*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Vytvoření odolné proti chybám úloh Spark streamování

Chcete-li vytvořit vysoce dostupné prostředí pro úlohy vysílání datového proudu Spark, spusťte kódování jednotlivých úloh pro obnovení v případě selhání. Takové samoobslužné obnovení úlohy jsou odolné proti chybám.

RDDs mít několik vlastností, které pomáhají vysoce dostupné a odolné proti chybám vysílání datového proudu Spark úlohy:

* Dávky vstupní data uložená v RDDs jako DStream se automaticky replikují v paměti pro odolnost proti chybám.
* Data ztracen z důvodu selhání pracovního procesu můžete přepočítávány z replikovaná vstupní data na různých pracovníci, dokud jsou k dispozici tyto uzly pracovního procesu.
* Rychlé obnovení selhání může docházet k jedné sekundy zotavení z chyb nebo podezřelé hodnoty se stane prostřednictvím výpočtů v paměti.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Přesně-jednou sémantiku pomocí vysílání datového proudu Spark

Pokud chcete vytvořit aplikaci, která zpracovává všechny události po (a pouze jednou), vezměte v úvahu jak všechny systému body selhání restartovat po s problémem, a ztrátě dat se můžete vyhnout. Přesně-po sémantiku vyžadovat, aby žádná data dojde ke ztrátě v libovolném bodě, a zpracování této zprávy se dá se restartovat, bez ohledu na to, kde dojde k selhání. V tématu [vytvořit vysílání datového proudu Spark úlohy s přesně-jednou událostí zpracování](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-yarn"></a>Vysílání datového proudu Spark a YARN

V prostředí HDInsight, cluster pracovní koordinuje *ještě jiný prostředek Vyjednavač* (YARN). Navrhování vysokou dostupnost pro datové proudy Spark zahrnuje techniky pro vysílání datového proudu Spark a také pro součásti YARN.  Příklad konfigurace pomocí YARN jsou uvedeny níže. 

![YARN architektura](./media/apache-spark-streaming-high-availability/yarn-arch.png)

Následující části popisují aspekty návrhu pro tuto konfiguraci.

### <a name="plan-for-failures"></a>Plán pro selhání

Pokud chcete vytvořit YARN konfiguraci pro vysoké dostupnosti, byste měli naplánovat možných selhání vykonavatele nebo ovladač. Některé úlohy streamování Spark také obsahovat data záruku požadavky, které potřebují další konfiguraci a instalaci. Například může streamování aplikací mít obchodním požadavkem pro nula data ztrátu zaručit i přes všechny chyby, ke kterému dochází v systému hostitele streamování nebo clusteru HDInsight.

Pokud **vykonavatele** selže, její úkoly a příjemci jsou restartovat Spark automaticky, takže není třeba žádná změna konfigurace.

Ale pokud **ovladač** nezdaří, pak všechny její přidružené vykonavatelů selhání, a všechny přijaté bloky a výpočetní výsledky budou ztraceny. Chcete-li obnovit v případě selhání ovladačů, použijte *DStream vytváření kontrolních bodů* jak je popsáno v [vytvořit vysílání datového proudu Spark úlohy s přesně-jednou událostí zpracování](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). Vytváření kontrolních bodů DStream pravidelně uloží *směrované Acyklické grafu* (DAG) z DStreams na odolný proti chybám úložiště, například Azure Storage.  Vytváření kontrolních bodů umožňuje, Spark strukturovaných streamování restartování neúspěšných ovladače z informací kontrolního bodu.  Tento ovladač restart spustí novou vykonavatelů a také restartuje příjemci.

Ovladače s DStream vytváření kontrolních bodů obnovení:

* Konfigurace automatického ovladač restartování na YARN s nastavení konfigurace `yarn.resourcemanager.am.max-attempts`.
* Nastavit adresář kontrolního bodu v systému souborů HDFS kompatibilní s `streamingContext.checkpoint(hdfsDirectory)`.
* Změny struktury zdrojový kód a použít kontrolní body obnovení, například:

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

* Konfigurace obnovení ke ztrátě dat. tím, že umožňuje předběžné protokolování (WAL) s `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`a zakažte replikaci v paměti pro vstupní DStreams s `StorageLevel.MEMORY_AND_DISK_SER`.

To Shrneme, pomocí vytváření kontrolních bodů + WAL + spolehlivé příjemci, bude možné pro doručení "alespoň jednou" obnovení dat:

* Přesně po, tak dlouho, dokud není přijatých dat. ztraceny a výstupy jsou buď idempotent nebo transakcí.
* Právě jednou s nový Kafka přímý přístup, který používá Kafka jako replikované protokolu, nikoli pomocí příjemci nebo WALs.

### <a name="typical-concerns-for-high-availability"></a>Typické otázky pro zajištění vysoké dostupnosti

* Je obtížné sledovat úloha streamování než úlohy batch. Vysílání datového proudu Spark úlohy jsou obvykle dlouhotrvající a YARN není agregace protokoly, až do dokončení úlohy.  Kontrolní body Spark se ztratí během aplikací nebo aktualizací Spark a budete muset vymazat adresář kontrolního bodu během upgradu.

* Konfigurace režimu vaší YARN clusteru spustit ovladače, i když se nezdaří klienta. Nastavení automatické restartování pro ovladače:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark a rozhraní vysílání datového proudu Spark mít systém konfigurovat metriky. Můžete také používat další knihovny, například grafitová/Grafana stáhnout metriky řídicího panelu, jako je počet záznamů zpracovat, 'Využití paměti nebo GC na ovladače & vykonavatelů', 'celkový delay', 'využití clusteru, a tak dále. V strukturovaných streamování verze 2.1 nebo novější, můžete použít `StreamingQueryListener` získat další metriky.

* Měli segmentovat dlouho běžící úlohy.  Při aplikaci na datové proudy Spark odeslání do clusteru, musí být definovány YARN fronty, kde má být úloha spuštěna. Můžete použít [Plánovač kapacity YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) dlouho běžící úlohy do fronty oddělení.

* Řádné ukončení aplikace streamování. Pokud vaše posuny jsou známé, a všechny stav aplikace je uložených externě, můžete programově zastavit streamování aplikace na příslušné místo. Jeden technik je použít "vláken háky" v Spark, kontrolou pro externí příznak každých *n* sekund. Můžete použít také *značky souboru* , je vytvořena na HDFS při spouštění aplikace a potom odebrat, pokud chcete zastavit. Značky přístup, soubor použijte samostatné vlákno v aplikaci Spark, který volá kód podobná této:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Další postup

* [Přehled vysílání datového proudu Spark](apache-spark-streaming-overview.md)
* [Vytvoření úlohy streamování Spark se přesně-jednou událostí zpracování](apache-spark-streaming-exactly-once.md)
* [Spark dlouhotrvajících úloh na rozhraní YARN streamování](http://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [Strukturované datové proudy: Odolný vůči chybám sémantiku selhání](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Diskretizovaný datové proudy: Odolné proti chybám Model pro zpracování škálovatelné datového proudu](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
