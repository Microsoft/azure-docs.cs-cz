---
title: 'Kurz: Streamování dat do Azure Databricks pomocí služby Event Hubs | Microsoft Docs'
description: Zjistěte, jak pomocí Azure Databricks se službou Event Hubs ingestovat streamovaná data z Twitteru a jak tato data číst téměř v reálném čase.
services: azure-databricks
documentationcenter: ''
author: lenadroid
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.custom: mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 03/23/2018
ms.author: alehall
ms.openlocfilehash: 94b09b824becc8a67adf4edfd2d4b44496a6169c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-stream-data-into-azure-databricks-using-event-hubs"></a>Kurz: Streamování dat do Azure Databricks pomocí služby Event Hubs

V tomto kurzu propojíte systém pro příjem dat s Azure Databricks, abyste mohli streamovat data do clusteru Apache Spark téměř v reálném čase. Pomocí služby Azure Event Hubs nastavíte systém pro příjem dat a pak ho připojíte k Azure Databricks, aby zpracovával přicházející zprávy. Pro přístup ke streamovaným datům použijete rozhraní Twitter API umožňující ingestování tweetů do služby Event Hubs. Jakmile budete mít data v Azure Databricks, můžete je dále analyzovat spouštěním analytických úloh. 

Na konci tohoto kurzu budete umět streamovat tweety z Twitteru (které obsahují výraz Azure) a číst tyto tweety v Azure Databricks.

Následující obrázek ukazuje běh aplikace:

