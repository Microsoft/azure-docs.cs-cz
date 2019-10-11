---
title: Použití Azure Databricks pro analýzu mínění
description: Naučte se používat Azure Databricks s rozhraním API Event Hubs a Cognitive Services ke spouštění analýzy mínění dat streamování prakticky v reálném čase.
services: azure-databricks
author: lenadroid
ms.author: alehall
ms.reviewer: mamccrea
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 07/29/2019
ms.openlocfilehash: 1d8b3aad3104f07f8f6499c88f00328c95047816
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274221"
---
# <a name="tutorial-sentiment-analysis-on-streaming-data-using-azure-databricks"></a>Kurz: analýza mínění dat streamování pomocí Azure Databricks

V tomto kurzu se naučíte, jak spustit analýzu mínění pro datový proud dat pomocí Azure Databricks téměř v reálném čase. Pomocí služby Azure Event Hubs nastavíte systém pro příjem dat. Zprávy z Event Hubs můžete využívat Azure Databricks pomocí konektoru Spark Event Hubs. Nakonec použijete rozhraní API pro rozpoznávání služby ke spuštění analýzy mínění dat v datových proudech.

Po konci tohoto kurzu budete mít datový proud tweety z Twitteru, který má v nich výraz "Azure", a spustili jste mínění analýzu na tweety.

Následující ilustrace znázorňuje tok aplikace:

![Azure Databricks s Event Hubs a Cognitive Services](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-tutorial.png "Azure Databricks s Event Hubs a Cognitive Services")

Tento kurz se zabývá následujícími úlohami:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření aplikace Twitter pro přístup k datovým proudům
> * Vytvoření poznámkových bloků v Azure Databricks
> * Připojit knihovny pro Event Hubs a rozhraní Twitter API
> * Vytvoření účtu Cognitive Services a načtení přístupového klíče
> * Odeslat tweety do Event Hubs
> * Číst tweety z Event Hubs
> * Spuštění analýzy mínění na tweety

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=sparkeventhubs-docs-alehall) před tím, než začnete.

