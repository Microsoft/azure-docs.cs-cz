---
title: 'Kurz: Zpracování dat ze služby Azure Event Hubs s Apache Sparkem v Azure HDInsight '
description: Připojení k Azure Event Hubs Apache Spark v Azure HDInsight a zpracovávat streamovaná data.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: 0bdcc253a57fb55d610d67acd9b6a50182a699e3
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257852"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Kurz: Zpracování tweetů pomocí Azure Event Hubs a Apache Spark v HDInsight

V tomto kurzu se dozvíte, jak vytvořit [Apache Spark](https://spark.apache.org/) streamování aplikaci k odesílání tweetů do služby Azure event hub a vytvořte jinou aplikaci ke čtení tweetů ze služby event hub. Podrobné vysvětlení streamování Sparku, najdete v článku [přehled streamování Apache Sparku](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight přináší stejné funkce streamování na clusteru Spark v Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Odesílání zpráv do centra událostí Azure
> * Čtení zpráv z centra událostí Azure

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Zobrazit [vytvořit cluster Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Znalost používání poznámkových bloků Jupyter se Sparkem ve službě HDInsight. Další informace najdete v tématu [načtení dat a spouštění dotazů s Apache Spark v HDInsight](./apache-spark-load-data-run-query.md).

* A [Twitterovém účtu](https://twitter.com/i/flow/signup).

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Pro příjem streamovaných tweetů je potřeba vytvořit aplikaci na Twitteru. Postupujte podle pokynů k vytvoření aplikace Twitter a poznamenejte si hodnoty, které potřebujete k dokončení tohoto kurzu.

1. Přejděte do [Twitter Application Management](https://apps.twitter.com/).

1. Vyberte **vytvořte novou aplikaci**.

1. Zadejte následující hodnoty:

    |Vlastnost |Hodnota |
    |---|---|
    |Název|Zadejte název aplikace. Hodnota použitá pro účely tohoto kurzu je **HDISparkStreamApp0423**. Tento název musí být jedinečný název.|
    |Popis|Zadejte krátký popis aplikace. Hodnota použitá pro účely tohoto kurzu je **jednoduché HDInsight Spark streaming aplikace**.|
    |Web|Poskytují aplikace webu. Nemusí být platný web.  Hodnota použitá pro účely tohoto kurzu je **http://www.contoso.com** .|
    |Adresa URL zpětného volání|Můžete jej můžete nechat prázdné.|

1. Vyberte **Ano, jsem přečtení a souhlas se smlouvou o pro vývojáře na Twitteru**a pak vyberte **vytvoření aplikace Twitter**.

1. Vyberte **klíče a přístupové tokeny** kartu.

1. Vyberte **vytvořit můj přístupový token** na konci stránky.

1. Poznamenejte si tyto hodnoty ze stránky.  Tyto hodnoty budete potřebovat později v tomto kurzu:

    - **Uživatelský klíč (klíč rozhraní API)**    
    - **Uživatelský tajný klíč (tajný klíč rozhraní API)**  
    - **Přístupový Token**
    - **Tajný klíč přístupového tokenu**   

## <a name="create-an-azure-event-hubs-namespace"></a>Vytvoření oboru názvů Azure Event Hubs

Ukládat tweety pomocí centra událostí.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. V nabídce vlevo vyberte **všechny služby**.  

3. V části **INTERNET OF THINGS**vyberte **Event Hubs**. 

    ![Vytvoření centra událostí pro Spark streaming příklad](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "vytvořit Centrum událostí pro příklad streamování Sparku")

4. Vyberte **+ Přidat**.

5. Zadejte následující hodnoty pro nový obor názvů služby Event Hubs:

    |Vlastnost |Hodnota |
    |---|---|
    |Název|Zadejte název centra událostí.  Hodnota použitá pro účely tohoto kurzu je **myeventhubns20180403**.|
    |Cenová úroveň|Vyberte **standardní**.|
    |Předplatné|Vyberte odpovídající předplatné.|
    |Skupina prostředků|Z rozevíracího seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit nový** vytvořit novou skupinu prostředků.|
    |Location|Vyberte stejné **umístění** jako cluster Apache Spark v HDInsight pro snížení latence a nákladů.|
    |Povolit automatické rozšiřování (volitelné) |Automatické rozšiřování automatické škálování počtu jednotek propustnosti při provozu překračuje kapacitu jednotek propustnosti, které jsou přiřazeny k němu přiřazen k vaší Namespace Event Hubs.  |
    |Automatické rozšiřování maximální počet jednotek propustnosti (volitelné)|Tento posuvník se zobrazí, pouze pokud zaškrtnete **povolit automatické rozšiřování**.  |

    ![Zadejte název centra událostí pro Spark streaming příklad](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "zadejte název centra událostí pro příklad streamování Sparku")

6. Vyberte **vytvořit** k vytvoření oboru názvů.  Nasazení se dokončí za pár minut.

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure
Vytvoření centra událostí po nasazení oboru názvů Event Hubs.  Z portálu:

1. V nabídce vlevo vyberte **všechny služby**.  

1. V části **INTERNET OF THINGS**vyberte **Event Hubs**.  

1. Zvolte svůj obor názvů služby Event Hubs ze seznamu.  

1. Z **Event Hubs Namespace** stránce **+ Centrum událostí**.  
1. Zadejte následující hodnoty **vytvořit Centrum událostí** stránky:

    - **Název**: Zadejte název centra událostí. 
 
    - **Počet oddílů**: 10.  

    - **Uchovávání zpráv**: 1.   
   
      ![Zadejte podrobnosti o Centru událostí pro Spark streaming příklad](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "poskytují podrobnosti o Centru událostí pro příklad streamování Sparku")

1. Vyberte **Vytvořit**.  Nasazení by se měla dokončit během několika sekund a budete přesměrováni zpět na stránku pro Event Hubs Namespace.

1. V části **nastavení**vyberte **zásady sdíleného přístupu**.

1. Vyberte **RootManageSharedAccessKey**.
    
     ![Nastavení zásad centra událostí pro Spark streaming příklad](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "zásady nastavení centra událostí pro Spark streaming příklad")

1. Uložit hodnoty **primární klíč** a **připojovací řetězec – primární klíč** použít později v tomto kurzu.

     ![Zobrazení klíče zásad centra událostí pro Spark streaming příklad](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "zásady centra událostí zobrazení klíče příklad streamování Sparku")


## <a name="send-tweets-to-the-event-hub"></a>Odeslání tweetů do centra událostí

Vytvoření poznámkového bloku Jupyter a pojmenujte ho **SendTweetsToEventHub**. 

1. Spusťte následující kód k přidání externí knihovny Apache Maven:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Úpravy kódu níže tak, že nahradíte `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`, `<ACCESS TOKEN>`, a `<TOKEN SECRET>` příslušnými hodnotami. Spusťte upravený kód k odesílání tweetů do vašeho centra událostí:

    ```scala
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

Vytvořte další Poznámkový blok Jupyter s názvem **ReadTweetsFromEventHub**. 

1. Spusťte následující kód k přidání externí knihovny Apache Maven:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```

2. Úpravy kódu níže tak, že nahradíte `<Event hub name>`, a `<Event hub namespace connection string>` příslušnými hodnotami. Spusťte upravený kód ke čtení tweetů ze služby event hub:

    ```scala
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

S HDInsight vaše data uložená ve službě Azure Storage nebo Azure Data Lake Storage, takže je můžete clusteru bezpečně odstranit, až nebude používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Pokud budete chtít pracovat v dalším kurzu okamžitě, můžete chtít zachovat clusteru, v opačném případě pokračujte a cluster odstranit.

Otevřete cluster na webu Azure Portal a vyberte **Odstranit**.

![Odstranění clusteru HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight Spark i výchozí účet úložiště.

## <a name="next-steps"></a>Další postup

V tomto kurzu se dozvěděli, jak vytvořit Apache Spark streaming aplikaci k odesílání tweetů do služby Azure event hub a vytvořit jinou aplikaci ke čtení tweetů ze služby event hub.  Přejděte k dalšímu článku chcete podívat, jak že můžete vytvořit služby machine learning aplikací.

> [!div class="nextstepaction"]
> [Vytvoření služby machine learning aplikací](./apache-spark-ipython-notebook-machine-learning.md)