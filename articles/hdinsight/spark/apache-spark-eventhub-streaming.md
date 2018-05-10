---
title: 'Kurz: Zpracování dat z Azure Event Hubs s Apache Spark v Azure HDInsight | Microsoft Docs'
description: Připojit Apache Spark v Azure HDInsight k Azure Event Hubs a zpracování dat streamování.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 9b59f5d58234aaf8f8385f722d6659548e066933
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>Kurz: Proces tweetů pomocí Azure Event Hubs a Spark v HDInsight

V tomto kurzu zjistěte, jak vytvořit Apache Spark streamování aplikací k odesílání tweetů do centra událostí Azure a vytvořit jinou aplikaci ke čtení tweetů z centra událostí. Podrobné vysvětlení vysílání datového proudu Spark, najdete v článku [vysílání datového proudu přehled Apache Spark](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight přináší stejné součásti pro datové proudy pro cluster Spark v Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Odeslání zprávy do centra událostí Azure
> * Čtení zpráv z centra událostí Azure

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **Dokončení článek [kurz: načtení dat a spouštění dotazů na cluster Apache Spark v Azure HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Pro příjem streamovaných tweetů je potřeba vytvořit aplikaci na Twitteru. Postupujte podle pokynů vytvořte aplikaci služby Twitter a poznamenejte si hodnoty, které potřebujete k dokončení tohoto kurzu.

1. Přejděte do [Twitter Správa aplikací](https://apps.twitter.com/).
2. Vyberte **vytvořte novou aplikaci**.
3. Zadejte následující hodnoty:

    - Název: Zadejte název aplikace. Hodnota použitá v tomto kurzu je **HDISparkStreamApp0423**. Tento název musí být jedinečný název.
    - Popis: Zadejte stručný popis aplikace. Hodnota použitá v tomto kurzu je **jednoduchého vysílání datového proudu aplikací HDInsight Spark**.
    - Web: Zadejte aplikace webu. Nemusí to být platný web.  Hodnota použitá v tomto kurzu je **http://www.contoso.com**.
    - Adresa URL zpětné volání: můžete toto pole ponecháte prázdné.

4. Vyberte **Ano, čtení a souhlas s vývojáře smlouvu Twitter**a potom vyberte **vytvořit aplikaci služby Twitter**.
5. Vyberte **klíče a přístupové tokeny** kartě.
6. Vyberte **vytvořit můj přístupový token** na konci stránky.
7. Zapište tyto hodnoty na stránce.  Tyto hodnoty budete potřebovat později v tomto kurzu:

    - **Uživatelský klíč (klíč rozhraní API)**    
    - **Uživatelský tajný klíč (tajný klíč rozhraní API)**  
    - **Přístupový Token**
    - **Tajný klíč přístupového tokenu**   

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure

Toto centrum událostí použijete k uložení tweetů.

1. Přihlaste se k [Portálu Azure](https://ms.portal.azure.com).
2. Vyberte **vytvořit prostředek** v levém horním rohu obrazovky.
3. Vyberte **Internet věcí**, pak vyberte **Event Hubs**.

    ![Centra událostí vytvořit pro příklad vysílání datového proudu Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "centra událostí vytvořit pro příklad vysílání datového proudu Spark")
4. Zadejte následující hodnoty pro nový obor názvů Centrum událostí:

    - **Název**: Zadejte název centra událostí.  Hodnota použitá v tomto kurzu je **myeventhubns20180403**.
    - **Úroveň ceny**: vyberte **standardní**.
    - **Skupina prostředků**: máte možnost vytvořit novou nebo vyberte skupinu prostředků clusteru Spark. 
    - **Umístění**: Vyberte stejný **umístění** jako cluster Apache Spark v HDInsight a snižuje tak latenci a náklady.

    ![Zadejte název centra událostí příklad vysílání datového proudu Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "zadejte název centra událostí příklad vysílání datového proudu Spark")
5. Vyberte **vytvořit** vytvoření oboru názvů.

6. Otevřete Centrum názvový prostor události podle následujících pokynů:

    1. Z portálu, vyberte **všechny služby**.
    2. Do pole filtru zadejte **služby event hubs**.
    3. Dvakrát klikněte na obor názvů, který jste vytvořili.
    4. Vyberte **+ centra událostí**.

6. V seznamu oboru názvů služby Event Hubs vyberte nově vytvořený obor názvů.      
5. Vyberte **Event Hubs**a potom vyberte **+ centra událostí** k vytvoření nového centra událostí.
  

6. Zadejte následující hodnoty:

    - Název: Zadejte název pro vaše Centrum událostí.
    - Počet oddílu: 10
    - Zpráva uchování: 1. 
   
    ![Zadejte podrobnosti o události rozbočovače pro příklad vysílání datového proudu Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "zadejte podrobnosti o události rozbočovače pro příklad vysílání datového proudu Spark")

7. Vyberte **Vytvořit**.
8. Vyberte **zásady sdíleného přístupu** pro obor názvů (Poznámka: není zásady události rozbočovače sdíleného přístupu) a potom vyberte **RootManageSharedAccessKey**.
    
     ![Nastavení centra událostí zásad pro vysílání datového proudu příklad Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "zásady nastavení centra událostí pro vysílání datového proudu příklad Spark")

9. Uložit hodnoty **primární klíč** a **připojovací řetězec primární klíč** používat později v tomto kurzu.

     ![Zobrazit centra událostí zásad klíče pro vysílání datového proudu příklad Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "centra událostí zobrazení zásad klíče pro streamování příklad Spark")


## <a name="send-tweets-to-the-event-hub"></a>Odesílání tweetů do centra událostí

Budete muset vytvořit poznámkového bloku Jupyter a pojmenujte ji **SendTweetsToEventHub**. 

1. Spusťte následující kód k přidání externí knihovny Maven:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Spusťte následující kód k odesílání tweetů do vašeho centra událostí:

    ```
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Otevřete Centrum událostí na portálu Azure.  Na **přehled**, zobrazí se některé grafy znázorňující zprávy odeslané do centra událostí.

## <a name="read-tweets-from-the-event-hub"></a>Čtení tweetů z centra událostí

Musíte vytvořit jiný Poznámkový blok Jupyter a pojmenujte ji **ReadTweetsFromEventHub**. 

1. Spusťte následující kód k přidání externí knihovny Maven:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Spusťte následující kód do čtení tweetů z vašeho centra událostí:

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connectionString).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

S HDInsight data se ukládají v Azure Storage nebo Azure Data Lake Store, takže clusteru můžete bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokud máte v plánu pro práci v dalším kurzu okamžitě, můžete chtít zachovat clusteru.

Otevřete cluster na portálu Azure a vyberte **odstranit**.

![Odstranění clusteru HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "clusteru HDInsight se odstranit")

Můžete také vybrat název skupiny prostředků, které chcete otevřít stránku skupiny prostředků a potom vyberte **odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstranění clusteru HDInsight Spark a výchozí účet úložiště.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

* Čtení zpráv z centra událostí.
Přechodu na další článek a zobrazit tak, že můžete vytvořit machine learning aplikací. 

> [!div class="nextstepaction"]
> [Vytvoření machine learning aplikací](./apache-spark-ipython-notebook-machine-learning.md)


