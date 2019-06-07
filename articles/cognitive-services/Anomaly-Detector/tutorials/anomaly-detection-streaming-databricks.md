---
title: 'Kurz: Detekce anomálií ve streamovaných datech pomocí Azure Databricks'
description: Pomocí rozhraní API detekce anomálií a Azure Databricks ke sledování anomálie v datech.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 05/08/2019
ms.author: aahi
ms.openlocfilehash: a5790b5412023f06d9f9fd1d2ff61c11db4c53f3
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807478"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Kurz: Detekce anomálií ve streamovaných datech pomocí Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) je rychlá, snadná a spolupráci založené na Apache Sparku analytická služba. API detekce anomálií, součástí Azure Cognitive Services, zajišťuje monitorování dat časových řad. Použít ke spuštění detekce anomálií na streamovaných datech téměř v reálném čase v tomto kurzu pomocí Azure Databricks. Bude ingestovat data twitteru pomocí služby Azure Event Hubs a importujte je do Azure Databricks pomocí konektoru služby Event Hubs pro Spark. Potom použijete rozhraní API pro detekci anomálií na streamovaných datech. 

Následující obrázek ukazuje běh aplikace:

![Azure Databricks se službami Event Hubs a Cognitive Services](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks se službami Event Hubs a Cognitive Services")

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření twitterové aplikace pro přístup ke streamovaným datům
> * Vytvoření poznámkových bloků v Azure Databricks
> * Připojení knihoven pro službu Event Hubs a rozhraní Twitter API
> * Vytvořte prostředek detekce anomálií a načtení přístupového klíče
> * Odeslání tweetů do služby Event Hubs
> * Čtení tweetů ze služby Event Hubs
> * Spuštění detekce anomálií na tweetech

> [!Note]
> Tento kurz představuje přístup k implementaci doporučené [architekturu řešení](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) pro rozhraní API detekce anomálií.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

> [!Note]
> V tomto kurzu nejde provést s bezplatnou zkušební verzi klíče rozhraní API detekce anomálií. Pokud chcete k vytvoření clusteru Azure Databricks použít bezplatný účet, přejděte na svůj profil a změňte své předplatné na **Průběžné platby**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- [Oboru názvů Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) a centra událostí.

- [Připojovací řetězec](../../../event-hubs/event-hubs-get-connection-string.md) pro přístup k oboru názvů Event Hubs. Připojovací řetězec by měl mít podobném formátu:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- Název zásad sdíleného přístupu a zásady klíč pro službu Event Hubs.

Azure Event Hubs naleznete v tématu [rychlý Start](../../../event-hubs/event-hubs-create.md) informace o vytváření obor názvů a Centrum událostí.

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte pomocí pracovního prostoru Azure Databricks [webu Azure portal](https://portal.azure.com/).

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Analýza** > **Azure Databricks**.

    ![Databricks na webu Azure Portal](../media/tutorials/azure-databricks-on-portal.png "Databricks na webu Azure Portal")

3. V části **služba Azure Databricks**, zadejte následující hodnoty pro vytvoření pracovního prostoru Databricks:


    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../../../azure-resource-manager/resource-group-overview.md). |
    |**Umístění**     | Vyberte **USA – východ 2** nebo jeden další dostupné oblasti. Zobrazit [dostupné služby Azure podle oblasti](https://azure.microsoft.com/regions/services/) dostupnosti oblast.        |
    |**Cenová úroveň**     |  Zvolte úroveň **Standard** nebo **Premium**. Nevybírejte **zkušební verze**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Vyberte **Vytvořit**.

4. Vytvoření pracovního prostoru trvá několik minut. 

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

1. Na webu Azure Portal přejděte do pracovního prostoru Databricks, který jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Z portálu, vyberte **nový Cluster**.

    ![Databricks v Azure](../media/tutorials/databricks-on-azure.png "Databricks v Azure")

3. V **nový Cluster** stránky, zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](../media/tutorials/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

    Přijměte všechny výchozí hodnoty kromě následujících:

   * Zadejte název clusteru.
   * Pro účely tohoto článku vytvořte cluster pomocí **5.2** modulu runtime. NESMÍ být zvolen **5.3** modulu runtime.
   * Ujistěte se, **po provedení \_ \_ počet minut nečinnosti** zaškrtávací políčko zaškrtnuto. Zadejte dobu (v minutách) ukončit clusteru, pokud se cluster nepoužívá.

     Vyberte **Vytvořit cluster**. 
4. Vytvoření clusteru trvá několik minut. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Pro příjem streamovaných tweetů je potřeba vytvořit aplikaci na Twitteru. Postupujte podle pokynů k vytvoření aplikace Twitter a poznamenejte si hodnoty, které potřebujete k dokončení tohoto kurzu.

1. Ve webovém prohlížeči přejděte na stránku [Twitter Application Management](https://apps.twitter.com/) (Správa aplikací Twitter) a vyberte **Create New App** (Vytvořit novou aplikaci).

    ![Vytvoření aplikace Twitter](../media/tutorials/databricks-create-twitter-app.png "Vytvoření aplikace Twitter")

2. Na stránce **Create an application** (Vytvoření aplikace) zadejte podrobnosti o nové aplikaci a pak vyberte **Create your Twitter application** (Vytvořit aplikaci Twitter).

    ![Podrobnosti o aplikaci Twitter](../media/tutorials/databricks-provide-twitter-app-details.png "Podrobnosti o aplikaci Twitter")

3. Na stránce aplikace vyberte kartu **Keys and Access Tokens** (Klíče a přístupové tokeny) a zkopírujte hodnoty **Consumer Key** (Uživatelský klíč) a **Consumer Secret** (Uživatelský tajný klíč). Kromě toho vyberte **Create my access token** (Vytvořit přístupový token) a vygenerujte přístupové tokeny. Zkopírujte hodnoty **Access Token** (Přístupový token) a **Access Token Secret** (Tajný klíč přístupového tokenu).

    ![Podrobnosti o aplikaci Twitter](../media/tutorials/twitter-app-key-secret.png "Podrobnosti o aplikaci Twitter")

Uložte hodnoty, které jste načetli pro aplikaci Twitter. Tyto hodnoty budete potřebovat v pozdější části kurzu.

## <a name="attach-libraries-to-spark-cluster"></a>Připojení knihoven ke clusteru Spark

V tomto kurzu k odesílání tweetů do služby Event Hubs použijete rozhraní Twitter API. Použijete také [konektor služby Event Hubs pro Apache Spark](https://github.com/Azure/azure-event-hubs-spark) ke čtení a zápisu dat do služby Azure Event Hubs. Pokud chcete tato rozhraní API použít v rámci svého clusteru, přidejte je jako knihovny do Azure Databricks a pak je přidružte ke svému clusteru Spark. Následující pokyny ukazují, jak přidat knihovny **Shared** složky v pracovním prostoru.

1. V pracovním prostoru Azure Databricks vyberte **Pracovní prostor** a pak klikněte pravým tlačítkem na **Sdílené**. V místní nabídce vyberte **Vytvořit** > **Knihovna**.

   ![Dialogové okno Přidat knihovnu](../media/tutorials/databricks-add-library-option.png "Dialogové okno Přidat knihovnu")

2. Na stránce Nová knihovna pro **zdroj** vyberte **Maven**. Pro **koordinuje**, zadejte souřadnice balíčku, které chcete přidat. Tady jsou souřadnice Maven pro knihovny použité v tomto kurzu:

   * Konektor služby Event Hubs pro Spark – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Rozhraní Twitter API – `org.twitter4j:twitter4j-core:4.0.7`

     ![Zadání souřadnic Maven](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Zadání souřadnic Maven")

3. Vyberte **Vytvořit**.

4. Vyberte složku, do které jste knihovnu přidali, a pak vyberte název knihovny.

    ![Výběr knihovny k přidání](../media/tutorials/select-library.png "Výběr knihovny k přidání")

5. Pokud na stránce knihovny neexistuje žádný cluster, vyberte **clustery** a spuštění clusteru, který jste vytvořili. Počkejte, až se zobrazí stav "Spuštěna" a pak přejděte zpět na stránku pro knihovnu.
Na stránce knihovny vyberte cluster, ve kterém chcete knihovnu a pak vyberte **nainstalovat**. Po úspěšném přidružení s clusterem knihovny se stav okamžitě změní na **nainstalováno**.

    ![Instalace knihovny do clusteru](../media/tutorials/databricks-library-attached.png "instalace knihovny ke clusteru")

6. Zopakujte tyto kroky pro balíček Twitteru `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Získání přístupového klíče služeb Cognitive Services

V tomto kurzu použijete [Azure anomálií detektor API služeb Cognitive Services](../overview.md) ke spuštění detekce anomálií v datovém proudu tweetů v reálném čase. Než použijete rozhraní API, musíte vytvořit prostředek detekce anomálií v Azure a načtení přístupového klíče k použití rozhraní API detekce anomálií.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte **+ Vytvořit prostředek**.

3. V části webu Azure Marketplace vyberte **AI a strojové učení** > **zobrazit všechny** > **Cognitive Services – více**  >  **Detekce anomálií**. Nebo můžete použít [tento odkaz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) přejdete **vytvořit** dialogové okno přímo.

    ![Vytvořit prostředek detekce anomálií](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "prostředků vytvořit detekce anomálií")

4. V dialogovém okně **Vytvořit** zadejte následující hodnoty:

    |Hodnota |Popis  |
    |---------|---------|
    |Název     | Název prostředku detekce anomálií.        |
    |Předplatné     | Předplatné Azure, které prostředek bude přidružen.        |
    |Location     | Umístění Azure.        |
    |Cenová úroveň     | Cenová úroveň služby. Další informace o cenách detekce anomálií, naleznete v tématu [stránce s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Skupina prostředků     | Určete, jestli chcete vytvořit novou skupinu prostředků nebo vyberte existující.        |


     Vyberte **Vytvořit**.

5. Po vytvoření prostředku z **přehled** kartu, zkopírujte a uložte **koncový bod** adresy URL, jak je znázorněno na snímku obrazovky. Potom vyberte **zobrazení přístupových klíčů**.

    ![Zobrazení přístupových klíčů](../media/tutorials/cognitive-services-get-access-keys.png "Zobrazení přístupových klíčů")

6. V části **klíče**, vyberte ikonu kopírování vedle klíče, který chcete použít. Uložte přístupový klíč.

    ![Kopírování přístupových klíčů](../media/tutorials/cognitive-services-copy-access-keys.png "Kopírování přístupových klíčů")

## <a name="create-notebooks-in-databricks"></a>Vytvoření poznámkových bloků v Databricks

V této části vytvoříte v pracovním prostoru Databricks dva poznámkové bloky s následujícími názvy:

- **SendTweetsToEventHub** –Poznámkový blob producenta, který použijete k získání tweetů z Twitteru a jejich streamování do služby Event Hubs.
- **AnalyzeTweetsFromEventHub** – Poznámkový blok konzumenta, které umožňují čtení tweetů ze služby Event Hubs a spuštění detekce anomálií.

1. V pracovním prostoru Azure Databricks vyberte **pracovní prostor** v levém podokně. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** a pak vyberte **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v Databricks](../media/tutorials/databricks-create-notebook.png "Vytvoření poznámkového bloku v Databricks")

2. V **vytvořit poznámkový blok** dialogového okna zadejte **SendTweetsToEventHub** jako název, vyberte **Scala** jako jazyk a vyberte cluster Spark, který jste vytvořili dříve.

    ![Vytvoření poznámkového bloku v Databricks](../media/tutorials/databricks-notebook-details.png "Vytvoření poznámkového bloku v Databricks")

    Vyberte **Vytvořit**.

3. Zopakováním těchto kroků vytvořte i poznámkový blok **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Odeslání tweetů do služby Event Hubs

V **SendTweetsToEventHub** Poznámkový blok, vložte následující kód a nahraďte zástupné hodnoty pro váš obor názvů Event Hubs a aplikaci Twitter, kterou jste vytvořili dříve. Tento poznámkový blok extrahuje čas vytvoření a počet "Jako" s z tweety s klíčovým slovem "Azure" a tyto události streamování do Event Hubs v reálném čase.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

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
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Pokud chcete poznámkový blok spustit, stiskněte **SHIFT + ENTER**. Zobrazí se výstup jako v následujícím fragmentu kódu. Každá událost ve výstupu je kombinací časové razítko a počet přijatých do služby Event Hubs s "Jako".

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Čtení tweetů ze služby Event Hubs

V **AnalyzeTweetsFromEventHub** Poznámkový blok, vložte následující kód a nahraďte zástupné hodnoty pro prostředek detekce anomálií, které jste vytvořili dříve. Tento poznámkový blok čte tweety, které jste předtím streamovali do služby Event Hubs pomocí poznámkového bloku **SendTweetsToEventHub**.

Zapsání klientovi umožní zavolat detekce anomálií. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
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

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Pokud chcete poznámkový blok spustit, stiskněte **SHIFT + ENTER**. Zobrazí se výstup jako v následujícím fragmentu kódu.

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

Poté si připravte agregační funkci pro budoucí použití.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

Pokud chcete poznámkový blok spustit, stiskněte **SHIFT + ENTER**. Zobrazí se výstup jako v následujícím fragmentu kódu.

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

Načtěte data z centra událostí pro detekci anomálií. Nahraďte zástupné hodnoty hodnotami pro Azure Event Hubs, který jste vytvořili dříve.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

Výstup teď vypadá podobně jako na následujícím obrázku. Všimněte si, že vaše data v tabulce může lišit od data v tomto kurzu jsou data reálném čase.
![Načtení dat z Event hub](../media/tutorials/load-data-from-eventhub.png "Data načíst z centra událostí")

Jste nyní streamovali data ze služby Azure Event Hubs do Azure Databricks v reálném čase pomocí konektoru služby Event Hubs pro Apache Spark. Další informace o použití konektoru služby Event Hubs pro Spark najdete v [dokumentaci ke konektorům](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Spuštění detekce anomálií na tweetech

V této části spustíte na tweetech přijatých pomocí detekce anomálií rozhraní API pro detekci anomálií. Fragmenty kódu v této části přidáte do stejného poznámkového bloku **AnalyzeTweetsFromEventHub**.

Provádět detekci anomálií, je třeba nejprve k agregaci počet vašich metrik za hodinu.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
Výstup teď vypadá podobně jako následující fragmenty kódu.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Potom získá agregované výstup výsledku rozdílový. Protože detekce anomálií vyžaduje delší okno historie, používáme Delta uchovávat data historie bodu, který chcete detekovat. Nahraďte "[zástupný symbol: název tabulky]" s kvalifikovaným názvem tabulky Delta vytvořit (například "tweets"). Nahraďte "[zástupný symbol: název složky pro kontrolní body]" s hodnotou řetězce, který je jedinečný pokaždé, když spustíte tento kód (například "etl z eventhub-20190605").
Další informace týkající se rozdílů Lake v Azure Databricks najdete [Delta Lake Průvodce](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Nahraďte "[zástupný symbol: název tabulky]" se stejným názvem tabulky Delta vámi zvolená výše.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
Výstup, jak je uvedeno níže: 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

Teď data agregovaná časových řad průběžně ingestuje se do rozdílů. Pak můžete naplánovat úlohu po hodinách pro detekci anomálií nejnovějším dostupným bodem. Nahraďte "[zástupný symbol: název tabulky]" se stejným názvem tabulky Delta vámi zvolená výše.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Výsledek, jak je uvedeno níže: 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+


That's it! Using Azure Databricks, you have successfully streamed data into Azure Event Hubs, consumed the stream data using the Event Hubs connector, and then run anomaly detection on streaming data in near real time.
Although in this tutorial, the granularity is hourly, you can always change the granularity to meet your need. 

## Clean up resources

After you have finished running the tutorial, you can terminate the cluster. To do so, in the Azure Databricks workspace, select **Clusters** from the left pane. For the cluster you want to terminate, move the cursor over the ellipsis under **Actions** column, and select the **Terminate** icon and then select **Confirm**.

![Stop a Databricks cluster](../media/tutorials/terminate-databricks-cluster.png "Stop a Databricks cluster")

If you don't manually terminate the cluster it will automatically stop, provided you selected the **Terminate after \_\_ minutes of inactivity** checkbox while creating the cluster. In such a case, the cluster will automatically stop if it has been inactive for the specified time.

## Next steps

In this tutorial, you learned how to use Azure Databricks to stream data into Azure Event Hubs and then read the streaming data from Event Hubs in real time. Advance to the next tutorial to learn how to call the Anomaly Detector API and visualize anomalies using Power BI desktop. 

> [!div class="nextstepaction"]
>[Batch anomaly detection with Power BI desktop](batch-anomaly-detection-powerbi.md)
