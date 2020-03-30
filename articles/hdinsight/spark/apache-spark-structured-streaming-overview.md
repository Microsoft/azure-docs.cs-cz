---
title: Strukturované streamování spark v Azure HDInsight
description: Jak používat aplikace strukturovaného streamování Spark v clusterech HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 19cfd5d8ed4100048c270fb41e5e54a920c61516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75548832"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Přehled strukturovaného streamování Apache Spark

[Apache Jiskra](https://spark.apache.org/) Strukturované streamování umožňuje implementovat škálovatelné aplikace s vysokou propustností a odolností proti chybám pro zpracování datových proudů. Strukturované streamování je postaveno na modulu Spark SQL a vylepšuje se na konstrukcích z datových rámců a datových sad Spark SQL, takže můžete psát dotazy streamování stejným způsobem, jako byste psali dávkové dotazy.  

Strukturované streamovací aplikace běží na clusterech HDInsight Spark a připojují se k streamovaným datům z [Apache Kafka](https://kafka.apache.org/), tcp socketu (pro účely ladění), Azure Storage nebo Azure Data Lake Storage. Poslední dvě možnosti, které jsou závislé na externích službách úložiště, umožňují sledovat nové soubory přidané do úložiště a zpracovávat jejich obsah, jako by byly přenášeny datovým proudem.

Strukturované streamování vytvoří dlouhotrvající dotaz, během kterého použijete operace na vstupní data, jako je výběr, projekce, agregace, okna a spojení datového rámce s odkazem na datové rámce. Dále výstup výsledků do úložiště souborů (Azure Storage Blobs nebo Data Lake Storage) nebo do libovolného úložiště dat pomocí vlastního kódu (například SQL Database nebo Power BI). Strukturované streamování také poskytuje výstup do konzoly pro ladění místně a do tabulky v paměti, takže můžete vidět data generovaná pro ladění v HDInsight.

![Zpracování datových proudů se strukturovaným streamováním HDInsight a Spark](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> Strukturované streamování Spark nahrazuje streamování Spark (DStreams). Do budoucna bude strukturované streamování přijímat vylepšení a údržbu, zatímco DStreams bude pouze v režimu údržby. Strukturované streamování není aktuálně tak kompletní jako funkce jako DStreams pro zdroje a propady, které podporuje po vybalení z krabice, takže vyhodnoťte své požadavky a zvolte příslušnou možnost zpracování datového proudu Spark.

## <a name="streams-as-tables"></a>Datové proudy jako tabulky

Spark Structured Streaming představuje datový proud dat jako tabulku, která je neohraničená do hloubky, to znamená, že tabulka nadále roste s příchodem nových dat. Tato *vstupní tabulka* je průběžně zpracovávána dlouhotrvajícím dotazem a výsledky jsou odesílány do výstupní *tabulky*:

![Koncept strukturovaného streamování](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

Ve strukturovaném streamování data dorazí do systému a je okamžitě ingestována do vstupní tabulky. Můžete psát dotazy (pomocí DataFrame a Dataset API), které provádějí operace proti této vstupní tabulce. Výstup dotazu dává další tabulku, *tabulku výsledků*. Tabulka výsledků obsahuje výsledky dotazu, ze kterého nakreslíte data pro externí úložiště dat, jako je relační databáze. Časování zpracování dat ze vstupní tabulky je řízeno *intervalem aktivační události*. Ve výchozím nastavení je interval aktivační události nulový, takže strukturované streamování se pokusí zpracovat data, jakmile dorazí. V praxi to znamená, že jakmile strukturované streamování je provedeno zpracování spuštění předchozího dotazu, spustí další zpracování spustit proti všechna nově přijatá data. Aktivační událost můžete nakonfigurovat tak, aby se spouštěla v intervalu, aby byla streamovaná data zpracována v dávkách založených na čase.

Data v tabulkách výsledků mohou obsahovat pouze data, která jsou nová od posledního zpracování*dotazu (režim připojení),* nebo tabulka může být aktualizována pokaždé, když jsou nová data, takže tabulka obsahuje všechna výstupní data od začátku dotazu streamování (*úplný režim*).

### <a name="append-mode"></a>Režim připojení

V režimu připojení jsou v tabulce výsledků k dispozici pouze řádky přidané do tabulky výsledků od posledního spuštění dotazu a zapsány do externího úložiště. Nejjednodušší dotaz například zkopíruje všechna data ze vstupní tabulky do tabulky výsledků beze změny. Pokaždé, když interval aktivační události uplyne, nová data jsou zpracovány a řádky představující nová data se zobrazí v tabulce výsledků.

Zvažte scénář, ve kterém zpracováváte telemetrii z teplotních senzorů, jako je například termostat. Předpokládejme, že první aktivační událost zpracována v čase 00:01 pro zařízení 1 s hodnotou teploty 95 stupňů. V první aktivační události dotazu se v tabulce výsledků zobrazí pouze řádek s časem 00:01. V době 00:02 při příchodu jiné události je jediným novým řádkem řádek s časem 00:02, takže tabulka výsledků bude obsahovat pouze tento jeden řádek.

![Režim připojení strukturovaného streamování](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Při použití režimu připojení by dotaz používal projekce (výběr sloupců, na kterých mu záleží), filtrování (vychystávání pouze řádků, které odpovídají určitým podmínkám) nebo spojování (zvětšení dat o data ze statické vyhledávací tabulky). Režim připojení usnadňuje nabízení pouze příslušných nových datových bodů do externího úložiště.

### <a name="complete-mode"></a>Kompletní režim

Zvažte stejný scénář, tentokrát pomocí režimu dokončení. V úplném režimu je celá výstupní tabulka aktualizována na každé aktivační události, takže tabulka obsahuje data nejen z posledního spuštění aktivační události, ale ze všech spuštění. Úplný režim můžete použít ke kopírování dat beze změny ze vstupní tabulky do tabulky výsledků. Při každém spuštění se zobrazí nové řádky výsledků spolu se všemi předchozími řádky. Tabulka výsledků výstupu bude nakonec ukládání všech dat shromážděných od začátku dotazu a nakonec dojde paměť. Režim Complete je určen pro použití s agregačními dotazy, které nějakým způsobem shrnují příchozí data, takže na každé aktivační události je tabulka výsledků aktualizována novým souhrnem.

Předpokládejme, že zatím existuje pět sekund 'v hodnotě dat již zpracovány, a je čas na zpracování dat pro šestou sekundu. Vstupní tabulka obsahuje události pro čas 00:01 a čas 00:03. Cílem tohoto ukázkového dotazu je poskytnout průměrnou teplotu zařízení každých pět sekund. Implementace tohoto dotazu použije agregaci, která přebírá všechny hodnoty, které spadají do každého okna 5 sekund, zprůměruje teplotu a vytvoří řádek pro průměrnou teplotu v tomto intervalu. Na konci prvního 5sekundového okna jsou dvě řazené kolekce členů: (00:01, 1, 95) a (00:03, 1, 98). Takže pro okno 00:00-00:05 agregace produkuje n-tice s průměrnou teplotou 96,5 stupňů. V dalším 5sekundovém okně je pouze jeden datový bod v čase 00:06, takže výsledná průměrná teplota je 98 stupňů. V době 00:10, pomocí režimu dokončení, tabulka výsledků obsahuje řádky pro obě okna 00:00-00:05 a 00:05-00:10, protože dotaz výstupy všechny agregované řádky, nikoli pouze nové. Proto tabulka výsledků nadále roste jako nová okna jsou přidány.

![Režim dokončení strukturovaného streamování](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Ne všechny dotazy pomocí režimu dokončení způsobí, že tabulka růst bez hranic.  V předchozím příkladu zvažte, že místo průměrování teploty podle časového okna se místo toho zprůměrovalo podle ID zařízení. Výsledková tabulka obsahuje pevný počet řádků (jeden na zařízení) s průměrnou teplotou zařízení ve všech datových bodech přijatých z tohoto zařízení. Při přijetí nových teplot je tabulka výsledků aktualizována tak, aby průměry v tabulce byly vždy aktuální.

## <a name="components-of-a-spark-structured-streaming-application"></a>Součásti aplikace Strukturované streamování Spark

Jednoduchý ukázkový dotaz může sumarizovat hodnoty teploty podle hodinových oken. V takovém případě se data ukládají v souborech JSON ve službě Azure Storage (připojené jako výchozí úložiště pro cluster HDInsight):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Tyto soubory JSON jsou `temps` uloženy v podsložce pod kontejnerem clusteru HDInsight.

### <a name="define-the-input-source"></a>Definování vstupního zdroje

Nejprve nakonfigurujte datový rámec, který popisuje zdroj dat a všechna nastavení vyžadovaná tímto zdrojem. Tento příklad čerpá ze souborů JSON ve službě Azure Storage a použije schéma na ně v době čtení.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Použití dotazu

Dále použijte dotaz, který obsahuje požadované operace proti datového rámce. V tomto případě agregace seskupí všechny řádky do 1 hodinová okna a pak vypočítá minimální, průměrné a maximální teploty v tomto 1hodinovém okně.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Definování výstupníjím jímky

Dále definujte cíl pro řádky, které jsou přidány do tabulky výsledků v rámci každého intervalu aktivační události. Tento příklad pouze výstupy všechny řádky `temps` do tabulky v paměti, které můžete později dotaz s SparkSQL. Kompletní výstupní režim zajišťuje, že všechny řádky pro všechna okna jsou výstup pokaždé.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Spuštění dotazu

Spusťte dotaz streamování a spouštět, dokud není přijat signál ukončení.

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Zobrazení výsledků

Při spuštění dotazu ve stejné SparkSession, můžete spustit dotaz `temps` SparkSQL proti tabulce, kde jsou uloženy výsledky dotazu.

    select * from temps

Tento dotaz poskytuje podobné výsledky následujícím:

| Okno |  min(teplota) | avg(temp) | max(teplota) |
| --- | --- | --- | --- |
|{u'start': u'2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u'2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u'2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u'2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u'2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

Podrobnosti o rozhraní API strukturovaného datového proudu Spark spolu se vstupními zdroji dat, operacemi a výstupy, které podporuje, naleznete v [tématu Apache Spark Structured Streaming Programming Guide](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Vytváření kontrolních bodů a zápis napřed protokoly

Chcete-li dodat odolnost proti chybám a odolnost proti chybám, strukturované streamování spoléhá na *vytváření kontrolních bodů* zajistit, že zpracování datového proudu může pokračovat bez přerušení, a to i při selhání uzlu. Ve službě HDInsight Spark vytváří kontrolní body pro trvalé úložiště, ať už azure storage nebo úložiště datových jezer. Tyto kontrolní body ukládají informace o průběhu dotazu streamování. Strukturované streamování navíc používá protokol wal *(write-ahead log).* Wal zachycuje přijatá data, která byla přijata, ale ještě nebyla zpracována dotazem. Pokud dojde k selhání a zpracování je restartován z WAL, žádné události přijaté ze zdroje nejsou ztraceny.

## <a name="deploying-spark-streaming-applications"></a>Nasazení aplikací streamování Spark

Aplikaci Spark Streaming obvykle vytvoříte místně do souboru JAR a pak ji nasadíte do Spark na HDInsight zkopírováním souboru JAR do výchozího úložiště připojeného k clusteru HDInsight. Aplikaci můžete spustit pomocí rozhraní API [Apache Livy](https://livy.incubator.apache.org/) REST, která jsou k dispozici v clusteru, pomocí operace POST. Tělo POST obsahuje dokument JSON, který poskytuje cestu k jar, název třídy, jejíž hlavní metoda definuje a spouští aplikaci streamování a volitelně požadavky na prostředky úlohy (například počet vykonavatelů, paměti a jader) a všechna nastavení konfigurace kódu aplikace vyžaduje.

![Nasazení aplikace Streamování Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Stav všech aplikací lze také zkontrolovat pomocí požadavku GET proti koncovému bodu LIVY. Nakonec můžete ukončit spuštěnou aplikaci vydáním požadavku DELETE proti koncovému bodu LIVY. Podrobnosti o rozhraní LIVY API najdete v [tématu Vzdálené úlohy s Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Další kroky

* [Vytvoření clusteru Apache Spark v HDInsightu](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Průvodce strukturovaným programováním datových proudů Apache Spark](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Spusťte pracovní příležitosti Apache Spark na dálku s Apache LIVY](apache-spark-livy-rest-interface.md)
