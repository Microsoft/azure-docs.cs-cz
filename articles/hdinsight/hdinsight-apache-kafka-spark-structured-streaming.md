---
title: 'Kurz: Apache Spark Streaming & Apache Kafka - Azure HDInsight'
description: Zjistěte, jak pomocí streamování Apache Sparku přenášet data do nebo ze systému Apache Kafka. V tomto kurzu budete streamovat data pomocí poznámkového bloku Jupyter ze Sparku ve službě HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,seodec18
ms.date: 03/11/2020
ms.openlocfilehash: 66bfa0d3ee4cb03f1b48e2db24be7a90d97f60d6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79117220"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Kurz: Použití strukturovaného streamování Apache Sparku se systémem Apache Kafka ve službě HDInsight

Tento kurz ukazuje, jak používat [Apache Spark Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) ke čtení a zápisu dat s Apache [Kafka](./kafka/apache-kafka-introduction.md) na Azure HDInsight.

Spark Structured Streaming je modul pro zpracování datových proudů postavený na Spark SQL. Umožňuje zrychlit streamované i dávkové výpočty se statickými daty.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použití šablony Správce prostředků Azure k vytvoření clusterů
> * Použití strukturovaného streamování spark s Kafkou

Až budete hotovi s kroky v tomto dokumentu, nezapomeňte odstranit clustery, aby se zabránilo nadměrné poplatky.

## <a name="prerequisites"></a>Požadavky

