---
title: 'Kurz: Apache Spark strukturovaného streamování s využitím Apache Kafka-Azure HDInsight'
description: Zjistěte, jak pomocí streamování Apache Sparku přenášet data do nebo ze systému Apache Kafka. V tomto kurzu budete streamovat data pomocí poznámkového bloku Jupyter ze Sparku ve službě HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: hrasheed
ms.openlocfilehash: da31b6a880344de918a3b3e0f89f60d985db2ce7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406021"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Kurz: Použití strukturovaného streamování Apache Spark s Apache Kafka v HDInsight

V tomto kurzu se dozvíte, jak pomocí [Apache Spark strukturovaného streamování](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) číst a zapisovat data pomocí [Apache Kafka](https://kafka.apache.org/) v Azure HDInsight.

Strukturované streamování Spark je modul pro zpracování datových proudů založený na Spark SQL. Umožňuje zrychlit streamované i dávkové výpočty se statickými daty.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použití šablony Azure Resource Manager k vytváření clusterů
> * Použití strukturovaného streamování Sparku s Kafka

Jakmile budete hotovi s kroky v tomto dokumentu, nezapomeňte clustery odstranit, abyste se vyhnuli nadbytečným poplatkům.

## <a name="prerequisites"></a>Požadavky

* JQ, procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Seznamte se s používáním poznámkových [bloků Jupyter](https://jupyter.org/) s Sparkem ve službě HDInsight. Další informace najdete v tématu [načtení dat a spuštění dotazů s Apache Spark v dokumentu HDInsight](spark/apache-spark-load-data-run-query.md) .

* Znalost programovacího jazyku [Scala](https://www.scala-lang.org/). Kód použitý v tomto kurzu je napsaný v jazyce Scala.

* Znalost vytváření témat Kafka. Další informace najdete v dokumentu [rychlý Start pro Apache Kafka v HDInsight](kafka/apache-kafka-get-started.md) .

> [!IMPORTANT]  
> Kroky v tomto dokumentu vyžadují skupinu prostředků Azure obsahující cluster Spark ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve virtuální síti Azure, což umožňuje přímou komunikaci clusteru Spark s clusterem Kafka.
> 
> Pro usnadnění práce tento dokument odkazuje na šablonu, která může vytvořit všechny požadované prostředky Azure. 
>
> Další informace o používání služby HDInsight ve virtuální síti najdete v tématu Naplánování [služby Virtual Network for HDInsight](hdinsight-plan-virtual-network-deployment.md) .

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

Operace streamování také používá `awaitTermination(30000)`, což zastaví datový proud po 30 000 ms. 

Pokud chcete použít strukturované streamování s využitím systému Kafka, váš projekt musí obsahovat závislost na balíčku `org.apache.spark : spark-sql-kafka-0-10_2.11`. Verze tohoto balíčku musí odpovídat verzi Sparku ve službě HDInsight. Pro Spark 2.2.0 (k dispozici ve službě HDInsight 3.6) můžete najít informace o závislostech pro různé typy projektů na adrese [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Pro Jupyter Notebook používané v tomto kurzu načte následující buňka tuto závislost balíčku:

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

Apache Kafka ve službě HDInsight neposkytuje přístup ke zprostředkovatelům systému Kafka přes veřejný internet. Cokoli, co využívá systém Kafka, musí být ve stejné virtuální síti Azure. V tomto kurzu se clustery Kafka i Spark nacházejí ve stejné virtuální síti Azure. 

Následující diagram znázorňuje tok komunikace mezi Sparkem a systémem Kafka:

![Diagram clusterů Spark a Kafka ve virtuální síti Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Komunikace služby Kafka je omezená na virtuální síť. Další služby v clusteru, jako jsou SSH a Ambari, jsou přístupné přes internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [Porty a identifikátory URI používané službou HDInsight](hdinsight-hadoop-port-settings-for-services.md).

K vytvoření virtuální sítě Azure a následnému vytvoření clusterů Kafka a Spark v rámci této sítě použijte následující postup:

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Šablona Azure Resource Manageru se nachází na adrese **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** .

    Tato šablona vytvoří následující prostředky:

   * Cluster Kafka ve službě HDInsight 3.6.
   * Cluster Spark 2.2.0 ve službě HDInsight 3.6.
   * Virtuální síť Azure obsahující clustery HDInsight.

     > [!IMPORTANT]  
     > Poznámkový blok strukturovaného streamování použitý v tomto kurzu vyžaduje Spark 2.2.0 ve službě HDInsight 3.6. Pokud používáte starší verzi Sparku ve službě HDInsight, při použití poznámkového bloku se zobrazí chyby.

2. Pomocí následujících informací vyplňte položky v části **Přizpůsobená šablona**:

    | Nastavení | Value |
    | --- | --- |
    | Subscription | Vaše předplatné Azure |
    | Resource group | Skupina prostředků obsahující prostředky. |
    | Location | Oblast Azure, ve které se prostředky vytvoří. |
    | Název clusteru Spark | Název clusteru Spark. Prvních šest znaků se musí lišit od názvu clusteru Kafka. |
    | Název clusteru Kafka | Název clusteru Kafka. Prvních šest znaků se musí lišit od názvu clusteru Spark. |
    | Uživatelské jméno přihlášení clusteru | Uživatelské jméno správce clusterů. |
    | Heslo přihlášení clusteru | Heslo správce clusterů. |
    | Uživatelské jméno SSH | Uživatel SSH, který se má pro clustery vytvořit. |
    | Heslo SSH | Heslo uživatele SSH. |
   
    ![Snímek obrazovky přizpůsobené šablony](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Přečtěte si **Podmínky a ujednání** a pak vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře**.

4. Nakonec zaškrtněte políčko **Připnout na řídicí panel** a vyberte **Koupit**. 

> [!NOTE]  
> Vytvoření clusterů může trvat až 20 minut.

## <a name="use-spark-structured-streaming"></a>Použití strukturovaného streamování Sparku

Tento příklad ukazuje, jak používat strukturované streamování Sparku s Kafka ve službě HDInsight. Používá data na cestách taxislužby, která jsou k dispozici v New Yorku City.  Datová sada použitá v tomto poznámkovém bloku je z [2016 zelených dat taxislužby](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb).

1. Shromážděte informace o hostiteli. Pomocí níže uvedených příkazů složené a [JQ](https://stedolan.github.io/jq/) můžete získat informace o hostitelích Kafka Zookeeper a Broker. Příkazy jsou určeny pro příkazový řádek systému Windows. pro ostatní prostředí budou potřeba drobné variace. Nahraďte `KafkaCluster` názvem vašeho clusteru Kafka a `KafkaPassword` heslem pro přihlášení ke clusteru. Nahraďte `C:\HDI\jq-win64.exe` také skutečnou cestou k instalaci JQ. Zadejte příkazy do příkazového řádku Windows a uložte výstup pro použití v pozdějších krocích.

    ```cmd
    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

2. Ve webovém prohlížeči se připojte k poznámkovému bloku Jupyter na svém clusteru Spark. V následující adrese URL nahraďte `CLUSTERNAME` názvem svého clusteru __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po zobrazení výzvy zadejte přihlašovací jméno (správce) a heslo clusteru, které jste použili při vytváření clusteru.

3. Pokud chcete vytvořit Poznámkový blok, vyberte **nový > Spark** .

4. Načíst balíčky používané poznámkovým blokem zadáním následujících informací v buňce s poznámkovým blokem. Spusťte příkaz pomocí **kombinace kláves CTRL + ENTER**.

    ```
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
        }
    }
    ```

5. Vytvořte téma Kafka. Níže uvedený příkaz upravte nahrazením `YOUR_ZOOKEEPER_HOSTS` informací o hostiteli Zookeeper extrahovaných v prvním kroku. Pokud chcete vytvořit `tripdata` téma, zadejte do svého Jupyter notebook upravený příkaz.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

6. Načtěte data na taxislužby TRIPS. Zadáním příkazu do další buňky načtěte data na taxislužby TRIPS v New York City. Data jsou načtena do datového rámce a datový rámec se zobrazí jako výstup buňky.

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

7. Nastavte informace o hostitelích zprostředkovatele Kafka. Nahraďte `YOUR_KAFKA_BROKER_HOSTS` službou Broker informace o hostitelích, které jste extrahovali v kroku 1.  Do následujícího Jupyter Notebook buňky zadejte upravený příkaz.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"
    
    println("Finished setting Kafka broker and topic configuration.")
    ```

8. Odešlete data do Kafka. V následujícím příkazu `vendorid` se pole používá jako hodnota klíče pro zprávu Kafka. Klíč používá Kafka při dělení dat. Všechna pole jsou uložena ve zprávě Kafka jako hodnota řetězce JSON. Zadáním následujícího příkazu v Jupyter uložíte data do Kafka pomocí dávkového dotazu.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

9. Deklaruje schéma. Následující příkaz ukazuje, jak použít schéma při čtení dat JSON z Kafka. Do další buňky Jupyter zadejte příkaz.

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

10. Vyberte data a spusťte Stream. Následující příkaz ukazuje, jak načíst data z Kafka pomocí dávkového dotazu a pak zapsat výsledky do HDFS v clusteru Spark. V tomto příkladu `select` načte zprávu (pole hodnota) z Kafka a použije pro ni schéma. Data se pak zapíší do HDFS (WASB nebo ADL) ve formátu Parquet. Do další buňky Jupyter zadejte příkaz.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()
    
    println("Wrote data to file")
    ```

11. Zadáním příkazu do další buňky Jupyter můžete ověřit, že se soubory vytvořily. Obsahuje seznam souborů v `/example/batchtripdata` adresáři.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

12. Zatímco předchozí příklad použil dávkový dotaz, následující příkaz ukazuje, jak provést stejnou věc pomocí dotazu streamování. Do další buňky Jupyter zadejte příkaz.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

13. Spusťte následující buňku a ověřte, zda byly soubory zapsány dotazem pro streamování.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a pak zvolte __Skupiny prostředků__. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

> [!WARNING]  
> Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá.
> 
> Odstraněním clusteru Kafka ve službě HDInsight odstraníte také všechna data uložená v systému Kafka.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak pomocí [Apache Spark strukturovaného streamování](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) zapisovat a číst data z [Apache Kafka](https://kafka.apache.org/) v HDInsight. Pomocí následujícího odkazu se dozvíte, jak používat [Apache Storm](https://storm.apache.org/) s Kafka.

> [!div class="nextstepaction"]
> [Použití Apache Storm s Apache Kafka](hdinsight-apache-storm-with-kafka.md)
