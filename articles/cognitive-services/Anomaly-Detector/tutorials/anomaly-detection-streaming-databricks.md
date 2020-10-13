---
title: 'Kurz: detekce anomálií pro streamovaná data pomocí Azure Databricks'
titleSuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API detektoru anomálií a Azure Databricks k monitorování anomálií ve vašich datech.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 642012a638100a3ad1cb8d9334f5af01adb6ad7a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979168"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Kurz: detekce anomálií pro streamovaná data pomocí Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) je rychlá, snadná a týmová analytická služba založená na Apache Spark. Rozhraní API pro detekci anomálií, součást služby Azure Cognitive Services, nabízí způsob, jak monitorovat data časových řad. Pomocí tohoto kurzu můžete spustit detekci anomálií na proud dat téměř v reálném čase pomocí Azure Databricks. Data Twitteru budete přijímat pomocí Azure Event Hubs a naimportujte je do Azure Databricks pomocí konektoru Spark Event Hubs. Následně použijete rozhraní API ke zjištění anomálií dat streamování.

Následující obrázek ukazuje běh aplikace:

![Azure Databricks s Event Hubs a Cognitive Services](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks s Event Hubs a Cognitive Services")

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření twitterové aplikace pro přístup ke streamovaným datům
> * Vytvoření poznámkových bloků v Azure Databricks
> * Připojení knihoven pro službu Event Hubs a rozhraní Twitter API
> * Vytvoření prostředku detektoru anomálií a načtení přístupového klíče
> * Odeslání tweetů do služby Event Hubs
> * Čtení tweetů ze služby Event Hubs
> * Spustit detekci anomálií na tweety

> [!Note]
> * Tento kurz zavádí přístup k implementaci doporučené [architektury řešení](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) pro rozhraní API detektoru anomálií.
> * Tento kurz nejde dokončit pomocí `F0` předplatného úrovně Free () pro rozhraní API detektoru anomálií nebo Azure Databricks.

Pokud nemáte [předplatné Azure](https://azure.microsoft.com/free/cognitive-services) , vytvořte ho.

## <a name="prerequisites"></a>Předpoklady

- Obor názvů a centrum událostí pro [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) .

- [Připojovací řetězec](../../../event-hubs/event-hubs-get-connection-string.md) pro přístup k oboru názvů Event Hubs. Připojovací řetězec by měl mít podobný formát:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.

- Název zásady sdíleného přístupu a klíč zásad pro Event Hubs.

Informace o vytvoření oboru názvů a centra událostí najdete v tématu [rychlý Start](../../../event-hubs/event-hubs-create.md) pro Azure Event Hubs.

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte Azure Databricks pracovní prostor pomocí [Azure Portal](https://portal.azure.com/).

1. V Azure Portal vyberte vytvořit Azure Databricks **prostředků**  >  **Analytics**  >  **Azure Databricks**.

    ![Azure Databricks na portálu](../media/tutorials/azure-databricks-on-portal.png "Datacihly na Azure Portal")

3. V části **Azure Databricks služba**zadejte následující hodnoty pro vytvoření pracovního prostoru datacihly:


    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../../../azure-resource-manager/management/overview.md). |
    |**Umístění**     | Vyberte **východní USA 2** nebo jednu z dalších dostupných oblastí. Dostupnost oblastí najdete v tématu [služby Azure dostupné v oblasti](https://azure.microsoft.com/regions/services/) .        |
    |**Cenová úroveň**     |  Zvolte úroveň **Standard** nebo **Premium**. Nevybírejte **zkušební verzi**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Vyberte **Vytvořit**.

4. Vytvoření pracovního prostoru trvá několik minut.

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

1. Na webu Azure Portal přejděte do pracovního prostoru Databricks, který jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **nový cluster**.

    ![Datacihly v Azure](../media/tutorials/databricks-on-azure.png "Datacihly v Azure")

3. Na stránce **nový cluster** zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru datacihly Spark v Azure](../media/tutorials/create-databricks-spark-cluster.png "Vytvoření clusteru datacihly Spark v Azure")

    Přijměte všechny výchozí hodnoty kromě následujících:

   * Zadejte název clusteru.
   * V tomto článku vytvořte cluster s modulem runtime **5,2** . Nevybírejte modul runtime **5,3** .
   * Ujistěte se, že je zaškrtnuté políčko **ukončit po \_ \_ minutách nečinnosti** . Zadejte dobu (v minutách), po kterou se má cluster ukončit, pokud se cluster nepoužívá.

     Vyberte **vytvořit cluster**.
4. Vytvoření clusteru trvá několik minut. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Pro příjem streamovaných tweetů je potřeba vytvořit aplikaci na Twitteru. Postupujte podle pokynů k vytvoření aplikace Twitter a poznamenejte si hodnoty, které potřebujete k dokončení tohoto kurzu.

1. Ve webovém prohlížeči přejděte na stránku [Twitter Application Management](https://apps.twitter.com/) (Správa aplikací Twitter) a vyberte **Create New App** (Vytvořit novou aplikaci).

    ![Vytvoření aplikace Twitter](../media/tutorials/databricks-create-twitter-app.png "Vytvoření aplikace Twitter")

2. Na stránce **Create an application** (Vytvoření aplikace) zadejte podrobnosti o nové aplikaci a pak vyberte **Create your Twitter application** (Vytvořit aplikaci Twitter).

    ![Podrobnosti o aplikaci Twitter](../media/tutorials/databricks-provide-twitter-app-details.png "Podrobnosti o aplikaci Twitter")

3. Na stránce aplikace vyberte kartu **Keys and Access Tokens** (Klíče a přístupové tokeny) a zkopírujte hodnoty **Consumer Key** (Uživatelský klíč) a **Consumer Secret** (Uživatelský tajný klíč). Kromě toho vyberte **Create my access token** (Vytvořit přístupový token) a vygenerujte přístupové tokeny. Zkopírujte hodnoty **Access Token** (Přístupový token) a **Access Token Secret** (Tajný klíč přístupového tokenu).

    ![Podrobnosti o aplikaci Twitter 2](../media/tutorials/twitter-app-key-secret.png "Podrobnosti o aplikaci Twitter")

Uložte hodnoty, které jste načetli pro aplikaci Twitter. Tyto hodnoty budete potřebovat v pozdější části kurzu.

## <a name="attach-libraries-to-spark-cluster"></a>Připojení knihoven ke clusteru Spark

V tomto kurzu k odesílání tweetů do služby Event Hubs použijete rozhraní Twitter API. Použijete také [konektor služby Event Hubs pro Apache Spark](https://github.com/Azure/azure-event-hubs-spark) ke čtení a zápisu dat do služby Azure Event Hubs. Pokud chcete tato rozhraní API použít v rámci svého clusteru, přidejte je jako knihovny do Azure Databricks a pak je přidružte ke svému clusteru Spark. Následující pokyny ukazují, jak přidat knihovny do **sdílené** složky ve vašem pracovním prostoru.

1. V pracovním prostoru Azure Databricks vyberte **Pracovní prostor** a pak klikněte pravým tlačítkem na **Sdílené**. V místní nabídce vyberte možnost **vytvořit**  >  **knihovnu**.

   ![Dialogové okno Přidat knihovnu](../media/tutorials/databricks-add-library-option.png "Dialogové okno Přidat knihovnu")

2. Na stránce Nová knihovna vyberte v části **zdroj** možnost **Maven**. Pro **souřadnice**zadejte souřadnici balíčku, který chcete přidat. Tady jsou souřadnice Maven pro knihovny použité v tomto kurzu:

   * Konektor služby Event Hubs pro Spark – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Rozhraní Twitter API – `org.twitter4j:twitter4j-core:4.0.7`

     ![Zadat souřadnice Maven](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Zadat souřadnice Maven")

3. Vyberte **Vytvořit**.

4. Vyberte složku, do které jste knihovnu přidali, a pak vyberte název knihovny.

    ![Vyberte knihovnu, kterou chcete přidat.](../media/tutorials/select-library.png "Vyberte knihovnu, kterou chcete přidat.")

5. Pokud na stránce Knihovna není žádný cluster, vyberte **clustery** a spusťte cluster, který jste vytvořili. Počkejte, dokud stav nezobrazuje ' spuštěno ' a pak se vraťte na stránku knihovny.
Na stránce Knihovna vyberte cluster, ve kterém chcete použít knihovnu, a pak vyberte **nainstalovat**. Po úspěšném přidružení knihovny ke clusteru se stav okamžitě změní na **nainstalováno**.

    ![Nainstalovat knihovnu do clusteru](../media/tutorials/databricks-library-attached.png "Nainstalovat knihovnu do clusteru")

6. Zopakujte tyto kroky pro balíček Twitteru `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Získání přístupového klíče služeb Cognitive Services

V tomto kurzu použijete rozhraní API pro detekci [anomálií v Azure Cognitive Services](../overview.md) ke spouštění detekce anomálií na streamu tweety téměř v reálném čase. Než použijete rozhraní API, musíte v Azure vytvořit prostředek detektoru anomálií a načíst přístupový klíč pro použití rozhraní API detektoru anomálií.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

2. Vyberte **+ Vytvořit prostředek**.

3. V části Azure Marketplace vyberte **AI + Machine Learning**  >  **Zobrazit všechny**  >  **Cognitive Services**  >  **detektor anomálií**. Nebo můžete použít [Tento odkaz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) k přímému přechodu do dialogového okna **vytvořit** .

    ![Vytvořit prostředek detektoru anomálií](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Vytvořit prostředek detektoru anomálií")

4. V dialogovém okně **Vytvořit** zadejte následující hodnoty:

    |Hodnota |Popis  |
    |---------|---------|
    |Název     | Název prostředku detektoru anomálií.        |
    |Předplatné     | Předplatné Azure, ke kterému bude prostředek přidružen.        |
    |Umístění     | Umístění Azure.        |
    |Cenová úroveň     | Cenová úroveň služby. Další informace o cenách detektoru anomálií najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Skupina prostředků     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo vyberte existující.        |


     Vyberte **Vytvořit**.

5. Po vytvoření prostředku na kartě **Přehled** zkopírujte a uložte adresu URL **koncového bodu** , jak je znázorněno na snímku obrazovky. Pak vyberte **Zobrazit přístupové klíče**.

    ![Zobrazit přístupové klíče](../media/tutorials/cognitive-services-get-access-keys.png "Zobrazit přístupové klíče")

6. V části **klíče**vyberte ikonu kopírování na klíč, který chcete použít. Uložte přístupový klíč.

    ![Kopírování přístupových klíčů](../media/tutorials/cognitive-services-copy-access-keys.png "Kopírování přístupových klíčů")

## <a name="create-notebooks-in-databricks"></a>Vytvoření poznámkových bloků v Databricks

V této části vytvoříte v pracovním prostoru Databricks dva poznámkové bloky s následujícími názvy:

- **SendTweetsToEventHub** –Poznámkový blob producenta, který použijete k získání tweetů z Twitteru a jejich streamování do služby Event Hubs.
- **AnalyzeTweetsFromEventHub** – uživatelský Poznámkový blok, který použijete ke čtení tweety z Event Hubs a spuštění detekce anomálií.

1. V pracovním prostoru Azure Databricks v levém podokně vyberte **pracovní prostor** . V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** a pak vyberte **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datacihlech](../media/tutorials/databricks-create-notebook.png "Vytvoření poznámkového bloku v datacihlech")

2. V dialogovém okně **vytvořit Poznámkový blok** zadejte **SendTweetsToEventHub** jako název, jako jazyk vyberte **Scala** a vyberte cluster Spark, který jste vytvořili dříve.

    ![Podrobnosti poznámkového bloku](../media/tutorials/databricks-notebook-details.png "Vytvoření poznámkového bloku v datacihlech")

    Vyberte **Vytvořit**.

3. Zopakováním těchto kroků vytvořte i poznámkový blok **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Odeslání tweetů do služby Event Hubs

Do poznámkového bloku **SendTweetsToEventHub** vložte následující kód a nahraďte zástupný text hodnotami pro váš obor názvů Event Hubs a aplikaci Twitter, kterou jste vytvořili dříve. Tento Poznámkový blok extrahuje čas vytvoření a číslo "Like" s z tweety s klíčovým slovem "Azure" a streamuje je jako události do Event Hubs v reálném čase.

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

Pokud chcete poznámkový blok spustit, stiskněte **SHIFT + ENTER**. Zobrazí se výstup jako v následujícím fragmentu kódu. Každá událost ve výstupu je kombinací časového razítka a počtu "podobného" s ingestování do Event Hubs.

```output
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
```

## <a name="read-tweets-from-event-hubs"></a>Čtení tweetů ze služby Event Hubs

Do poznámkového bloku **AnalyzeTweetsFromEventHub** vložte následující kód a nahraďte zástupný symbol hodnotami pro prostředek detektoru anomálií, který jste vytvořili dříve. Tento poznámkový blok čte tweety, které jste předtím streamovali do služby Event Hubs pomocí poznámkového bloku **SendTweetsToEventHub**.

Nejdřív napište klienta pro volání detektoru anomálií.
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

```scala
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
```

Pak Připravte agregační funkci pro budoucí použití.
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

```scala
import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap
defined class AnomalyDetectorAggregationFunction
```

Pak načtěte data z centra událostí pro detekci anomálií. Zástupný text nahraďte hodnotami pro váš Event Hubs Azure, který jste vytvořili dříve.

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

Výstup teď vypadá podobně jako na následujícím obrázku. Všimněte si, že vaše datum v tabulce se může od data v tomto kurzu lišit, protože data jsou v reálném čase.
![Načtení dat z centra událostí](../media/tutorials/load-data-from-eventhub.png "Načtení dat z centra událostí")

Nyní máte streamovaná data z Azure Event Hubs do Azure Databricks téměř v reálném čase pomocí konektoru Event Hubs pro Apache Spark. Další informace o použití konektoru služby Event Hubs pro Spark najdete v [dokumentaci ke konektorům](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Spustit detekci anomálií na tweety

V této části spustíte detekci anomálií na tweety přijatém pomocí rozhraní API detektoru anomálií. Fragmenty kódu v této části přidáte do stejného poznámkového bloku **AnalyzeTweetsFromEventHub**.

Aby bylo možné zjistit anomálii, je třeba nejprve agregovat počet metrik o hodinu.
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
Výstup nyní připomíná následující fragmenty kódu.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Pak získá agregovaný výsledek výstupu do rozdílu. Vzhledem k tomu, že detekce anomálií vyžaduje okno s delší historií, používáme k uchování dat historie bodu, který chcete zjistit, rozdílová data.
Nahraďte "[zástupný symbol: název tabulky]" kvalifikovaným názvem tabulky rozdílů, který má být vytvořen (například "tweety"). Nahraďte "[zástupný symbol: název složky pro kontrolní body]" hodnotou řetězce, která je jedinečná při každém spuštění tohoto kódu (například "ETL-from-eventhub-20190605").
Další informace o rozdílových Lake na Azure Databricks najdete v tématu [Průvodce rozdílovým Lake Guide](https://docs.azuredatabricks.net/delta/index.html) .


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

Nahraďte "[zástupný symbol: název tabulky]" stejným názvem rozdílové tabulky, kterou jste vybrali výše.
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
Výstup je následující:
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

Data agregované časové řady se teď neustále ingestují do rozdílu. Pak můžete naplánovat hodinovou úlohu k detekci anomálií nejnovějšího bodu.
Nahraďte "[zástupný symbol: název tabulky]" stejným názvem rozdílové tabulky, kterou jste vybrali výše.

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
Výsledek následujícím způsobem:

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

A je to! Pomocí Azure Databricks jste úspěšně streamovaná data do Azure Event Hubs, využili jste data datového proudu pomocí konektoru Event Hubs a pak spustíte detekci anomálií pro streamovaná data prakticky v reálném čase.
I když je v tomto kurzu členitost na hodinu, můžete změnit členitost tak, aby odpovídala vašim potřebám.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete cluster ukončit. Provedete to tak, že v pracovním prostoru Azure Databricks v levém podokně vyberete **clustery** . U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions (akce** ) a vyberte ikonu **ukončit** a pak vyberte **Potvrdit**.

![Zastavení clusteru datacihly](../media/tutorials/terminate-databricks-cluster.png "Zastavení clusteru datacihly")

Pokud cluster neukončíte ručně, zastaví se automaticky, a to za předpokladu, že jste při vytváření clusteru zaškrtli políčko **ukončit po \_ \_ minutách nečinnosti** . V takovém případě se cluster automaticky zastaví, pokud byl po zadanou dobu neaktivní.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí Azure Databricks streamovat data do služby Azure Event Hubs a pak streamovaná data v reálném čase číst ze služby Event Hubs. Přejděte k dalšímu kurzu, kde se dozvíte, jak volat rozhraní API detektoru anomálií a vizualizovat anomálie pomocí Power BI plochy.

> [!div class="nextstepaction"]
>[Detekce anomálií dávky pomocí Power BI Desktop](batch-anomaly-detection-powerbi.md)
