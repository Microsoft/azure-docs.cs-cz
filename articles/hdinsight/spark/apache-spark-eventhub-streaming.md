---
title: 'Kurz: Zpracování dat ze služby Azure Event Hubs s Apache Sparkem v Azure HDInsight '
description: Připojení k Azure Event Hubs Apache Spark v Azure HDInsight a zpracovávat streamovaná data.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive,mvc
ms.topic: conceptual
ms.date: 06/14/2018
ms.openlocfilehash: 27c8a51ee3f0274489041f4dafbbf73d906e2fa4
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617632"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>Kurz: Proces tweetuje pomocí Azure Event Hubs a Spark v HDInsight

V tomto kurzu se dozvíte, jak vytvořit Apache Spark streaming aplikaci k odesílání tweetů do služby Azure event hub a vytvořte jinou aplikaci ke čtení tweetů ze služby event hub. Podrobné vysvětlení streamování Sparku, najdete v článku [přehled streamování Apache Sparku](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight přináší stejné funkce streamování na clusteru Spark v Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Odesílání zpráv do centra událostí Azure
> * Čtení zpráv z centra událostí Azure

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **Dokončete článek [Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Pro příjem streamovaných tweetů je potřeba vytvořit aplikaci na Twitteru. Postupujte podle pokynů k vytvoření aplikace Twitter a poznamenejte si hodnoty, které potřebujete k dokončení tohoto kurzu.

1. Přejděte do [Twitter Application Management](https://apps.twitter.com/).
2. Vyberte **vytvořte novou aplikaci**.
3. Zadejte následující hodnoty:

    - Název: Zadejte název aplikace. Hodnota použitá pro účely tohoto kurzu je **HDISparkStreamApp0423**. Tento název musí být jedinečný název.
    - Popis: Zadejte stručný popis aplikace. Hodnota použitá pro účely tohoto kurzu je **jednoduché HDInsight Spark streaming aplikace**.
    - Webový server: Zadejte aplikace webu. Nemusí být platný web.  Hodnota použitá pro účely tohoto kurzu je **http://www.contoso.com**.
    - Adresa URL zpětného volání: jste jej můžete nechat prázdné.

4. Vyberte **Ano, jsem přečtení a souhlas se smlouvou o pro vývojáře na Twitteru**a pak vyberte **vytvoření aplikace Twitter**.
5. Vyberte **klíče a přístupové tokeny** kartu.
6. Vyberte **vytvořit můj přístupový token** na konci stránky.
7. Poznamenejte si tyto hodnoty ze stránky.  Tyto hodnoty budete potřebovat později v tomto kurzu:

    - **Uživatelský klíč (klíč rozhraní API)**    
    - **Uživatelský tajný klíč (tajný klíč rozhraní API)**  
    - **Přístupový Token**
    - **Tajný klíč přístupového tokenu**   

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure

Ukládat tweety pomocí centra událostí.

1. Přihlaste se k [Portálu Azure](https://ms.portal.azure.com).
2. Vyberte **vytvořit prostředek** v levém horním rohu obrazovky.
3. Vyberte **Internet of Things**a pak vyberte **Event Hubs**.

    ![Vytvoření centra událostí pro Spark streaming příklad](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "vytvořit Centrum událostí pro příklad streamování Sparku")
4. Zadejte následující hodnoty pro nový obor názvů centra událostí:

    - **Název**: Zadejte název centra událostí.  Hodnota použitá pro účely tohoto kurzu je **myeventhubns20180403**.
    - **Cenová hladina**: vyberte **standardní**.
    - **Skupina prostředků**: máte možnost vytvořit novou nebo vyberte skupinu prostředků clusteru Spark. 
    - **Umístění**: Vyberte stejné **umístění** jako cluster Apache Spark v HDInsight pro snížení latence a nákladů.

    ![Zadejte název centra událostí pro Spark streaming příklad](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "zadejte název centra událostí pro příklad streamování Sparku")
5. Vyberte **vytvořit** k vytvoření oboru názvů.

6. Otevřete obor názvů centra událostí pomocí následujících pokynů:

    1. Z portálu, vyberte **všechny služby**.
    2. V dialogovém okně Filtr zadejte **služby event hubs**.
    3. Dvakrát klikněte na obor názvů, který jste vytvořili.
    4. Vyberte **+ Centrum událostí**.

6. V seznamu Event Hubs oboru názvů vyberte nově vytvořený obor názvů.      
5. Vyberte **Event Hubs**a pak vyberte **+ Centrum událostí** vytvořit nové Centrum událostí.
  

6. Zadejte následující hodnoty:

    - Název: Zadejte název centra událostí.
    - Počet oddílů: 10
    - Uchovávání zpráv: 1. 
   
    ![Zadejte podrobnosti o Centru událostí pro Spark streaming příklad](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "poskytují podrobnosti o Centru událostí pro příklad streamování Sparku")

7. Vyberte **Vytvořit**.
8. Vyberte **zásady sdíleného přístupu** pro obor názvů (Poznámka: není zásady přístupu sdílené centra událostí) a pak vyberte **RootManageSharedAccessKey**.
    
     ![Nastavení zásad centra událostí pro Spark streaming příklad](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "zásady nastavení centra událostí pro Spark streaming příklad")

9. Uložit hodnoty **primární klíč** a **připojovací řetězec – primární klíč** použít později v tomto kurzu.

     ![Zobrazení klíče zásad centra událostí pro Spark streaming příklad](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "zásady centra událostí zobrazení klíče příklad streamování Sparku")


## <a name="send-tweets-to-the-event-hub"></a>Odeslání tweetů do centra událostí

Je potřeba vytvořit poznámkový blok Jupyter a pojmenujte ho **SendTweetsToEventHub**. 

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

3. Otevřete Centrum událostí na webu Azure Portal.  Na **přehled**, měly by se zobrazit několik grafů zobrazujících zprávy odeslané do centra událostí.

## <a name="read-tweets-from-the-event-hub"></a>Čtení tweetů ze služby event hub

Je potřeba vytvořit jiný Poznámkový blok Jupyter a pojmenujte ho **ReadTweetsFromEventHub**. 

1. Spusťte následující kód k přidání externí knihovny Maven:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Spusťte následující kód ke čtení tweetů ze služby event hub:

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
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

V případě služby HDInsight se vaše data ukládají ve službě Azure Storage nebo Azure Data Lake Store, takže můžete cluster bezpečně odstranit, když se nepoužívá. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokud se chystáte hned začít pracovat na dalším kurzu, měli byste cluster zachovat.

Otevřete cluster na webu Azure Portal a vyberte **Odstranit**.

![Odstranění clusteru HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight Spark i výchozí účet úložiště.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

* Čtení zpráv z centra událostí.
Přejděte k dalšímu článku chcete podívat, jak že můžete vytvořit služby machine learning aplikací. 

> [!div class="nextstepaction"]
> [Vytvoření služby machine learning aplikací](./apache-spark-ipython-notebook-machine-learning.md)