![Azure Databricks se službou Event Hubs](./media/databricks-stream-from-eventhubs/databricks-eventhubs-tutorial.png "Azure Databricks se službou Event Hubs")

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření twitterové aplikace pro přístup ke streamovaným datům
> * Vytvoření poznámkových bloků v Azure Databricks
> * Připojení knihoven pro službu Event Hubs a rozhraní Twitter API
> * Odeslání tweetů do služby Event Hubs
> * Čtení tweetů ze služby Event Hubs

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, ujistěte se, že splňujete následující požadavky:
- Obor názvů služby Azure Event Hubs.
- Centrum událostí v rámci tohoto oboru názvů.
- Připojovací řetězec pro přístup k oboru názvů služby Event Hubs. Připojovací řetězec by měl mít formát podobný tomuto: `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.
- Název zásady sdíleného přístupu a klíč zásady pro službu Event Hubs.

Tyto požadavky můžete splnit dokončením kroků v článku [Vytvoření oboru názvů služby Azure Event Hubs a centra událostí](../event-hubs/event-hubs-create.md).

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte pomocí portálu Azure pracovní prostor služby Azure Databricks.

1. Na portálu Azure Portal vyberte **Vytvořit prostředek** > **Data + analýzy** > **Azure Databricks**.

    ![Databricks na portálu Azure Portal](./media/databricks-stream-from-eventhubs/azure-databricks-on-portal.png "Databricks na portálu Azure Portal")

3. V části **Služba Azure Databricks** zadejte hodnoty pro vytvoření pracovního prostoru Databricks.

    ![Vytvoření pracovního prostoru služby Azure Databricks](./media/databricks-stream-from-eventhubs/create-databricks-workspace.png "Vytvoření pracovního prostoru služby Azure Databricks")

    Zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Umístění**     | Vyberte **Východní USA 2**. Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).        |
    |**Cenová úroveň**     |  Zvolte úroveň **Standard** nebo **Premium**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

4. Vytvoření účtu trvá několik minut. Během vytváření účtu se na pravé straně portálu zobrazí dlaždice **Odesílání nasazení pro Azure Databricks**. Možná se budete muset posunout do pravé části řídicího panelu, aby se dlaždice zobrazila. V horní části obrazovky se také zobrazí indikátor průběhu. Průběh můžete sledovat v obou oblastech.

    ![Dlaždice nasazení Databricks](./media/databricks-stream-from-eventhubs/databricks-deployment-tile.png "Dlaždice nasazení Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

1. Na portálu Azure Portal přejděte do pracovního prostoru Databricks, který jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Cluster**.

    ![Databricks v Azure](./media/databricks-stream-from-eventhubs/databricks-on-azure.png "Databricks v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/databricks-stream-from-eventhubs/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

    Přijměte všechny výchozí hodnoty kromě následujících:

    * Zadejte název clusteru.
    * Pro účely tohoto článku vytvořte cluster s modulem runtime verze **4.0**.
    * Nezapomeňte zaškrtnout políčko **Terminate after ____ minutes of inactivity** (Ukončit po ____ minutách neaktivity). Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.

    Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Pro příjem streamovaných tweetů je potřeba vytvořit aplikaci na Twitteru. Postupujte podle pokynů k vytvoření aplikace Twitter a poznamenejte si hodnoty, které potřebujete k dokončení tohoto kurzu.

1. Ve webovém prohlížeči přejděte na stránku [Twitter Application Management](http://twitter.com/app) (Správa aplikací Twitter) a vyberte **Create New App** (Vytvořit novou aplikaci).

    ![Vytvoření aplikace Twitter](./media/databricks-stream-from-eventhubs/databricks-create-twitter-app.png "Vytvoření aplikace Twitter")

2. Na stránce **Create an application** (Vytvoření aplikace) zadejte podrobnosti o nové aplikaci a pak vyberte **Create your Twitter application** (Vytvořit aplikaci Twitter).

    ![Podrobnosti o aplikaci Twitter](./media/databricks-stream-from-eventhubs/databricks-provide-twitter-app-details.png "Podrobnosti o aplikaci Twitter")

3. Na stránce aplikace vyberte kartu **Keys and Access Tokens** (Klíče a přístupové tokeny) a zkopírujte hodnoty **Consumer Key** (Uživatelský klíč) a **Consumer Secret** (Uživatelský tajný kód). Kromě toho vyberte **Create my access token** (Vytvořit přístupový token) a vygenerujte přístupové tokeny. Zkopírujte hodnoty **Access Token** (Přístupový token) a **Access Token Secret** (Tajný kód přístupového tokenu).

    ![Podrobnosti o aplikaci Twitter](./media/databricks-stream-from-eventhubs/twitter-app-key-secret.png "Podrobnosti o aplikaci Twitter")

Uložte hodnoty, které jste načetli pro aplikaci Twitter. Tyto hodnoty budete potřebovat v pozdější části kurzu.

## <a name="attach-libraries-to-spark-cluster"></a>Připojení knihoven ke clusteru Spark

V tomto kurzu k odesílání tweetů do služby Event Hubs použijete rozhraní Twitter API. Použijete také [konektor služby Event Hubs pro Apache Spark](https://github.com/Azure/azure-event-hubs-spark) ke čtení a zápisu dat do služby Azure Event Hubs. Pokud chcete tato rozhraní API použít v rámci svého clusteru, přidejte je jako knihovny do Azure Databricks a pak je přidružte ke svému clusteru Spark. Následující pokyny ukazují, jak přidat knihovnu do složky **Sdílené** ve vašem pracovním prostoru.

1.  V pracovním prostoru Azure Databricks vyberte **Pracovní prostor** a pak klikněte pravým tlačítkem na **Sdílené**. V místní nabídce vyberte **Vytvořit** > **Knihovna**.

    ![Dialogové okno Přidat knihovnu](./media/databricks-stream-from-eventhubs/databricks-add-library-option.png "Dialogové okno Přidat knihovnu")

2. Na stránce Nová knihovna jako **Zdroj** vyberte **Souřadnice Maven**. V části **Souřadnice** zadejte souřadnice balíčku, který chcete přidat. Tady jsou souřadnice Maven pro knihovny použité v tomto kurzu:

    * Konektor služby Event Hubs pro Spark – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.1`
    * Rozhraní Twitter API – `org.twitter4j:twitter4j-core:4.0.6`

    ![Zadání souřadnic Maven](./media/databricks-stream-from-eventhubs/databricks-eventhub-specify-maven-coordinate.png "Zadání souřadnic Maven")

3. Vyberte **Vytvořit knihovnu**.

4. Vyberte složku, do které jste knihovnu přidali, a pak vyberte název knihovny.

    ![Výběr knihovny k přidání](./media/databricks-stream-from-eventhubs/select-library.png "Výběr knihovny k přidání")

