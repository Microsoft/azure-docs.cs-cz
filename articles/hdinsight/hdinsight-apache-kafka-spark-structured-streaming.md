---
title: 'Kurz: Strukturované streamování Apache Sparku s využitím systému Kafka – Azure HDInsight '
description: Zjistěte, jak pomocí streamování Apache Sparku přenášet data do nebo ze systému Apache Kafka. V tomto kurzu budete streamovat data pomocí poznámkového bloku Jupyter ze Sparku ve službě HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: jasonh
ms.openlocfilehash: 47879350c56b9e8b943e1bff61359727746b697d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39598296"
---
# <a name="tutorial-use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Kurz: Použití strukturovaného streamování Sparku se systémem Kafka ve službě HDInsight

V tomto kurzu se dozvíte, jak pomocí strukturovaného streamování Sparku číst a zapisovat data s využitím systému Apache Kafka ve službě Azure HDInsight.

Strukturované streamování Sparku je modul pro zpracování datových proudů založený na Spark SQL. Umožňuje zrychlit streamované i dávkové výpočty se statickými daty. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Strukturované streamování s využitím systému Kafka
> * Vytvoření clusterů Kafka a Spark
> * Nahrání poznámkového bloku do Sparku
> * Použití poznámkového bloku
> * Vyčištění prostředků

Jakmile budete hotovi s kroky v tomto dokumentu, nezapomeňte clustery odstranit, abyste se vyhnuli nadbytečným poplatkům.

## <a name="prerequisites"></a>Požadavky

* Znalost používání poznámkových bloků Jupyter se Sparkem ve službě HDInsight. Další informace najdete v tématu [Načítání dat a spouštění dotazů s využitím Sparku ve službě HDInsight](spark/apache-spark-load-data-run-query.md).

* Znalost programovacího jazyku [Scala](https://www.scala-lang.org/). Kód použitý v tomto kurzu je napsaný v jazyce Scala.

* Znalost vytváření témat Kafka. Další informace najdete v dokumentu [Rychlý start k systému Kafka ve službě HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují skupinu prostředků Azure obsahující cluster Spark ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve virtuální síti Azure, což umožňuje přímou komunikaci clusteru Spark s clusterem Kafka.
> 
> Pro usnadnění práce tento dokument odkazuje na šablonu, která může vytvořit všechny požadované prostředky Azure. 
>
> Další informace o použití služby HDInsight ve virtuální síti najdete v dokumentu [Rozšíření služby HDInsight pomocí virtuální sítě](hdinsight-extend-hadoop-virtual-network.md).

## <a name="structured-streaming-with-kafka"></a>Strukturované streamování s využitím systému Kafka

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

V operaci streamování se také používá `awaitTermination(30000)` a díky tomu se stream zastaví za 30 000 ms. 

Pokud chcete použít strukturované streamování s využitím systému Kafka, váš projekt musí obsahovat závislost na balíčku `org.apache.spark : spark-sql-kafka-0-10_2.11`. Verze tohoto balíčku musí odpovídat verzi Sparku ve službě HDInsight. Pro Spark 2.2.0 (k dispozici ve službě HDInsight 3.6) můžete najít informace o závislostech pro různé typy projektů na adrese [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Pro Jupyter Notebook, který je součástí tohoto kurzu, načte tuto závislost balíčku následující buňka:

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
    
    Šablona Azure Resource Manageru se nachází na adrese **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

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

3. Přečtěte si **Podmínky a ujednání** a pak vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře**.

4. Nakonec zaškrtněte políčko **Připnout na řídicí panel** a vyberte **Koupit**. 

> [!NOTE]
> Vytvoření clusterů může trvat až 20 minut.

## <a name="upload-the-notebook"></a>Nahrání poznámkového bloku

Pokud chcete nahrát poznámkový blok z projektu do clusteru Spark ve službě HDInsight, postupujte následovně:

1. Stáhněte projekt z adresy [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

1. Ve webovém prohlížeči se připojte k poznámkovému bloku Jupyter na svém clusteru Spark. V následující adrese URL nahraďte `CLUSTERNAME` názvem svého clusteru __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po zobrazení výzvy zadejte přihlašovací jméno (správce) a heslo clusteru, které jste použili při vytváření clusteru.

2. Pomocí tlačítka __Upload__ (Nahrát) v pravém horním rohu stránky nahrajte do clusteru soubor __spark-structured-streaming-kafka.ipynb__. Vyberte __Otevřít__ a zahajte nahrávání.

    ![Použití tlačítka Upload (Nahrát) k výběru a nahrání poznámkového bloku](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Výběr souboru KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. V seznamu poznámkových bloků vyhledejte položku __spark-structured-streaming-kafka.ipynb__ a vyberte tlačítko __Upload__ (Nahrát) vedle ní.

    ![Nahrání poznámkového bloku pomocí tlačítka Upload (Nahrát) vedle položky KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Použití poznámkového bloku

Po nahrání souborů výběrem položky __spark-structured-streaming-kafka.ipynb__ otevřete poznámkový blok. Informace o použití strukturovaného streamování Sparku se systémem Kafka ve službě HDInsight najdete v pokynech v poznámkovém bloku.

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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí strukturovaného streamování Sparku zapisovat a číst data ze systému Kafka ve službě HDInsight. Na následujícím odkazu zjistíte, jak používat Storm se systémem Kafka.

> [!div class="nextstepaction"]
> [Použití Apache Stormu se systémem Kafka](hdinsight-apache-storm-with-kafka.md)
