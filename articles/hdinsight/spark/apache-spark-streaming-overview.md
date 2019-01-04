---
title: Spark Streaming v Azure HDInsight
description: Jak používat streamování Sparku aplikací v clusterech HDInsight Spark.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 796cbcfc7ab905c54b878a5ccc866efbf9242fb6
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652077"
---
# <a name="overview-of-apache-spark-streaming"></a>Přehled streamování Apache Sparku

[Apache Spark](https://spark.apache.org/) streamování poskytuje v clusterech HDInsight Spark, se zárukou, že žádný vstup události pro zpracování datových proudů dat je zpracována právě jednou, i když dojde k selhání uzlu. Spark Stream je dlouho běžící úlohu, která přijímá vstupní data z nejrůznějších zdrojů, včetně služby Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ ](http://zeromq.org/), nezpracovaná TCP sockety, nebo z monitorování [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) systémy souborů. Na rozdíl od procesu výhradně založený na událostech Spark Stream dávek vstupní data do časových oken, jako je například určitý řez 2sekundové a potom transformací pro jednotlivé dávky dat s využitím map, snížit, připojte se k a extrahovat operace. Spark Stream Transformovaná data zapíše navýšení kapacity pro systémy souborů, databází, řídicí panely a konzoly.

![Stream zpracování ve službě HDInsight a streamování Sparku](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark Streaming aplikace musíte počkat zlomek sekundy ke shromažďování jednotlivých *mikrodávek* událostí před odesláním této služby batch zpracování. Naproti tomu aplikace založené na událostech zpracuje každou událost okamžitě. Spark Streaming latence je obvykle za pár sekund. Výhody přístup mikrodávek. jsou účinnější zpracování dat a jednodušší agregační výpočty.

## <a name="introducing-the-dstream"></a>Úvod DStream

Nepřetržitý datový proud příchozích dat pomocí Spark Streaming představuje *diskretizovaný stream* volána DStream. DStream lze vytvořit ze vstupních zdrojů, jako jsou Event Hubs a Kafka, nebo použití transformací na jiné DStream.

DStream poskytuje vrstvu abstrakční vrstvu nezpracovaných dat událostí. 

Začněte s jedinou událost, Řekněme, že teplota čtení z připojených termostat. Pokud události dorazí aplikace Spark Streaming, události se ukládají spolehlivě, kde se replikuje na několika uzlech. Odolnost proti chybám zajistí, že nebude selhání jakéhokoli jednoho způsobit ztrátu události. Spark core používá datová struktura, která distribuuje data napříč několika uzly v clusteru, kde každý uzel obvykle udržuje svůj vlastní. data v paměti pro zajištění nejlepšího výkonu. Tato datová struktura se nazývá *odolné distributed dataset* (RDD).

Každý RDD představuje událostí shromážděných za období definované uživatelem volá *batch interval*. Protože každé dávky interval uplyne, je vytvořen nový RDD, který obsahuje všechna data z tohoto intervalu. Spojité sady Rdd se shromáždí do DStream. Například pokud batch interval je 1 sekunda dlouhý, vaše DStream vydává dávky každý druhý obsahující jeden RDD, obsahující všechna data přijatá během této druhé. Při zpracování DStream událostí teplotní se zobrazí v jednom z těchto listech. Aplikace Spark Streaming zpracovává listy, které obsahují události a nakonec pracuje s daty uloženými v každé RDD.

![Příklad DStream s událostí teplotní ](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struktura aplikace Spark Streaming

Aplikace Spark Streaming je dlouho běžící aplikace, které přijímá data ze zdroje pro ingestování, platí transformace ke zpracování dat a pak nasadí data do jednoho nebo více cílů. Struktura aplikace Spark Streaming má statické části a dynamické části. Definuje statické části, kde data pochází, jaké zpracování na data a zobrazit výsledky. Dynamická část je spuštěna aplikace po neomezenou dobu, čeká na signál k zastavení.

Například následující jednoduché aplikace přijímá řádek textu přes soket TCP a vypočítá počet, kolikrát se každé slovo zobrazí.

### <a name="define-the-application"></a>Definování aplikace

Definice aplikace logiky má čtyři kroky:

1. Vytvoření StreamingContext.
2. Vytvoření DStream z StreamingContext.
3. Použití transformací na DStream.
4. Vypíše výsledky.

Tato definice je statická a žádná data se zpracovává, dokud se nespustí aplikaci.

#### <a name="create-a-streamingcontext"></a>Vytvoření StreamingContext

Vytvoření StreamingContext z SparkContext odkazující na váš cluster. Při vytváření StreamingContext, můžete zadat velikost dávky v sekundách, například:

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>Vytvoření DStream

Instanci StreamingContext vytvořte vstupní DStream pro vstupní zdroj. V tomto případě aplikace sleduje pro vzhled odeslání nových souborů ve výchozím nastavení úložiště připojené ke clusteru HDInsight.

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>Použití transformací

Použití transformací na DStream implementujete zpracování. Tato aplikace přijímá jeden řádek textu po jednom ze souboru, rozdělí slova každý řádek a pak používá vzor map-reduce ke zjištění počtu, kolikrát se každé slovo zobrazí.

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>Výsledky výstupu

Nabídněte výsledky transformace cílové systémy s použitím výstupních operací. V takovém případě je vytištěna výsledek každého spuštění prostřednictvím výpočtu ve výstupu konzoly.

    wordCounts.print()

### <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikace pro streaming a až do ukončení signál.

    ssc.start()            
    ssc.awaitTermination()

Podrobnosti o rozhraní API služby Stream Spark, spolu s zdroje událostí, transformace a výstupní operace, které podporuje, najdete v části [Apache Spark Streaming Průvodce programováním pro službu](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Následující ukázkové aplikace je samostatný, abyste mohli spustit ji uvnitř [Poznámkový blok Jupyter](apache-spark-jupyter-notebook-kernels.md). Tento příklad vytvoří zdroj dat mock ve třídě DummySource, jejichž výstupem jsou hodnoty čítače a aktuální čas v milisekundách každých pět sekund. Nový objekt StreamingContext má batch intervalu 30 sekund. Pokaždé, když se vytvoří dávky, aplikace streaming prozkoumá RDD vytvořen, převede RDD Spark DataFrame a vytvoří dočasné tabulky prostřednictvím datového rámce.

    class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

        /** Start the thread that simulates receiving data */
        def onStart() {
            new Thread("Dummy Source") { override def run() { receive() } }.start()
        }

        def onStop() {  }

        /** Periodically generate a random number from 0 to 9, and the timestamp */
        private def receive() {
            var counter = 0  
            while(!isStopped()) {
                store(Iterator((counter, System.currentTimeMillis)))
                counter += 1
                Thread.sleep(5000)
            }
        }
    }

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process RDDs in the batch
    stream.foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
            .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

Můžete pak dotazování datového rámce pravidelně, chcete-li zobrazit aktuální sadu hodnot, které jsou k dispozici ve službě batch, například pomocí tohoto dotazu SQL:

    %%sql
    SELECT * FROM demo_numbers

Výsledný výstup vypadá takto:

| hodnota | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Existují šest hodnoty, protože DummySource vytvoří hodnotu každých 5 sekund a aplikace vydá dávku každých 30 sekund.

## <a name="sliding-windows"></a>Klouzavé systému windows

Provádění výpočtů agregační na vaše DStream za některé časové období, třeba když chcete získat průměrnou teplota za poslední 2 sekundy, můžete použít *klouzavým 5minutovým* operace součástí Spark Streaming. Posuvné okno má dobu trvání (délka okna) a interval, během které obsah okna jsou u nich vyhodnoceno (interval snímku).

Klouzavé windows může dojít k překrytí, například můžete definovat okno s délkou 2 sekundy, snímky za jednu sekundu. To znamená, že pokaždé, když se provést výpočet agregace, okno, které bude obsahovat data z posledních jedné sekundy předchozího okna i žádná nová data do příští jedné sekundy.

![Příklad počáteční okno s událostí teplotní](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Příklad okna událostí teplotní za klouzavé](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

Následující příklad aktualizuje kód, který používá DummySource, shromažďovat dávek do okna s dobou trvání minutových a minutových snímku.

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process batches in 1 minute windows
    stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

Po první minutu jsou 12 položek - šest položek z každé ze dvou dávky shromážděných v okně.

| hodnota | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

K dispozici v rozhraní API Spark Streaming posuvné okno funkce zahrnují okna, countByWindow, reduceByWindow a countByValueAndWindow. Podrobnosti o těchto funkcích najdete v tématu [transformace na diskretizovanými streamy](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Vytváření kontrolních bodů

K zajištění odolnosti proti chybám a odolnost proti chybám, Spark Streaming spoléhá na vytváření kontrolních bodů k zajištění, že zpracování datových proudů můžete pokračovat bez přerušení, dokonce i v případě selhání uzlu. Spark v HDInsight, vytvoří kontrolní body do trvalého úložiště (Azure Storage nebo Data Lake Storage). Tyto kontrolní body ukládají metadata o datových proudů aplikaci, například konfigurace operací definovaných v aplikaci a všechny listy, které byly zařazeny do fronty, ale ještě nebyla zpracována. V některých případech se body obnovení bude obsahovat také ukládá data v Rdd rychleji opětovné sestavení stav dat od co je součástí sady Rdd spravuje Spark.

## <a name="deploying-spark-streaming-applications"></a>Nasazení aplikací Spark Streaming

Obvykle sestavit aplikace Spark Streaming místně do souboru JAR a nasadit ho na Spark v HDInsight zkopírováním souboru JAR do výchozího úložiště připojené ke clusteru HDInsight. Vaše aplikace může začínat LIVY REST API, který je k dispozici z clusteru pomocí operace POST. Text příspěvku obsahuje dokument JSON, který obsahuje cestu k JAR, název třídy, jejichž hlavní metoda definuje a spouští aplikace streaming a volitelně požadavky na prostředky úlohy (například počet moduly provádění, paměti a jader) , a všechna nastavení konfigurace kódu aplikace vyžaduje.

![Nasazení aplikace Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Stav všech aplikací můžete také zkontrolovat požadavek GET na koncový bod LIVY. Nakonec můžete ukončit běžící aplikaci vydáním odstranit požadavek na koncový bod LIVY. Podrobnosti o rozhraní API LIVY najdete v tématu [vzdálené úlohy s Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Další postup

* [Vytvoření clusteru Apache Spark v HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark Streaming Průvodce programováním](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Spouštět úlohy Apache Sparku s využitím Apache LIVY](apache-spark-livy-rest-interface.md)