* jq, procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Seznamte se s používáním [jupyterových notebooků](https://jupyter.org/) se Sparkem na HDInsightu. Další informace najdete v tématu [Načítání dat a spouštění dotazů pomocí Apache Spark v dokumentu HDInsight.](spark/apache-spark-load-data-run-query.md)

* Znalost programovacího jazyku [Scala](https://www.scala-lang.org/). Kód použitý v tomto kurzu je napsaný v jazyce Scala.

* Znalost vytváření témat Kafka. Další informace najdete v dokumentu [apache kafka na hdinsight rychlý start](kafka/apache-kafka-get-started.md) dokumentu.

> [!IMPORTANT]  
> Kroky v tomto dokumentu vyžadují skupinu prostředků Azure obsahující cluster Spark ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve virtuální síti Azure, což umožňuje přímou komunikaci clusteru Spark s clusterem Kafka.
>
> Pro usnadnění práce tento dokument odkazuje na šablonu, která může vytvořit všechny požadované prostředky Azure.
>
> Další informace o používání HDInsight ve virtuální síti najdete v tématu [Plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md) dokumentu.

## <a name="structured-streaming-with-apache-kafka"></a>Strukturované streamování s Apache Kafka

Strukturované streamování Sparku je modul pro zpracování datových proudů založený na modulu Spark SQL. Při použití strukturovaného streamování můžete psát streamovací dotazy stejným způsobem jako dávkové dotazy.

Následující fragmenty kódu ukazují čtení ze systému Kafka a uložení do souboru. První z nich je dávková operace, zatímco druhá je operace streamování:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

V obou fragmentech kódu se data čtou ze systému Kafka a zapisují do souboru. Rozdíly mezi příklady jsou následující:

| Batch | Streamování |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Operace streamování také `awaitTermination(30000)`používá , který zastaví datový proud po 30 000 ms.

Pokud chcete použít strukturované streamování s využitím systému Kafka, váš projekt musí obsahovat závislost na balíčku `org.apache.spark : spark-sql-kafka-0-10_2.11`. Verze tohoto balíčku musí odpovídat verzi Sparku ve službě HDInsight. Pro Spark 2.2.0 (k dispozici v HDInsight 3.6), můžete najít [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar)informace o závislosti pro různé typy projektů na .

Pro poznámkový blok Jupyter použitý v tomto kurzu načte tuto závislost balíčku následující buňka:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Vytvoření clusterů

Apache Kafka na HDInsight neposkytuje přístup k makléřům Kafka přes veřejný internet. Cokoli, co využívá systém Kafka, musí být ve stejné virtuální síti Azure. V tomto kurzu se clustery Kafka i Spark nacházejí ve stejné virtuální síti Azure.

Následující diagram znázorňuje tok komunikace mezi Sparkem a systémem Kafka:

![Diagram clusterů Spark a Kafka ve virtuální síti Azure](./media/hdinsight-apache-kafka-spark-structured-streaming/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Komunikace služby Kafka je omezená na virtuální síť. Další služby v clusteru, jako jsou SSH a Ambari, jsou přístupné přes internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [Porty a identifikátory URI používané službou HDInsight](hdinsight-hadoop-port-settings-for-services.md).

K vytvoření virtuální sítě Azure a následnému vytvoření clusterů Kafka a Spark v rámci této sítě použijte následující postup:

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-kafka-spark-structured-streaming/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Šablona Azure Resource Manager **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**je umístěna na adrese .

    Tato šablona vytvoří následující prostředky:

   * Cluster Kafka ve službě HDInsight 3.6.
   * Cluster Spark 2.2.0 ve službě HDInsight 3.6.
   * Virtuální síť Azure obsahující clustery HDInsight.

     > [!IMPORTANT]  
     > Poznámkový blok strukturovaného streamování použitý v tomto kurzu vyžaduje Spark 2.2.0 ve službě HDInsight 3.6. Pokud používáte starší verzi Sparku ve službě HDInsight, při použití poznámkového bloku se zobrazí chyby.

2. Pomocí následujících informací vyplňte položky v části **Přizpůsobená šablona**:

    | Nastavení | Hodnota |
    | --- | --- |
    | Předplatné | Vaše předplatné Azure |
    | Skupina prostředků | Skupina prostředků obsahující prostředky. |
    | Umístění | Oblast Azure, ve které se prostředky vytvoří. |
    | Název clusteru Spark | Název clusteru Spark. Prvních šest znaků se musí lišit od názvu clusteru Kafka. |
    | Název clusteru Kafka | Název clusteru Kafka. Prvních šest znaků se musí lišit od názvu clusteru Spark. |
    | Uživatelské jméno přihlášení clusteru | Uživatelské jméno správce clusterů. |
    | Heslo přihlášení clusteru | Heslo správce clusterů. |
    | Uživatelské jméno SSH | Uživatel SSH, který se má pro clustery vytvořit. |
    | Heslo SSH | Heslo uživatele SSH. |

    ![Snímek obrazovky přizpůsobené šablony](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Přečtěte si **obchodní podmínky**a pak vyberte Souhlasím **s podmínkami uvedenými výše**.

4. Vyberte **Koupit**.

> [!NOTE]  
> Vytvoření clusterů může trvat až 20 minut.

## <a name="use-spark-structured-streaming"></a>Použití strukturovaného streamování Spark

Tento příklad ukazuje, jak používat Strukturované streamování Spark s Kafka na HDInsight. Využívá údaje o taxi jízdách, které poskytuje New York City.  Datová sada používaná tímto notebookem je z [2016 Green Taxi Trip Data](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb).

1. Shromážděte informace o hostiteli. Použijte curl a [jq](https://stedolan.github.io/jq/) příkazy níže získat kafka ZooKeeper a makléř hostí informace. Příkazy jsou určeny pro příkazový řádek systému Windows, budou zapotřebí mírné odchylky pro jiná prostředí. Nahraďte `KafkaCluster` název clusteru Kafka `KafkaPassword` a přihlašovacím heslem clusteru. Také nahraďte `C:\HDI\jq-win64.exe` skutečnou cestou k instalaci jq. Zadejte příkazy do příkazového řádku systému Windows a uložte výstup pro použití v pozdějších krocích.

    ```cmd
    REM Enter cluster name in lowercase

    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/jupyter`přejděte `CLUSTERNAME` na , kde je název clusteru. Po zobrazení výzvy zadejte přihlašovací jméno (správce) a heslo clusteru, které jste použili při vytváření clusteru.

1. Vyberte **Nový > Spark,** abyste vytvořili poznámkový blok.

1. Spark streaming má microbatching, což znamená, že data přicházejí jako dávky a prováděcí zařízení běží na dávkách dat. Pokud má vykonavatel časový limit nečinnosti menší než doba potřebný ke zpracování dávky, budou vykonavatelé neustále přidávány a odebírány. Pokud je časový limit nečinnosti vykonavatele větší než doba trvání dávky, vykonavatel nikdy neodstraní. Proto **doporučujeme zakázat dynamické přidělení nastavením spark.dynamicAllocation.enabled na false při spuštění aplikací streamování.**

    Balíčky používané poznámkovým blokem načtěte zadáním následujících informací do buňky poznámkového bloku. Spusťte příkaz pomocí **kombinace kláves CTRL + ENTER**.

    ```configuration
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11",
            "spark.dynamicAllocation.enabled": false
        }
    }
    ```

1. Vytvořte téma Kafka. Upravte níže uvedený `YOUR_ZOOKEEPER_HOSTS` příkaz nahrazením informacemi o hostiteli Zookeeper extrahovanými v prvním kroku. Chcete-li `tripdata` vytvořit téma, zadejte upravený příkaz do poznámkového bloku Jupyter.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

1. Načíst data o taxi výlety. Zadejte příkaz v další buňce pro načtení dat o taxi jízdách v New Yorku. Data se načtou do datového rámce a datový rámec se zobrazí jako výstup buňky.

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString

    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)

    // Display the dataframe containing trip data
    taxiDF.show()
    ```

1. Nastavte informace o zprostředkovateli Kafka. Nahraďte `YOUR_KAFKA_BROKER_HOSTS` informacemi, které jste získali v kroku 1, zprostředkovatelem.  Zadejte upravený příkaz do další buňky Jupyter notebooku.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"

    println("Finished setting Kafka broker and topic configuration.")
    ```

1. Pošlete data Do Kafky. V následujícím příkazu `vendorid` se pole používá jako hodnota klíče pro zprávu Kafka. Klíč používá Kafka při dělení dat. Všechna pole jsou uložena ve zprávě Kafka jako hodnota řetězce JSON. Zadáním následujícího příkazu v režimu Jupyter uložte data do Kafky pomocí dávkového dotazu.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

1. Deklarujte schéma. Následující příkaz ukazuje, jak používat schéma při čtení dat JSON z kafka. Zadejte příkaz do další buňky Jupyter.

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)

    println("Schema declared")
    ```

1. Vyberte data a spusťte datový proud. Následující příkaz ukazuje, jak načíst data z kafka pomocí dávkového dotazu a potom zapsat výsledky do HDFS v clusteru Spark. V tomto příkladu načte `select` zprávu (pole hodnoty) z Kafka a použije schéma na něj. Data jsou pak zapsána do HDFS (WASB nebo ADL) ve formátu parket. Zadejte příkaz do další buňky Jupyter.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()

    println("Wrote data to file")
    ```

1. Můžete ověřit, že soubory byly vytvořeny zadáním příkazu v další buňce Jupyter. Obsahuje seznam souborů `/example/batchtripdata` v adresáři.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

1. Zatímco v předchozím příkladu byl použit dávkový dotaz, následující příkaz ukazuje, jak provést totéž pomocí dotazu streamování. Zadejte příkaz do další buňky Jupyter.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

1. Spusťte následující buňku a ověřte, zda byly soubory zapsány dotazem pro streamování.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na [webu Azure Portal](https://portal.azure.com/)rozbalte nabídku na levé straně a otevřete nabídku služeb a pak zvolte __Skupiny prostředků,__ chcete-li zobrazit seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

> [!WARNING]  
> Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá.
>
> Odstraněním clusteru Kafka ve službě HDInsight odstraníte také všechna data uložená v systému Kafka.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat [Apache Spark Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) k zápisu a čtení dat z Apache [Kafka](./kafka/apache-kafka-introduction.md) na HDInsight. Pomocí následujícího odkazu se dozvíte, jak používat [Apache Storm](./storm/apache-storm-overview.md) s Kafkou.

> [!div class="nextstepaction"]
> [Použijte Apache Storm s Apache Kafka](hdinsight-apache-storm-with-kafka.md)