> [!Note]
> Tento kurz se nedá provést pomocí **předplatného Azure free zkušební verze**.
> Pokud chcete před vytvořením clusteru použít bezplatný Azure Databricks účet, přejděte do svého profilu a změňte předplatné na **průběžné platby**. Další informace najdete v tématu [bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=sparkeventhubs-docs-alehall).

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, ujistěte se, že splňujete následující požadavky:
- Obor názvů Azure Event Hubs.
- Centrum událostí v rámci oboru názvů.
- Připojovací řetězec pro přístup k oboru názvů Event Hubs. Připojovací řetězec by měl mít formát podobný `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.
- Název zásady sdíleného přístupu a klíč zásad pro Event Hubs.

Tyto požadavky můžete splnit provedením kroků v článku [Vytvoření oboru názvů Azure Event Hubs a centra událostí](../event-hubs/event-hubs-create.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlaste se k Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall).

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte Azure Databricks pracovní prostor pomocí Azure Portal.

1. V Azure Portal vyberte **vytvořit prostředek** > **data a analýzy** > **Azure Databricks**.

    ![Datacihly na Azure Portal](./media/databricks-sentiment-analysis-cognitive-services/azure-databricks-on-portal.png "datacihly na Azure Portal")

3. V části **služba Azure Databricks**zadejte hodnoty pro vytvoření pracovního prostoru datacihly.

    ![Vytvoření pracovního prostoru Azure Databricks](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-workspace.png "vytvoření pracovního prostoru Azure Databricks")

    Zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru datacihly.        |
    |**Formě**     | V rozevíracím seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, zda chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace najdete v tématu [Přehled skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Poloha**     | Vyberte **východní USA 2**. Další dostupné oblasti najdete v tématu [služby Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/regions/services/?WT.mc_id=sparkeventhubs-docs-alehall).        |
    |**Cenová úroveň**     |  Vyberte si z **úrovně Standard** nebo **Premium**. Další informace o těchto úrovních najdete na stránce s [cenami pro datacihly](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall).       |

    Vyberte **Připnout na řídicí panel** a pak vyberte **vytvořit**.

4. Vytváření účtů trvá několik minut. Během vytváření účtu se na pravé straně portálu zobrazí dlaždice **odeslání nasazení pro Azure Databricks** . Možná budete muset přejít na řídicí panel Doprava, aby se dlaždice zobrazila. V horní části obrazovky se zobrazí také indikátor průběhu. Pro průběh můžete sledovat obě oblasti.

    Dlaždice nasazení datacihly ![dlaždice](./media/databricks-sentiment-analysis-cognitive-services/databricks-deployment-tile.png "nasazení datacihly")

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark v datacihlách

1. V Azure Portal přejdete do pracovního prostoru datacihly, který jste vytvořili, a pak vyberete **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **cluster**.

    ![Datacihly v Azure](./media/databricks-sentiment-analysis-cognitive-services/databricks-on-azure.png "datacihlách v Azure")

3. Na stránce **nový cluster** zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru datacihly Spark v Azure](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-spark-cluster.png "Vytvoření clusteru datacihly Spark v Azure")

    Přijměte všechny ostatní výchozí hodnoty kromě následujících:

   * Zadejte název clusteru.
   * V tomto článku vytvořte cluster s modulem runtime **5,2** .
   * Ujistěte se, že jste zaškrtli políčko **ukončit po \_ @ no__t – 2 minuty nečinnosti** . Zadejte dobu (v minutách), po kterou se má cluster ukončit, pokud se cluster nepoužívá.

   Vyberte pracovní proces clusteru a velikost uzlu ovladače vhodné pro vaše technická kritéria a [rozpočet](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall).

     Vyberte **vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Pro příjem datového proudu tweety vytvoříte aplikaci na Twitteru. Postupujte podle pokynů k vytvoření aplikace Twitter a poznamenejte si hodnoty, které potřebujete k dokončení tohoto kurzu.

1. Ve webovém prohlížeči klikněte na [Twitter pro vývojáře](https://developer.twitter.com/en/apps)a vyberte **vytvořit aplikaci**. Může se zobrazit zpráva oznamující, že je potřeba požádat o vývojářský účet pro Twitter. Nebojte se tak a po schválení vaší aplikace by se měl zobrazit potvrzovací e-mail. Schválení pro vývojářský účet může trvat několik dní.

    Potvrzení ![účtu vývojáře Twitteru potvrzení]účtu pro(./media/databricks-sentiment-analysis-cognitive-services/databricks-twitter-dev-confirmation.png "vývojáře Twitteru")

2. Na stránce **vytvořit aplikaci** zadejte podrobnosti nové aplikace a pak vyberte **vytvořit aplikaci Twitter**.

    Podrobnosti ![o aplikaci Twitter]podrobnosti(./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details.png "aplikace Twitter")

    Podrobnosti ![o aplikaci Twitter]podrobnosti(./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details-create.png "aplikace Twitter")

3. Na stránce aplikace vyberte kartu **klíče a tokeny** a zkopírujte hodnoty **klíč rozhraní API příjemce** a **tajného klíče rozhraní API příjemce**. Pokud chcete generovat přístupové tokeny, vyberte také možnost **vytvořit** v části **přístupový token a tajný klíč přístupového tokenu** . Zkopírujte hodnoty **přístupového tokenu** a **tajného klíče přístupového tokenu**.

    Podrobnosti ![o aplikaci Twitter]podrobnosti(./media/databricks-sentiment-analysis-cognitive-services/twitter-app-key-secret.png "aplikace Twitter")

Uložte hodnoty, které jste načetli pro aplikaci Twitter. Hodnoty budete potřebovat později v tomto kurzu.

## <a name="attach-libraries-to-spark-cluster"></a>Připojit knihovny ke clusteru Spark

V tomto kurzu odešlete tweety do Event Hubs pomocí rozhraní Twitter API. Ke čtení a zápisu dat do služby Azure Event Hubs se používá také [konektor Apache Spark Event Hubs](https://github.com/Azure/azure-event-hubs-spark?WT.mc_id=sparkeventhubs-docs-alehall) . Pokud chcete tato rozhraní API používat jako součást clusteru, přidejte je jako knihovny, abyste je Azure Databricks a přidružte je k vašemu clusteru Spark. Následující pokyny ukazují, jak přidat knihovnu.

1. V pracovním prostoru Azure Databricks vyberte **clustery**a vyberte svůj stávající cluster Spark. V nabídce cluster vyberte možnost **knihovny** a klikněte na možnost **nainstalovat novou**.

   ![Dialogové okno Přidat knihovnu](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-locate-cluster.png "Přidání knihovny najít cluster")

   ![Přidat knihovnu – dialogové okno](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-install-new.png "přidat knihovnu nainstalovat novou")

2. Na stránce Nová knihovna vyberte v části **zdroj** možnost **Maven**. Pro **souřadnici**klikněte na **Vyhledat balíčky** pro balíček, který chcete přidat. Tady jsou souřadnice Maven pro knihovny používané v tomto kurzu:

   * Konektor Spark Event Hubs – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Rozhraní Twitter API-`org.twitter4j:twitter4j-core:4.0.7`

     ![Zadání souřadnic Maven](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search.png "poskytují souřadnice Maven")

     ![Zadání](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search-dialogue.png "souřadnic Maven hledání") souřadnic Maven

3. Vyberte **nainstalovat**.

4. V nabídce cluster zajistěte, aby byly správně nainstalovány a připojeny obě knihovny.

    ![Kontrolovat knihovny](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-check.png "kontroly") knihoven

6. Opakujte tento postup pro balíček Twitteru `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Získat Cognitive Services přístupový klíč

