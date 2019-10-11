---
title: Kurz Apache Spark strukturované streamování s využitím Apache Kafka-Azure HDInsight
description: Naučte se používat streamování Apache Spark k získání dat do Apache Kafka nebo z nich. V tomto kurzu Streamujte data pomocí poznámkového bloku Jupyter ze Sparku ve službě HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 10/08/2019
ms.openlocfilehash: db2174451f01ef38dc69e4e14561175203e075c3
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264253"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Kurz: Použití strukturovaného streamování Apache Spark s Apache Kafka v HDInsight

V tomto kurzu se dozvíte, jak pomocí [Apache Spark strukturovaného streamování](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) číst a zapisovat data pomocí [Apache Kafka](https://kafka.apache.org/) v Azure HDInsight.

Strukturované streamování Spark je modul pro zpracování datových proudů založený na Spark SQL. Umožňuje vám obpočítat výpočty streamování stejné jako dávkové výpočty statických dat.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použití šablony Azure Resource Manager k vytváření clusterů
> * Použití strukturovaného streamování Sparku s Kafka

Až budete s kroky v tomto dokumentu hotovi, nezapomeňte odstranit clustery, abyste se vyhnuli nadměrným poplatkům.

## <a name="prerequisites"></a>Požadavky

* JQ, procesor JSON příkazového řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Seznamte se s používáním [poznámkových bloků Jupyter](https://jupyter.org/) s Sparkem ve službě HDInsight. Další informace najdete v tématu [načtení dat a spuštění dotazů s Apache Spark v dokumentu HDInsight](spark/apache-spark-load-data-run-query.md) .

* Znalost programovacího jazyka [Scala](https://www.scala-lang.org/) Kód použitý v tomto kurzu je napsán v Scala.

* Znalost vytváření Kafka témat. Další informace najdete v dokumentu [rychlý Start pro Apache Kafka v HDInsight](kafka/apache-kafka-get-started.md) .

> [!IMPORTANT]  
> Kroky v tomto dokumentu vyžadují skupinu prostředků Azure, která obsahuje Spark ve službě HDInsight a Kafka v clusteru HDInsight. Tyto clustery se nacházejí v rámci Azure Virtual Network, což umožňuje clusteru Spark přímo komunikovat s clusterem Kafka.
>
> Pro usnadnění práce tento dokument odkazuje na šablonu, která může vytvořit všechny požadované prostředky Azure.
>
> Další informace o používání služby HDInsight ve virtuální síti najdete v tématu [Naplánování služby Virtual Network for HDInsight](hdinsight-plan-virtual-network-deployment.md) .

## <a name="structured-streaming-with-apache-kafka"></a>Strukturované streamování s Apache Kafka

Strukturované streamování Spark je modul pro zpracování datových proudů založený na modulu Spark SQL. Při použití strukturovaného streamování můžete zapisovat dotazy streamování stejným způsobem, jakým píšete dávkové dotazy.

Následující fragmenty kódu ukazují čtení z Kafka a ukládání do souboru. První z nich je operace Batch, zatímco druhá z nich je operace streamování:

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

V obou fragmentech kódu jsou data načítána z Kafka a zapsána do souboru. Rozdíly mezi příklady:

| Partie | Streamování |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Operace streamování také používá `awaitTermination(30000)`, což zastaví datový proud po 30 000 ms.

Aby bylo možné používat strukturované streamování s Kafka, musí mít váš projekt závislost na balíčku `org.apache.spark : spark-sql-kafka-0-10_2.11`. Verze tohoto balíčku by měla odpovídat verzi Sparku ve službě HDInsight. Pro Spark 2.2.0 (k dispozici v HDInsight 3,6) můžete najít informace o závislostech pro různé typy projektů na [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

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

Apache Kafka v HDInsight neposkytuje přístup ke zprostředkovatelům Kafka prostřednictvím veřejného Internetu. Cokoli, co používá Kafka, musí být ve stejné virtuální síti Azure. V tomto kurzu se clustery Kafka a Spark nacházejí ve stejné virtuální síti Azure.

Následující diagram ukazuje způsob komunikace toků mezi Sparkem a Kafka:

![Diagram clusterů Sparku a Kafka ve službě Azure Virtual Network](./media/hdinsight-apache-kafka-spark-structured-streaming/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Služba Kafka je omezená na komunikaci v rámci virtuální sítě. K dalším službám v clusteru, jako jsou SSH a Ambari, se dá dostat přes Internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [porty a identifikátory URI používané v HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Pokud chcete vytvořit Virtual Network Azure a pak v něm vytvořit clustery Kafka a Spark, použijte následující postup:

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu v Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-kafka-spark-structured-streaming/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Šablona Azure Resource Manager se nachází na **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** .

    Tato šablona vytvoří následující prostředky:

   * Kafka v clusteru HDInsight 3,6.
   * 2\.2.0 Spark v clusteru HDInsight 3,6.
   * Virtual Network Azure, který obsahuje clustery HDInsight.

     > [!IMPORTANT]  
     > Strukturovaný Poznámkový blok používaný v tomto kurzu vyžaduje Spark 2.2.0 ve službě HDInsight 3,6. Pokud používáte starší verzi Sparku ve službě HDInsight, dostanete při používání poznámkového bloku chyby.

2. Následující informace použijte k naplnění položek v části **vlastní šablona** :

    | Nastavením | Hodnota |
    | --- | --- |
    | Formě | Vaše předplatné Azure |
    | Skupina prostředků | Skupina prostředků, která obsahuje prostředky. |
    | Umístění | Oblast Azure, ve které se prostředky vytvářejí. |
    | Název clusteru Spark | Název clusteru Spark. Prvních šest znaků se musí lišit od názvu clusteru Kafka. |
    | Název clusteru Kafka | Název clusteru Kafka. Prvních šest znaků se musí lišit od názvu clusteru Spark. |
    | Uživatelské jméno přihlášení clusteru | Uživatelské jméno správce pro clustery. |
    | Heslo pro přihlášení ke clusteru | Uživatelské heslo správce pro clustery. |
    | Uživatelské jméno SSH | Uživatel SSH, který se má pro clustery vytvořit. |
    | Heslo SSH | Heslo uživatele SSH. |

    ![Snímek obrazovky přizpůsobené šablony](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Přečtěte si **podmínky a ujednání**a potom vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními**.

4. Vyberte **koupit**.

> [!NOTE]  
> Vytvoření clusterů může trvat až 20 minut.

## <a name="use-spark-structured-streaming"></a>Použití strukturovaného streamování Sparku

Tento příklad ukazuje, jak používat strukturované streamování Sparku s Kafka ve službě HDInsight. Používá data na cestách taxislužby, která jsou k dispozici v New Yorku City.  Datová sada použitá v tomto poznámkovém bloku je z [2016 zelených dat taxislužby](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb).

1. Shromážděte informace o hostiteli. Pomocí níže uvedených příkazů složené a [JQ](https://stedolan.github.io/jq/) můžete získat informace o hostitelích Kafka Zookeeper a Broker. Příkazy jsou určeny pro příkazový řádek systému Windows. pro ostatní prostředí budou potřeba drobné variace. Nahraďte `KafkaCluster` názvem vašeho clusteru Kafka a `KafkaPassword` s heslem pro přihlášení ke clusteru. Také nahraďte `C:\HDI\jq-win64.exe` skutečnou cestou k instalaci JQ. Zadejte příkazy do příkazového řádku Windows a uložte výstup pro použití v pozdějších krocích.

    ```cmd
    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

2. Ve webovém prohlížeči se připojte k poznámkovému bloku Jupyter v clusteru Spark. V následující adrese URL nahraďte `CLUSTERNAME` názvem vašeho clusteru __Spark__ :

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po zobrazení výzvy zadejte přihlášení ke clusteru (správce) a heslo, které jste použili při vytváření clusteru.

3. Pokud chcete vytvořit Poznámkový blok, vyberte **nový > Spark** .

4. Streamování Sparku má mikrodávkování, což znamená, že data přicházejí do dávek a executers se spouštějí na dávkách dat. Pokud prováděcí modul obsahuje časový limit nečinnosti, který je kratší než čas potřebný ke zpracování dávky, prováděcí moduly budou trvale přidány a odebrány. Pokud je časový limit nečinnosti prováděcích modulů delší než doba trvání dávky, prováděcí modul se nikdy neodebere. Proto **doporučujeme zakázat dynamické přidělování nastavením Spark. dynamicAllocation. Enabled na hodnotu false při spouštění aplikací streamování.**

    Načíst balíčky používané poznámkovým blokem zadáním následujících informací v buňce s poznámkovým blokem. Spusťte příkaz pomocí **kombinace kláves CTRL + ENTER**.

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

5. Vytvořte téma Kafka. Níže uvedený příkaz upravte tak, že nahradíte `YOUR_ZOOKEEPER_HOSTS` informacemi o hostiteli Zookeeper extrahovaných v prvním kroku. Pokud chcete vytvořit téma `tripdata`, zadejte do svého Jupyter Notebook upravený příkaz.

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

7. Nastavte informace o hostitelích zprostředkovatele Kafka. Nahraďte `YOUR_KAFKA_BROKER_HOSTS` informacemi o hostitelích zprostředkovatele, které jste extrahovali v kroku 1.  Do následujícího Jupyter Notebook buňky zadejte upravený příkaz.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"

    println("Finished setting Kafka broker and topic configuration.")
    ```

8. Odešlete data do Kafka. V následujícím příkazu se jako hodnota klíče pro zprávu Kafka používá pole `vendorid`. Klíč používá Kafka při dělení dat. Všechna pole jsou uložena ve zprávě Kafka jako hodnota řetězce JSON. Zadáním následujícího příkazu v Jupyter uložíte data do Kafka pomocí dávkového dotazu.

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

11. Zadáním příkazu do další buňky Jupyter můžete ověřit, že se soubory vytvořily. Obsahuje seznam souborů v adresáři `/example/batchtripdata`.

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

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků se odstraní také přidružený cluster HDInsight a všechny další prostředky přidružené ke skupině prostředků.

Odebrání skupiny prostředků pomocí Azure Portal:

1. V Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a potom zvolte __skupiny prostředků__ . zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a potom klikněte pravým tlačítkem myši na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__a pak potvrďte.

> [!WARNING]  
> Fakturace clusteru HDInsight začíná, jakmile se cluster vytvoří a zastaví se, když se cluster odstraní. Fakturuje se poměrnou částí po minutách, takže byste cluster měli vždycky odstranit, když už se nepoužívá.
>
> Odstranění Kafka v clusteru HDInsight odstraní všechna data uložená v Kafka.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí [Apache Spark strukturovaného streamování](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) zapisovat a číst data z [Apache Kafka](https://kafka.apache.org/) v HDInsight. Pomocí následujícího odkazu se dozvíte, jak používat [Apache Storm](https://storm.apache.org/) s Kafka.

> [!div class="nextstepaction"]
> [Použití Apache Storm s Apache Kafka](hdinsight-apache-storm-with-kafka.md)