5. Na stránce knihovny vyberte cluster, ve kterém chcete knihovnu použít. Po úspěšném přidružení knihovny ke clusteru se stav okamžitě změní na **Připojeno**.

    ![Připojení knihovny ke clusteru](./media/databricks-stream-from-eventhubs/databricks-library-attached.png "Připojení knihovny ke clusteru")

6. Zopakujte tyto kroky pro balíček Twitteru `twitter4j-core:4.0.6`.

## <a name="create-notebooks-in-databricks"></a>Vytvoření poznámkových bloků v Databricks

V této části vytvoříte v pracovním prostoru Databricks dva poznámkové bloky s následujícími názvy:

- **SendTweetsToEventHub** –Poznámkový blob producenta, který použijete k získání tweetů z Twitteru a jejich streamování do služby Event Hubs.
- **ReadTweetsFromEventHub** – Poznámkový blok konzumenta, který použijete ke čtení tweetů ze služby Event Hubs.

1. V levém podokně vyberte **Pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v Databricks](./media/databricks-stream-from-eventhubs/databricks-create-notebook.png "Vytvoření poznámkového bloku v Databricks")

2. V dialogovém okně **Vytvořit poznámkový blok** zadejte název **SendTweetsToEventHub**, vyberte jazyk **Scala** a vyberte cluster Spark, který jste vytvořili dříve.

    ![Vytvoření poznámkového bloku v Databricks](./media/databricks-stream-from-eventhubs/databricks-notebook-details.png "Vytvoření poznámkového bloku v Databricks")

    Vyberte **Vytvořit**.

3. Zopakováním těchto kroků vytvořte i poznámkový blok **ReadTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Odeslání tweetů do služby Event Hubs

Do poznámkového bloku **SendTweetsToEventHub** vložte následující kód a nahraďte zástupné hodnoty hodnotami pro váš obor názvů služby Event Hubs a aplikaci Twitter, kterou jste vytvořili dříve. Tento poznámkový blok v reálném čase streamuje tweety s klíčovým slovem Azure do služby Event Hubs.

    import java.util._
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._

    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sendEvent(message: String) = {
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData)
      System.out.println("Sent event: " + message + "\n")
    }

    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter configuration!
    // Replace values below with yours

    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"

    val cb = new ConfigurationBuilder()
      cb.setDebugEnabled(true)
      .setOAuthConsumerKey(twitterConsumerKey)
      .setOAuthConsumerSecret(twitterConsumerSecret)
      .setOAuthAccessToken(twitterOauthAccessToken)
      .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

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

Pokud chcete poznámkový blok spustit, stiskněte **SHIFT + ENTER**. Zobrazí se výstup podobný následujícímu fragmentu kódu. Každá událost ve výstupu představuje tweet, který obsahuje výraz Azure a ingestuje se do služby Event Hubs.

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure

    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie

    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk

    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Čtení tweetů ze služby Event Hubs

Do poznámkového bloku **ReadTweetsFromEventHub** vložte následující kód a nahraďte zástupné hodnoty hodnotami pro vaši službu Azure Event Hubs, kterou jste vytvořili dříve. Tento poznámkový blok čte tweety, které jste předtím streamovali do služby Event Hubs pomocí poznámkového bloku **SendTweetsToEventHub**.

    import org.apache.spark.eventhubs._

    // Build connection string with the above information
    val connectionString = ConnectionStringBuilder("<EVENT HUBS CONNECTION STRING>")
      .setEventHubName("<EVENT HUB NAME>")
      .build

    val customEventhubParameters =
      EventHubsConf(connectionString)
      .setMaxEventsPerTrigger(5)

    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

    incomingStream.printSchema

    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()