V tomto kurzu použijete [rozhraní API služby Azure Cognitive Services analýza textu](../cognitive-services/text-analytics/overview.md) ke spouštění analýzy mínění pro datový proud tweety téměř v reálném čase. Předtím, než použijete rozhraní API, musíte vytvořit účet Azure Cognitive Services v Azure a načíst přístupový klíč pro použití rozhraní API Analýza textu.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall).

2. Vyberte **+ vytvořit prostředek**.

3. V části Azure Marketplace vyberte **AI a Cognitive Services** > **rozhraní API pro analýzu textu**.

    ![Vytvoření účtu služby]pro rozpoznávání účtů vytvoření účtu služby pro(./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-text-api.png "rozpoznávání")

4. V dialogovém okně **vytvořit** zadejte následující hodnoty:

    ![Vytvoření účtu služby]pro rozpoznávání účtů vytvoření účtu služby pro(./media/databricks-sentiment-analysis-cognitive-services/create-cognitive-services-account.png "rozpoznávání")

   - Zadejte název účtu Cognitive Services.
   - Vyberte předplatné Azure, ve kterém se účet vytvoří.
   - Vyberte umístění Azure.
   - Vyberte cenovou úroveň služby. Další informace o cenách Cognitive Services najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/?WT.mc_id=sparkeventhubs-docs-alehall).
   - Určete, jestli chcete vytvořit novou skupinu prostředků, nebo vyberte existující.

     Vyberte **vytvořit**.

5. Po vytvoření účtu vyberte na kartě **Přehled** možnost **Zobrazit přístupové klíče**.

    ![Zobrazit]přístupové klíče(./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-get-access-keys.png "zobrazení") přístupových klíčů

    Zkopírujte také část adresy URL koncového bodu, jak je znázorněno na snímku obrazovky. Tuto adresu URL budete potřebovat v tomto kurzu.

6. V části **spravovat klíče**vyberte ikonu kopírování na klíč, který chcete použít.

    ![Kopírování přístupových klíčů](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-copy-access-keys.png "kopírování přístupových") klíčů

7. Uložte hodnoty adresy URL koncového bodu a přístupového klíče, který jste získali v tomto kroku. Budete ho potřebovat později v tomto kurzu.

## <a name="create-notebooks-in-databricks"></a>Vytvoření poznámkových bloků v datacihlách

V této části vytvoříte v pracovním prostoru datacihly dva poznámkové bloky s následujícími názvy.

- **SendTweetsToEventHub** – Poznámkový blok pro výrobce, který použijete k získání tweety z Twitteru a jeho streamování do Event Hubs.
- **AnalyzeTweetsFromEventHub** – uživatelský Poznámkový blok, který použijete ke čtení tweety z Event Hubs a spuštění analýzy mínění.

1. V levém podokně vyberte **pracovní prostor**. V rozevíracím seznamu **pracovní prostor** vyberte **vytvořit**a pak vyberte **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datacihlách](./media/databricks-sentiment-analysis-cognitive-services/databricks-create-notebook.png "vytvoření poznámkového bloku v datacihlech")

