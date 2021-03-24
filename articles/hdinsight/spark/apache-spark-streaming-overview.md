---
title: Streamování Sparku v Azure HDInsight
description: Jak používat aplikace Apache Spark streamování v clusterech HDInsight Spark.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: e8a9f771827b870f493d6b0d7590feee7fc52b20
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870241"
---
# <a name="overview-of-apache-spark-streaming"></a>Přehled streamování Apache Spark

[Apache Spark](https://spark.apache.org/) Streamování zajišťuje zpracování datových proudů v clusterech HDInsight Spark. S jistotou, že jakákoli vstupní událost je zpracována přesně jednou, i když dojde k selhání uzlu. Datový proud Spark je dlouhodobá úloha, která přijímá vstupní data z nejrůznějších zdrojů, včetně Azure Event Hubs. Také: Azure IoT Hub, Apache Kafka, Apache Flume, Twitter, `ZeroMQ` raw TCP Sockets nebo monitorování Apache HADOOP přízové systémy souborů. Na rozdíl od výhradně procesu založeného na událostech vytvoří datový proud Spark vstupní data do časových oken. Například řez o dvou sekundách a následně transformuje každou dávku dat pomocí map, snižování, spojování a extrahování operací. Datový proud Spark pak zapisuje transformovaná data mimo systém souborů, databází, řídicích panelů a konzoly.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png" alt-text="Zpracování datových proudů pomocí HDInsight a streamování Sparku" border="false":::

Aplikace Spark streamování musí `micro-batch` před odesláním této dávky na zpracování počkat na zlomek sekundy. Naproti tomu aplikace řízená událostmi zpracovává každou událost okamžitě. Latence streamování Sparku obvykle trvá několik sekund. Výhody mikrodávkového přístupu jsou efektivnější zpracování dat a jednodušší agregační výpočty.

## <a name="introducing-the-dstream"></a>Představujeme DStream

Streamování Spark představuje souvislý datový proud příchozích dat pomocí *datového proudu diskretizovaný* s názvem DStream. DStream se dá vytvořit ze vstupních zdrojů, jako je například Event Hubs nebo Kafka. Nebo aplikováním transformací na jiné DStream.

DStream poskytuje vrstvu abstrakce nad nezpracovaná data události.

Začněte s jedinou událostí, řekněme od připojeného termostata teploty. Když tato událost dorazí do vaší aplikace streamování Sparku, událost se uloží spolehlivým způsobem, kde se replikuje na více uzlech. Tato odolnost proti chybám zajišťuje, že selhání jakéhokoli jednoho uzlu nevede ke ztrátě události. Spark Core používá datovou strukturu, která distribuuje data napříč více uzly v clusteru. Kde každý uzel obecně udržuje vlastní data v paměti, aby se dosáhlo co nejlepšího výkonu. Tato datová struktura se nazývá *odolná distribuovaná datová sada* (RDD).

Každý RDD představuje události shromážděné v rámci uživatelsky definovaného časového rámce označovaného jako *interval dávky*. Po uplynutí každého intervalu dávky bude vytvořen nový RDD, který obsahuje všechna data z tohoto intervalu. Souvislá sada RDD je shromažďována do DStream. Pokud je například interval dávky jedna sekunda, DStream vygeneruje dávku každou sekundu obsahující jednu RDD, která obsahuje všechna data ingestovaná během této sekundy. Při zpracování DStream se událost teploty zobrazuje v jedné z těchto dávek. Aplikace pro streamování Spark zpracovává dávky, které obsahují události a nakonec fungují s daty uloženými v jednotlivých RDD.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png" alt-text="Příklad DStream s událostmi teploty" border="false":::

## <a name="structure-of-a-spark-streaming-application"></a>Struktura aplikace streamování Sparku

Aplikace pro streamování Spark je dlouhodobě běžící aplikace, která přijímá data ze zdrojů ingestování. Aplikuje transformace pro zpracování dat a pak data přenáší do jednoho nebo více cílů. Struktura aplikace streamování Spark má statickou část a dynamickou část. Statická část určuje, odkud data pocházejí, jak zpracování dat provádět. A kde by měly výsledky jít. Dynamická část spouští aplikaci po neomezenou dobu a čeká na signál k zastavení.

Například následující jednoduchá aplikace obdrží řádek textu přes soket TCP a spočítá počet zobrazených slov.

### <a name="define-the-application"></a>Definování aplikace

Definice logiky aplikace má čtyři kroky:

1. Vytvořte StreamingContext.
2. Vytvořte DStream z StreamingContext.
3. Aplikujte transformace na DStream.
4. Výstup výsledků.

Tato definice je statická a není zpracována žádná data, dokud aplikaci nespustíte.

#### <a name="create-a-streamingcontext"></a>Vytvoření StreamingContext

Vytvořte StreamingContext z SparkContext, který odkazuje na váš cluster. Při vytváření StreamingContext zadáte velikost dávky v sekundách, například:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Vytvoření DStream

S instancí StreamingContext vytvořte vstupní DStream pro vstupní zdroj. V takovém případě aplikace sleduje vzhled nových souborů ve výchozím připojeném úložišti.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Použití transformací

Zpracování implementujete použitím transformací na DStream. Tato aplikace od souboru přijímá jeden řádek textu, každý řádek rozdělí na slova. A pak pomocí schématu pro omezení rozvržení spočítá počet, kolikrát se každé slovo zobrazí.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Výsledky výstupu

Nahrajte výsledky transformace do cílových systémů použitím operací výstup. V tomto případě se výsledek každého spuštění během výpočtu vytiskne ve výstupu konzoly.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci streamování a spusťte ji až do přijetí signálu ukončení.

```
ssc.start()
ssc.awaitTermination()
```

Podrobnosti o rozhraní Spark Stream API najdete v tématu [Průvodce programováním](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)pro streamování Apache Spark.

Následující ukázková aplikace je samostatně obsažená, takže ji můžete spustit v [Jupyter notebook](apache-spark-jupyter-notebook-kernels.md). Tento příklad vytvoří z třídy DummySource zdroj dat, který vypíše hodnotu čítače a aktuální čas v milisekundách každých pět sekund. Nový objekt StreamingContext má interval dávky 30 sekund. Pokaždé, když se vytvoří dávka, aplikace streamování prověřuje RDD vyrobené. Poté převede RDD na datový rámec Spark a vytvoří dočasnou tabulku přes datový rámec.

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

Počkejte přibližně 30 sekund od spuštění aplikace výše.  Pak můžete pravidelně dotazovat se na datový rámec a zobrazit aktuální sadu hodnot přítomných v dávce, například pomocí tohoto dotazu SQL:

```sql
%%sql
SELECT * FROM demo_numbers
```

Výsledný výstup vypadá jako následující výstup:

| hodnota | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

K dispozici jsou šest hodnot, protože DummySource vytvoří hodnotu každých 5 sekund a aplikace vygeneruje dávku každých 30 sekund.

## <a name="sliding-windows"></a>Posuvná okna

K provádění agregačních výpočtů v DStream za určité časové období, například k získání průměrné teploty za poslední dvě sekundy, použijte `sliding window` operace zahrnuté ve streamování Spark. Posuvné okno má dobu trvání (délka okna) a interval, během kterého jsou vyhodnoceny obsah okna (interval snímků).

Posuvná okna se můžou překrývat, například můžete definovat okno o délce dvou sekund. Tyto snímky se pokaždé podruhé. Tato akce znamená pokaždé, když provedete výpočet agregace, okno bude obsahovat data z poslední jedné sekundy předchozího okna. A všechna nová data v následující jedné druhé.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png" alt-text="Příklad počátečního okna s událostmi teploty" border="false":::

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png" alt-text="Příklad okna s událostmi teploty po posouvání" border="false":::

Následující příklad aktualizuje kód, který používá DummySource, pro shromáždění dávek do okna s dobou trvání 1 minuty a snímkovým snímkem.

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

Po první minutě se v každé z těchto dvou dávek shromážděných v okně nachází 12 záznamů – šest záznamů.

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

Funkce posuvných oken, které jsou k dispozici v rozhraní API pro streamování Spark, zahrnují Window, countByWindow, reduceByWindow a countByValueAndWindow. Podrobnosti o těchto funkcích najdete v tématu [transformace v DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Vytváření kontrolních bodů

Pro zajištění odolnosti proti chybám streamování Sparku spoléhá na kontrolní body, aby bylo zajištěno, že zpracování streamování může pokračovat bez přerušení, dokonce i v případě selhání uzlu. Spark vytvoří kontrolní body pro trvalé úložiště (Azure Storage nebo Data Lake Storage). Tyto kontrolní body ukládají metadata aplikace streamování, jako je konfigurace, a operace definované aplikací. Také všechny dávky, které byly zařazeny do fronty, ale ještě nebyly zpracovány. V některých případech budou kontrolní body také zahrnovat ukládání dat do RDD, aby bylo možné rychleji znovu sestavit stav dat z toho, co je k dispozici v RDD spravovaném Sparkem.

## <a name="deploying-spark-streaming-applications"></a>Nasazení aplikací pro streamování Sparku

Obvykle se aplikace pro streamování Spark vytváří místně do souboru JAR. Pak ji nasaďte do Sparku ve službě HDInsight zkopírováním souboru JAR do výchozího připojeného úložiště. Aplikaci můžete spustit pomocí rozhraní LIVY REST API, která jsou dostupná z clusteru, pomocí operace POST. Tělo příspěvku obsahuje dokument JSON, který poskytuje cestu k vašemu JAR. A název třídy, jejíž hlavní Metoda definuje a spouští aplikaci pro streamování, a volitelně požadavky na prostředky úlohy (například počet prováděcích modulů, paměti a jader). Také všechna nastavení konfigurace, která váš kód aplikace vyžaduje.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png" alt-text="Nasazení aplikace pro streamování Sparku" border="false":::

Stav všech aplikací lze také zkontrolovat pomocí požadavku GET na LIVY koncový bod. Nakonec můžete ukončit běžící aplikaci vyvoláním žádosti o odstranění na koncový bod LIVY. Podrobnosti o rozhraní LIVY API najdete v tématu [vzdálené úlohy s Apache LIVY](apache-spark-livy-rest-interface.md) .

## <a name="next-steps"></a>Další kroky

* [Vytvoření clusteru Apache Spark v HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Průvodce programováním pro Apache Spark streaming](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Přehled strukturovaného streamování Apache Spark](apache-spark-structured-streaming-overview.md)
