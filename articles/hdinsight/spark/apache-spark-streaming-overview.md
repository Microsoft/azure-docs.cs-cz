---
title: Streamování Spark v Azure HDInsight
description: Jak používat aplikace Apache Spark Streaming v clusterech HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 521d72642a27995d096402a4ca0e4af632b0788c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74406262"
---
# <a name="overview-of-apache-spark-streaming"></a>Přehled streamování Apache Spark

[Apache Jiskra](https://spark.apache.org/) Streamování poskytuje zpracování datového proudu v clusterech HDInsight Spark se zárukou, že každá vstupní událost se zpracuje přesně jednou, i když dojde k selhání uzlu. Spark Stream je dlouhotrvající úloha, která přijímá vstupní data z celé řady zdrojů, včetně Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), raw TCP sockets nebo z monitorování souborových systémů [Apache Hadoop YARN.](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Na rozdíl od procesu založeného výhradně na událostech, Spark Stream dávky vstupní data do časových oken, jako je například 2-druhý řez a pak transformuje každou dávku dat pomocí mapy, snížit, spojit a extrahovat operace. Spark Stream pak zapíše transformovaná data do souborových systémů, databází, řídicích panelů a konzoly.

![Zpracování datových proudů pomocí hdinsightu a streamování Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Aplikace streamování spark musí čekat zlomek sekundy shromažďovat každý *mikro dávky* událostí před odesláním této dávky na zpracování. Naproti tomu aplikace řízená událostmi zpracuje každou událost okamžitě. Latence streamování spark je obvykle pod několik sekund. Výhodami mikrodávkového přístupu jsou efektivnější zpracování dat a jednodušší souhrnné výpočty.

## <a name="introducing-the-dstream"></a>Představujeme DStream

Spark Streaming představuje nepřetržitý proud příchozích dat pomocí *diskretizovaného datového proudu* nazývaného DStream. DStream lze vytvořit ze vstupních zdrojů, jako jsou centra událostí nebo Kafka, nebo použitím transformace na jiném DStream.

DStream poskytuje vrstvu abstrakce nad nezpracovaná data událostí.

Začněte s jedinou událostí, řekněme údaje o teplotě z připojeného termostatu. Když tato událost dorazí do aplikace Spark Streaming, událost je uložena spolehlivým způsobem, kde je replikována na více uzlech. Tato odolnost proti chybám zajišťuje, že selhání libovolného uzlu nepovede ke ztrátě události. Jádro Spark používá datovou strukturu, která distribuuje data mezi více uzly v clusteru, kde každý uzel obecně udržuje vlastní data v paměti pro nejlepší výkon. Tato datová struktura se nazývá *pružná distribuovaná datová sada* (RDD).

Každý RDD představuje události shromážděné v rámci uživatelem definovaného časového rámce nazývaného *dávkový interval*. S ukončením každého intervalu dávky se vytváří nový RDD, který obsahuje všechna data z tohoto intervalu. Souvislá sada RDDs jsou shromažďovány do DStream. Například pokud interval dávky je jedna sekunda dlouhá, DStream vyzařuje dávku každou sekundu obsahující jeden RDD, který obsahuje všechna data pohostinovaná během této sekundy. Při zpracování DStream, teplota událost se zobrazí v jedné z těchto dávek. Aplikace Spark Streaming zpracovává dávky, které obsahují události a nakonec funguje na data uložená v každém RDD.

![Příklad DStream s teplotními událostmi](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struktura aplikace Spark Streaming

Aplikace Spark Streaming je dlouhotrvající aplikace, která přijímá data ze zdrojů ingestování, používá transformace ke zpracování dat a pak odešle data do jednoho nebo více cílů. Struktura aplikace Spark Streaming má statický díl a dynamický díl. Statická část definuje, odkud data pocházejí, jaké zpracování má být na datech a kam by měly být výsledky. Dynamická část je spuštěna aplikace po neomezenou dobu, čeká na signál stop.

Například následující jednoduchá aplikace obdrží řádek textu přes soket TCP a spočítá, kolikrát se každé slovo zobrazí.

### <a name="define-the-application"></a>Definování aplikace

Definice aplikační logiky má čtyři kroky:

1. Vytvořte StreamingContext.
2. Vytvořte DStream z StreamingContext.
3. Použít transformace DStream.
4. Výstup výsledky.

Tato definice je statická a dokud aplikaci nespustíte, nebudou zpracována žádná data.

#### <a name="create-a-streamingcontext"></a>Vytvoření kontextu streamování

Vytvořte StreamingContext z SparkContext, který odkazuje na váš cluster. Při vytváření StreamingContext zadáte velikost dávky v sekundách, například:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Vytvoření DStreamu

S StreamingContext instance, vytvořte vstupní DStream pro vstupní zdroj. V tomto případě aplikace sleduje vzhled nových souborů ve výchozím úložišti připojeném ke clusteru HDInsight.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Použití transformací

Implementovat zpracování použitím transformace na DStream. Tato aplikace obdrží jeden řádek textu najednou ze souboru, rozdělí každý řádek na slova a pak používá vzor zmenšení mapy, aby se spočítal počet výskytů každého slova.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Výstupní výsledky

Posunout výsledky transformace do cílových systémů použitím výstupních operací. V tomto případě je výsledek každého spuštění prostřednictvím výpočtu vytištěn ve výstupu konzoly.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pro streamování a spouštěj, dokud není přijat signál ukončení.

```
ssc.start()
ssc.awaitTermination()
```

Podrobnosti o rozhraní Api streamování Spark, spolu se zdroji událostí, transformacemi a výstupními operacemi, které podporuje, najdete v [tématu Apache Spark Streaming Programming Guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Následující ukázková aplikace je samostatná, takže ji můžete spustit uvnitř [poznámkového bloku Jupyter](apache-spark-jupyter-notebook-kernels.md). Tento příklad vytvoří mock zdroj dat ve třídě DummySource, který výstupy hodnotu čítače a aktuální čas v milisekundách každých pět sekund. Nový Objekt StreamingContext má dávkový interval 30 sekund. Při každém vytvoření dávky aplikace pro streamování zkontroluje vytvořené RDD, převede RDD na datový rámec Spark a vytvoří přes datový rámec.

```
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
```

Počkejte asi 30 sekund po spuštění výše uvedené aplikace.  Potom můžete pravidelně dotazovat dataframe, abyste viděli aktuální sadu hodnot přítomných v dávce, například pomocí tohoto dotazu SQL:

```sql
%%sql
SELECT * FROM demo_numbers
```

Výsledný výstup vypadá takto:

| value | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Existuje šest hodnot, protože DummySource vytvoří hodnotu každých 5 sekund a aplikace vydává dávku každých 30 sekund.

## <a name="sliding-windows"></a>Posuvná okna

Chcete-li provádět souhrnné výpočty na vašem DStream v určitém časovém období, například získat průměrnou teplotu za poslední dvě sekundy, můžete použít *operace posuvné okno* součástí Streamování Spark. Posuvné okno má dobu trvání (délku okna) a interval, během kterého je obsah okna vyhodnocován (interval posunutí).

Posuvná okna se mohou překrývat, například můžete definovat okno o délce dvou sekund, které se každou sekundu posouvá. To znamená, že pokaždé, když provedete výpočet agregace, okno bude obsahovat data z poslední sekundy předchozího okna, stejně jako všechna nová data v další jedné sekundě.

![Příklad počátečního okna s teplotními událostmi](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Příklad okna s událostmi teploty po posunutí](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

Následující příklad aktualizuje kód, který používá DummySource, shromažďovat dávky do okna s dobu jedné minuty a jeden minutsnímek.

```
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

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Po první minutě je 12 položek - šest položek z každé ze dvou šarží shromážděných v okně.

| value | time |
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

Funkce posuvné okno k dispozici v rozhraní API streamování Spark patří okno, countByWindow, reduceByWindow a countByValueAndWindow. Podrobnosti o těchto funkcích naleznete [v tématu Transformace na DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Vytváření kontrolních bodů

Chcete-li zajistit odolnost proti chybám a odolnost proti chybám, Spark Streaming spoléhá na vytváření kontrolních bodů, aby zajistily, že zpracování datového proudu může pokračovat bez přerušení, a to i v případě selhání uzlu. Ve službě HDInsight Spark vytváří kontrolní body do trvalého úložiště (Azure Storage nebo Storage data lake). Tyto kontrolní body ukládají metadata o aplikaci streamování, jako je například konfigurace, operace definované aplikací a všechny dávky, které byly zařazeny do fronty, ale ještě nebyly zpracovány. V některých případech budou kontrolní body také zahrnovat ukládání dat v RDD s rychlejším obnovením stavu dat z toho, co je přítomno v RDD spravovaných Spark.

## <a name="deploying-spark-streaming-applications"></a>Nasazení aplikací streamování Spark

Aplikaci Spark Streaming obvykle vytvoříte místně do souboru JAR a pak ji nasadíte do Spark na HDInsight zkopírováním souboru JAR do výchozího úložiště připojeného k clusteru HDInsight. Aplikaci můžete spustit pomocí rozhraní API LIVY REST, která jsou k dispozici v clusteru, pomocí operace POST. Tělo POST obsahuje dokument JSON, který poskytuje cestu k jar, název třídy, jejíž hlavní metoda definuje a spouští aplikaci streamování a volitelně požadavky na prostředky úlohy (například počet vykonavatelů, paměti a jader) a všechna nastavení konfigurace kódu aplikace vyžaduje.

![Nasazení aplikace Streamování Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Stav všech aplikací lze také zkontrolovat pomocí požadavku GET proti koncovému bodu LIVY. Nakonec můžete ukončit spuštěnou aplikaci vydáním požadavku DELETE proti koncovému bodu LIVY. Podrobnosti o rozhraní LIVY API najdete v [tématu Vzdálené úlohy s Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Další kroky

* [Vytvoření clusteru Apache Spark v HDInsightu](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Průvodce programováním datových proudů Apache Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Spusťte pracovní příležitosti Apache Spark na dálku s Apache LIVY](apache-spark-livy-rest-interface.md)