2. V dialogovém okně **vytvořit Poznámkový blok** zadejte **SendTweetsToEventHub**, jako jazyk vyberte **Scala** a vyberte cluster Spark, který jste vytvořili dříve.

    ![Vytvoření poznámkového bloku v datacihlách](./media/databricks-sentiment-analysis-cognitive-services/databricks-notebook-details.png "vytvoření poznámkového bloku v datacihlech")

    Vyberte **vytvořit**.

3. Opakujte postup pro vytvoření poznámkového bloku **AnalyzeTweetsFromEventHub** .

## <a name="send-tweets-to-event-hubs"></a>Odeslat tweety do Event Hubs

Do poznámkového bloku **SendTweetsToEventHub** vložte následující kód a nahraďte zástupné hodnoty hodnotami pro váš obor názvů Event Hubs a aplikaci Twitter, kterou jste vytvořili dříve. Tyto streamy poznámkových bloků tweety s klíčovým slovem "Azure" do Event Hubs v reálném čase.

> [!NOTE]
> Rozhraní Twitter API má určitá omezení požadavků a [kvóty](https://developer.twitter.com/en/docs/basics/rate-limiting.html). Pokud nesplňujete standardní omezení rychlosti v rozhraní Twitter API, můžete v tomto příkladu vygenerovat textový obsah bez použití rozhraní Twitter API. Chcete-li to provést, nastavte proměnnou **DataSource** na `test` namísto `twitter` a naplňte seznam **testSource** s preferovaným vstupem testu.

```scala
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._
    import scala.collection.immutable._
    import scala.concurrent.Future
    import scala.concurrent.ExecutionContext.Implicits.global

    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val pool = Executors.newScheduledThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sleep(time: Long): Unit = Thread.sleep(time)

    def sendEvent(message: String, delay: Long) = {
      sleep(delay)
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData)
      System.out.println("Sent event: " + message + "\n")
    }

    // Add your own values to the list
    val testSource = List("Azure is the greatest!", "Azure isn't working :(", "Azure is okay.")

    // Specify 'test' if you prefer to not use Twitter API and loop through a list of values you define in `testSource`
    // Otherwise specify 'twitter'
    val dataSource = "test"

    if (dataSource == "twitter") {

      import twitter4j._
      import twitter4j.TwitterFactory
      import twitter4j.Twitter
      import twitter4j.conf.ConfigurationBuilder

      // Twitter configuration!
      // Replace values below with you

      val twitterConsumerKey = "<CONSUMER API KEY>"
      val twitterConsumerSecret = "<CONSUMER API SECRET>"
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
            sendEvent(status.getText(), 5000)
          }
          lowestStatusId = Math.min(status.getId(), lowestStatusId)
        }
        query.setMaxId(lowestStatusId - 1)
      }

    } else if (dataSource == "test") {
      // Loop through the list of test input data
      while (true) {
        testSource.foreach {
          sendEvent(_,5000)
        }
      }

    } else {
      System.out.println("Unsupported Data Source. Set 'dataSource' to \"twitter\" or \"test\"")
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
```

Chcete-li spustit Poznámkový blok, stiskněte klávesu **SHIFT + ENTER**. Zobrazí se výstup podobný následujícímu fragmentu kódu. Každá událost ve výstupu je v případě, že je součástí Event Hubs, který obsahuje pojem "Azure".

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure

    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie

    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk

    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Číst tweety z Event Hubs

Do poznámkového bloku **AnalyzeTweetsFromEventHub** vložte následující kód a nahraďte zástupné hodnoty hodnotami pro váš Event Hubs Azure, které jste vytvořili dříve. Tento Poznámkový blok přečte tweety, které jste dřív použili při streamování do Event Hubs pomocí poznámkového bloku **SendTweetsToEventHub** .

```scala

    import org.apache.spark.eventhubs._
    import com.microsoft.azure.eventhubs._

    // Build connection string with the above information
    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new com.microsoft.azure.eventhubs.ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val customEventhubParameters =
      EventHubsConf(connStr.toString())
      .setMaxEventsPerTrigger(5)

    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

    incomingStream.printSchema

    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

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

Vzhledem k tomu, že výstup je v binárním režimu, použijte následující fragment kódu k převedení na řetězec.

```scala
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
```

Výstup je nyní podobný následujícímu fragmentu kódu:

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

Nyní máte streamovaná data z Azure Event Hubs do Azure Databricks téměř v reálném čase pomocí konektoru Event Hubs pro Apache Spark. Další informace o použití konektoru Event Hubs pro Spark najdete v [dokumentaci k konektoru](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs?WT.mc_id=sparkeventhubs-docs-alehall).

## <a name="run-sentiment-analysis-on-tweets"></a>Spuštění analýzy mínění na tweety

V této části spustíte analýzu mínění na tweety přijatých pomocí rozhraní Twitter API. V této části přidáte fragmenty kódu do stejného poznámkového bloku **AnalyzeTweetsFromEventHub** .

Začněte přidáním nové buňky kódu do poznámkového bloku a vložte níže uvedený fragment kódu. Tento fragment kódu definuje datové typy pro práci s jazykem a rozhraním API pro mínění.

```scala
import java.io._
import java.net._
import java.util._

case class Language(documents: Array[LanguageDocuments], errors: Array[Any]) extends Serializable
case class LanguageDocuments(id: String, detectedLanguages: Array[DetectedLanguages]) extends Serializable
case class DetectedLanguages(name: String, iso6391Name: String, score: Double) extends Serializable

case class Sentiment(documents: Array[SentimentDocuments], errors: Array[Any]) extends Serializable
case class SentimentDocuments(id: String, score: Double) extends Serializable

case class RequestToTextApi(documents: Array[RequestToTextApiDocument]) extends Serializable
case class RequestToTextApiDocument(id: String, text: String, var language: String = "") extends Serializable
```

Přidejte novou buňku kódu a vložte níže uvedený fragment kódu. Tento fragment kódu definuje objekt, který obsahuje funkce pro volání rozhraní API pro analýzu textu za účelem spuštění rozpoznávání jazyka a mínění analýzy. Zástupný symbol `<PROVIDE ACCESS KEY HERE>` nahraďte hodnotou, kterou jste načetli pro Cognitive Services účet.

```scala
import javax.net.ssl.HttpsURLConnection
import com.google.gson.Gson
import com.google.gson.GsonBuilder
import com.google.gson.JsonObject
import com.google.gson.JsonParser
import scala.util.parsing.json._

object SentimentDetector extends Serializable {

    // Cognitive Services API connection settings
    val accessKey = "<PROVIDE ACCESS KEY HERE>"
    val host = "https://cognitive-docs.cognitiveservices.azure.com/"
    val languagesPath = "/text/analytics/v2.1/languages"
    val sentimentPath = "/text/analytics/v2.1/sentiment"
    val languagesUrl = new URL(host+languagesPath)
    val sentimenUrl = new URL(host+sentimentPath)
    val g = new Gson

    def getConnection(path: URL): HttpsURLConnection = {
        val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
        connection.setRequestMethod("POST")
        connection.setRequestProperty("Content-Type", "text/json")
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", accessKey)
        connection.setDoOutput(true)
        return connection
    }

    def prettify (json_text: String): String = {
        val parser = new JsonParser()
        val json = parser.parse(json_text).getAsJsonObject()
        val gson = new GsonBuilder().setPrettyPrinting().create()
        return gson.toJson(json)
    }

    // Handles the call to Cognitive Services API.
    def processUsingApi(request: RequestToTextApi, path: URL): String = {
        val requestToJson = g.toJson(request)
        val encoded_text = requestToJson.getBytes("UTF-8")
        val connection = getConnection(path)
        val wr = new DataOutputStream(connection.getOutputStream())
        wr.write(encoded_text, 0, encoded_text.length)
        wr.flush()
        wr.close()

        val response = new StringBuilder()
        val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
        var line = in.readLine()
        while (line != null) {
            response.append(line)
            line = in.readLine()
        }
        in.close()
        return response.toString()
    }

    // Calls the language API for specified documents.
    def getLanguage (inputDocs: RequestToTextApi): Option[Language] = {
        try {
            val response = processUsingApi(inputDocs, languagesUrl)
            // In case we need to log the json response somewhere
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val language = g.fromJson(niceResponse, classOf[Language])
            if (language.documents(0).detectedLanguages(0).iso6391Name == "(Unknown)")
                return None
            return Some(language)
        } catch {
            case e: Exception => return None
        }
    }

    // Calls the sentiment API for specified documents. Needs a language field to be set for each of them.
    def getSentiment (inputDocs: RequestToTextApi): Option[Sentiment] = {
        try {
            val response = processUsingApi(inputDocs, sentimenUrl)
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val sentiment = g.fromJson(niceResponse, classOf[Sentiment])
            return Some(sentiment)
        } catch {
            case e: Exception => return None
        }
    }
}
```

Přidejte další buňku pro definování systému Spark UDF (uživatelsky definovaná funkce), která určuje mínění.

```scala
// User Defined Function for processing content of messages to return their sentiment.
val toSentiment =
    udf((textContent: String) =>
        {
            val inputObject = new RequestToTextApi(Array(new RequestToTextApiDocument(textContent, textContent)))
            val detectedLanguage = SentimentDetector.getLanguage(inputObject)
            detectedLanguage match {
                case Some(language) =>
                    if(language.documents.size > 0) {
                        inputObject.documents(0).language = language.documents(0).detectedLanguages(0).iso6391Name
                        val sentimentDetected = SentimentDetector.getSentiment(inputObject)
                        sentimentDetected match {
                            case Some(sentiment) => {
                                if(sentiment.documents.size > 0) {
                                    sentiment.documents(0).score.toString()
                                }
                                else {
                                    "Error happened when getting sentiment: " + sentiment.errors(0).toString
                                }
                            }
                            case None => "Couldn't detect sentiment"
                        }
                    }
                    else {
                        "Error happened when getting language" + language.errors(0).toString
                    }
                case None => "Couldn't detect language"
            }
        }
    )
```

Přidáním konečné buňky kódu Připravte datový rámec s obsahem přípravné a mínění přidružené k poli.

```scala
// Prepare a dataframe with Content and Sentiment columns
val streamingDataFrame = incomingStream.selectExpr("cast (body as string) AS Content").withColumn("Sentiment", toSentiment($"Content"))

// Display the streaming data with the sentiment
streamingDataFrame.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Měl by se zobrazit výstup podobný následujícímu fragmentu kódu:

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +--------------------------------+------------------+
    |Content                         |Sentiment         |
    +--------------------------------+------------------+
    |Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah   #cloudcomputing #Azure          |0.7761918306350708|
    |Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |0.8558163642883301|
    |@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|0.5               |
    |4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |0.5               |
    +--------------------------------+------------------+

Hodnota blíže k hodnotě **1** ve sloupci **mínění** navrhuje Skvělé prostředí pro Azure. Hodnota blíže k **0** navrhuje problémy, které uživatelé čelí při práci s Microsoft Azure.

A je to! Pomocí Azure Databricks jste úspěšně streamovaná data do služby Azure Event Hubs, využili jste data datového proudu pomocí konektoru Event Hubs a potom jsme spustili mínění analýzu dat streamování téměř v reálném čase.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete cluster ukončit. Provedete to tak, že z pracovního prostoru Azure Databricks v levém podokně vyberete **clustery**. U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions (akce** ) a vyberte ikonu **ukončit** .

![Zastavení clusteru datacihly](./media/databricks-sentiment-analysis-cognitive-services/terminate-databricks-cluster.png "zastavení clusteru datacihly")

Pokud cluster neukončíte ručně, zastaví se automaticky a za předpokladu, že jste při vytváření clusteru zaškrtli políčko **ukončit po \_ @ no__t – 2 minuty nečinnosti** . V takovém případě se cluster automaticky zastaví, pokud byl po určenou dobu neaktivní.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak používat Azure Databricks ke streamování dat do Azure Event Hubs a pak číst streamovaná data z Event Hubs v reálném čase. Zjistili jste, jak:
> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření aplikace Twitter pro přístup k datovým proudům
> * Vytvoření poznámkových bloků v Azure Databricks
> * Přidání a připojení knihoven pro Event Hubs a rozhraní Twitter API
> * Vytvoření účtu Microsoft Cognitive Services a načtení přístupového klíče
> * Odeslat tweety do Event Hubs
> * Číst tweety z Event Hubs
> * Spuštění analýzy mínění na tweety

Přejděte k dalšímu kurzu, kde se dozvíte, jak provádět úlohy strojového učení pomocí Azure Databricks.

> [!div class="nextstepaction"]
>[Machine Learning pomocí Azure Databricks](https://docs.azuredatabricks.net/spark/latest/mllib/decision-trees.html)
