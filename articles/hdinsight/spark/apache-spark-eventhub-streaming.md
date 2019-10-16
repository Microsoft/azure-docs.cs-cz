---
title: 'Kurz: zpracování dat z Azure Event Hubs s využitím Apache Spark ve službě HDInsight'
description: Kurz – připojení Apache Spark ve službě Azure HDInsight do Azure Event Hubs a zpracování dat streamování.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: be21b809272a132ee6e63582036c36ad5dcdf4ad
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "71266197"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Kurz: zpracování tweety pomocí Azure Event Hubs a Apache Spark v HDInsight

V tomto kurzu se naučíte, jak vytvořit aplikaci pro streamování [Apache Spark](https://spark.apache.org/) pro odeslání tweety do centra událostí Azure a vytvořit další aplikaci pro čtení tweety z centra událostí. Podrobné vysvětlení streamování Sparku najdete v tématu [Přehled streamování Apache Spark](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight přináší stejné funkce streamování do clusteru Spark v Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Odesílání zpráv do centra událostí Azure
> * Čtení zpráv z centra událostí Azure

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

* Cluster Apache Spark ve službě HDInsight. Viz [Vytvoření clusteru Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Znalost používání poznámkových bloků Jupyter se Sparkem ve službě HDInsight. Další informace najdete v tématech [načtení dat a spuštění dotazů s Apache Spark v HDInsight](./apache-spark-load-data-run-query.md).

* [Účet na Twitteru](https://twitter.com/i/flow/signup).

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Pro příjem streamovaných tweetů je potřeba vytvořit aplikaci na Twitteru. Postupujte podle pokynů k vytvoření aplikace Twitter a zapište hodnoty, které potřebujete k dokončení tohoto kurzu.

1. Přejděte na [Správa aplikací na Twitter](https://apps.twitter.com/).

1. Vyberte **vytvořit novou aplikaci**.

1. Zadejte následující hodnoty:

    |Vlastnost |Hodnota |
    |---|---|
    |Name (Název)|Zadejte název aplikace. Hodnota použitá v tomto kurzu je **HDISparkStreamApp0423**. Tento název musí být jedinečný název.|
    |Popis|Zadejte krátký popis aplikace. Hodnota použitá v tomto kurzu je **Jednoduchá aplikace pro streamování HDInsight Spark**.|
    |Web|Poskytněte web aplikace. Nemusí to být platný web.  Hodnota použitá v tomto kurzu je `http://www.contoso.com`.|
    |Adresa URL zpětného volání|Můžete ponechat prázdné.|

1. Vyberte **Ano, jsem si přečetl (a) jsem na Twitteru a souhlasím se smlouvou pro vývojáře na Twitteru**a pak vyberte **vytvořit aplikaci Twitter**.

1. Vyberte kartu **klíče a přístupové tokeny** .

1. Na konci stránky vyberte **vytvořit token přístupu** .

1. Zapište ze stránky následující hodnoty.  Tyto hodnoty budete potřebovat později v tomto kurzu:

    - **Klíč příjemce (klíč rozhraní API)**    
    - **Tajný klíč uživatele (tajný kód rozhraní API)**  
    - **Přístupový token**
    - **Tajný klíč přístupového tokenu**   

## <a name="create-an-azure-event-hubs-namespace"></a>Vytvoření oboru názvů Azure Event Hubs

Toto centrum událostí se používá k ukládání tweety.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). 

2. V nabídce vlevo vyberte **všechny služby**.  

3. V části **Internet věcí**vyberte **Event Hubs**. 

    ![Vytvoření centra událostí pro Spark Stream příklad](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "vytvoření centra událostí pro streamování Sparku – příklad")

4. Vyberte **+ Přidat**.

5. Zadejte následující hodnoty pro nový obor názvů Event Hubs:

    |Vlastnost |Hodnota |
    |---|---|
    |Name (Název)|Zadejte název centra událostí.  Hodnota použitá v tomto kurzu je **myeventhubns20180403**.|
    |Cenová úroveň|Vyberte **Standard**.|
    |Předplatné|Vyberte odpovídající předplatné.|
    |Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit novou** a vytvořte novou skupinu prostředků.|
    |Umístění|Pokud chcete snížit latenci a náklady, vyberte stejné **umístění** jako cluster Apache Spark v HDInsight.|
    |Povolit automatické rozploché (volitelné) |Automatické rozploché automaticky škálují počet jednotek propustnosti přiřazených k vašemu oboru názvů Event Hubs, když váš provoz přesáhne kapacitu jednotek propustnosti, které jsou jim přiřazeny.  |
    |Automaticky plochý maximální počet jednotek propustnosti (volitelné)|Tento posuvník se zobrazí pouze v případě, že je zaškrtnuto zaškrtávací políčka **Povolit automatické rozplochení**.  |

    ![Zadejte název centra událostí pro streamování Sparku]. příklad(./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "poskytuje název centra událostí pro streamování Sparku") .

6. Vyberte **vytvořit** a vytvořte obor názvů.  Nasazení bude dokončeno během několika minut.

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure
Po nasazení Event Hubs oboru názvů vytvořte centrum událostí.  Z portálu:

1. V nabídce vlevo vyberte **všechny služby**.  

1. V části **Internet věcí**vyberte **Event Hubs**.  

1. Ze seznamu vyberte svůj obor názvů Event Hubs.  

1. Na stránce **Event Hubs obor názvů** vyberte **+ centrum událostí**.  
1. Na stránce **vytvořit centrum událostí** zadejte následující hodnoty:

    - **Název**: zadejte název centra událostí. 
 
    - **Počet oddílů**: 10.  

    - **Uchovávání zpráv**: 1.   
   
      ![Příklad zadání podrobností centra událostí pro streamování Spark příklad](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "poskytnutí podrobností centra událostí pro streamování Sparku")

1. Vyberte **Create** (Vytvořit).  Nasazení by mělo být dokončeno během několika sekund a bude vráceno na stránku Event Hubs oboru názvů.

1. V části **Nastavení**vyberte **zásady sdíleného přístupu**.

1. Vyberte **RootManageSharedAccessKey**.
    
     ![Nastavení zásad centra událostí pro příklad streamování Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "nastavení zásad centra událostí pro příklad streamování Sparku")

1. Uložte hodnoty **primárního klíče** a **připojovacího řetězce – primární klíč** , který použijete později v tomto kurzu.

     ![Podívejte se na klíče zásad centra událostí pro streamování pro Spark příklad](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "zobrazení klíčů zásad centra událostí pro streamování Sparku.")


## <a name="send-tweets-to-the-event-hub"></a>Odeslání tweety do centra událostí

Vytvořte Poznámkový blok Jupyter a pojmenujte ho **SendTweetsToEventHub**. 

1. Spusťte následující kód a přidejte externí knihovny Apache Maven:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Níže uvedený kód nahraďte `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`, `<ACCESS TOKEN>` a `<TOKEN SECRET>` s příslušnými hodnotami. Spusťte upravený kód pro odeslání tweety do centra událostí:

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

3. Otevřete centrum událostí v Azure Portal.  V **přehledu**se zobrazí některé grafy znázorňující zprávy odeslané do centra událostí.

## <a name="read-tweets-from-the-event-hub"></a>Čtení tweety z centra událostí

Vytvořte další Poznámkový blok Jupyter a pojmenujte ho **ReadTweetsFromEventHub**. 

1. Spuštěním následujícího kódu přidejte externí knihovnu Apache Maven:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. Níže uvedený kód nahraďte `<Event hub name>` a `<Event hub namespace connection string>` s příslušnými hodnotami. Spusťte upravený kód pro čtení tweety z centra událostí:

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

Ve službě HDInsight jsou vaše data uložená ve Azure Storage nebo Azure Data Lake Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Pokud plánujete, že budete v dalším kurzu pracovat okamžitě, můžete chtít zachovat cluster, jinak pokračovat a odstranit cluster.

Otevřete cluster na webu Azure Portal a vyberte **Odstranit**.

![Portál HDInsight Azure odstranění clusteru](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Odstranit cluster HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight Spark i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit aplikaci pro streamování Apache Spark pro odeslání tweety do centra událostí Azure a vytvořit další aplikaci pro čtení tweety z centra událostí.  V dalším článku se dozvíte, jak můžete vytvořit aplikaci Machine Learning.

> [!div class="nextstepaction"]
> [Vytvoření aplikace Machine Learning](./apache-spark-ipython-notebook-machine-learning.md)