Zobrazí se následující výstup:


    root
     |-- body: binary (nullable = true)
     |-- offset: long (nullable = true)
     |-- seqNumber: long (nullable = true)
     |-- enqueuedTime: long (nullable = true)
     |-- publisher: string (nullable = true)
     |-- partitionKey: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+------+--------------+---------------+---------+------------+
    |body  |offset|sequenceNumber|enqueuedTime   |publisher|partitionKey|
    +------+------+--------------+---------------+---------+------------+
    |[50 75 62 6C 69 63 20 70 72 65 76 69 65 77 20 6F 66 20 4A 61 76 61 20 6F 6E 20 41 70 70 20 53 65 72 76 69 63 65 2C 20 62 75 69 6C 74 2D 69 6E 20 73 75 70 70 6F 72 74 20 66 6F 72 20 54 6F 6D 63 61 74 20 61 6E 64 20 4F 70 65 6E 4A 44 4B 0A 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 37 76 73 37 63 4B 74 76 61 68 20 0A 23 63 6C 6F 75 64 63 6F 6D 70 75 74 69 6E 67 20 23 41 7A 75 72 65]                              |0     |0             |2018-03-09 05:49:08.86 |null     |null        |
    |[4D 69 67 72 61 74 65 20 79 6F 75 72 20 64 61 74 61 62 61 73 65 73 20 74 6F 20 61 20 66 75 6C 6C 79 20 6D 61 6E 61 67 65 64 20 73 65 72 76 69 63 65 20 77 69 74 68 20 41 7A 75 72 65 20 53 51 4C 20 44 61 74 61 62 61 73 65 20 4D 61 6E 61 67 65 64 20 49 6E 73 74 61 6E 63 65 20 7C 20 23 41 7A 75 72 65 20 7C 20 23 43 6C 6F 75 64 20 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 73 4A 48 58 4E 34 74 72 44 6B]            |168   |1             |2018-03-09 05:49:24.752|null     |null        |
    +------+------+--------------+---------------+---------+------------+

    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Vzhledem k tomu, že se výstup zobrazuje v binárním režimu, pomocí následujícího fragmentu kódu ho převeďte na řetězec.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so we cast it to string to see the actual content of the message
    val messages =
      incomingStream
      .withColumn("Offset", $"offset".cast(LongType))
      .withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType))
      .withColumn("Timestamp", $"enqueuedTime".cast(LongType))
      .withColumn("Body", $"body".cast(StringType))
      .select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()

Výstup teď vypadá podobně jako následující fragment kódu:

    root
     |-- Offset: long (nullable = true)
     |-- Time (readable): timestamp (nullable = true)
     |-- Timestamp: long (nullable = true)
     |-- Body: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+-----------------+----------+-------+
    |Offset|Time (readable)  |Timestamp |Body
    +------+-----------------+----------+-------+
    |0     |2018-03-09 05:49:08.86 |1520574548|Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure          |
    |168   |2018-03-09 05:49:24.752|1520574564|Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |
    |0     |2018-03-09 05:49:02.936|1520574542|@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|
    |176   |2018-03-09 05:49:20.801|1520574560|4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |
    +------+-----------------+----------+-------+
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

A to je vše! Pomocí Azure Databricks jste úspěšně streamovali data do služby Azure Event Hubs téměř v reálném čase. Následně jste streamovaná data použili pomocí konektoru služby Event Hubs pro Apache Spark.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete cluster ukončit. Pokud to chcete udělat, v levém podokně v pracovním prostoru Azure Databricks vyberte **Clusters** (Clustery). U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions** (Akce) a vyberte ikonu **Terminate** (Ukončit).

![Zastavení clusteru Databricks](./media/databricks-stream-from-eventhubs/terminate-databricks-cluster.png "Zastavení clusteru Databricks")

Pokud cluster neukončíte ručně, zastaví se automaticky za předpokladu, že jste při vytváření clusteru zaškrtli políčko **Ukončit po __ minutách nečinnosti**. V takovém případě se cluster automaticky zastaví, pokud byl po zadanou dobu neaktivní.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření aplikace Twitter generující streamovaná data
> * Vytvoření poznámkových bloků v Azure Databricks
> * Přidání knihoven pro službu Event Hubs a rozhraní Twitter API
> * Odeslání tweetů do služby Event Hubs
> * Čtení tweetů ze služby Event Hubs

Pokračujte k dalšímu kurzu, kde se seznámíte s prováděním analýzy mínění na streamovaných datech pomocí Azure Databricks a [rozhraní API služeb Microsoft Cognitive Services](../cognitive-services/text-analytics/overview.md).

> [!div class="nextstepaction"]
>[Analýza mínění na streamovaných datech pomocí Azure Databricks](databricks-sentiment-analysis-cognitive-services.md)
