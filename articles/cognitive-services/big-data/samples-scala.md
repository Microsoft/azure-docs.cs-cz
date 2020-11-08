---
title: Cognitive Services pro ukázky Scala pro velké objemy dat
description: Použijte Cognitive Services pro Azure Databricks ke spuštění kanálu MMLSpark pro velké objemy dat.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: c47aa803774343b39efeabe3452f1b256cc64c0d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363268"
---
# <a name="quick-examples"></a>Rychlé příklady

Následující fragmenty kódu jsou připravené ke spuštění a vám pomůžou začít s používáním Cognitive Services ve Sparku. Níže uvedené ukázky jsou v Scala.

Tyto ukázky používají tyto Cognitive Services:

- Analýza textu – získání mínění (nebo nálady) sady vět.
- Počítačové zpracování obrazu – získá značky (popisy jednoho slova) přidružené k sadě imagí.
- Vyhledávání obrázků Bingu – hledejte na webu obrázky související s dotazem přirozeného jazyka.
- Zvukové soubory s mluveným převodem na text přepisovat k extrakci textových přepisů.
- Detektor anomálií – zjištění anomálií v datech časové řady

## <a name="prerequisites"></a>Požadavky

1. Postupujte podle kroků uvedených v části [Začínáme](getting-started.md) s nastavením Azure Databricks a Cognitive Services prostředí. Tento kurz obsahuje postup instalace MMLSpark a postup vytvoření clusteru Spark v datacihlách.
1. Po vytvoření nového poznámkového bloku v Azure Databricks zkopírujte níže **sdílený kód** a vložte ho do nové buňky v poznámkovém bloku.
1. Vyberte níže ukázku služby a zkopírujte ji do druhé nové buňky v poznámkovém bloku.
1. Nahraďte zástupné symboly klíče předplatného služby vlastním klíčem.
1. Zvolte tlačítko spustit (ikona trojúhelníku) v pravém horním rohu buňky a pak vyberte **Spustit buňku**.
1. Zobrazí výsledky v tabulce pod buňkou.

## <a name="shared-code"></a>Sdílený kód
Chcete-li začít, přidejte tento kód do projektu:

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="text-analytics"></a>Analýza textu

Služba [Analýza textu](../text-analytics/index.yml) poskytuje několik algoritmů pro extrakci inteligentních přehledů z textu. Můžete například najít mínění zadaného vstupního textu. Služba vrátí skóre mezi `0.0` a `1.0` , kde nízké skóre naznačují negativní mínění a vysoké skóre značí kladné mínění.  Následující ukázka používá tři jednoduché věty a vrací mínění skóre pro každé z nich.

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Očekávaný výsledek

| text                                      | mínění                                             |
|:------------------------------------------|:------------------------------------------------------|
| Jsem si dnes spokojeni, Slunečné!           | 0.9789592027664185                                    |
| Frustrovaní tento přenos nespěcháte hodin | 0.023795604705810547                                  |
| Služba rozpoznávání na Spark aint je chybná.  | 0.8888956308364868                                    |

## <a name="computer-vision"></a>Computer Vision

[Počítačové zpracování obrazu](../computer-vision/index.yml) analyzuje obrázky pro identifikaci struktury, jako jsou obličeje, objekty a popisy přirozeného jazyka.
V této ukázce označíte seznam imagí. Značky jsou jedním z popisů textu v obrázku, jako jsou například rozpoznatelné objekty, lidé, krajin a akce.

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>Očekávaný výsledek

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ["skating" Person "Man" "skateboard" "mladý" "deskou" "Boy", "" ";" "na" "" "" na "" ""
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ["pes" venku "dřevěný" "malé" "hnědé" "budova" "" na "" stůl "" "," "" na "" stojící "" stůl "" "", "" "," "na" "" na "" "" "                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ["" venku "" House "" na "pracovišti" "na" "" "," malý "" domov "" "malý" "staroslověnština" kámen "" velký "", "" velké "pastviny" "v" ovčí "" cihla "' ' ' ulice ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' '. ' ' '. ' ' '. ' ' '. ' ' ' 

## <a name="bing-image-search"></a>Vyhledávání obrázků Bingu

[Vyhledávání obrázků Bingu](../bing-image-search/overview.md) vyhledá web, aby se načetly obrázky související s dotazem přirozeného jazyka uživatele. V této ukázce používáme textový dotaz, který vyhledává obrázky s uvozovkami. Vrátí seznam adres URL obrázků, které obsahují fotografie související s naším dotazem.


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>Očekávaný výsledek

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>Převod řeči na text

Služba převod [řeči na text](../speech-service/index-speech-to-text.yml) převede datové proudy nebo soubory mluveného zvuku na text. V této ukázce jsme přepisovat dva zvukové soubory. První soubor je snadno srozumitelný a druhý je náročnější.

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>Očekávaný výsledek

| url | Zobrazenýtext |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Vlastní řeč nabízí nástroje, které vám umožní vizuálně zkontrolovat kvalitu rozpoznávání modelu tím, že porovná zvuková data s odpovídajícím výsledkem rozpoznávání z vlastního portálu pro rozpoznávání řeči. Můžete si přehrát nahraný zvuk a zjistit, jestli je zadaný výsledek rozpoznávání správný. Tento nástroj umožňuje rychlou kontrolu kvality základního rozpoznávání řeči od Microsoftu k textovému modelu nebo škole vlastního modelu, aniž by se musel přepisovat libovolná zvuková data. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Přidejte vizuální kontrolu gentleman Sir.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Já jsem slyšet. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Líbí se mi možnost reassurance pro rádi, kterou můžu slyšet také. |

## <a name="anomaly-detector"></a>Detektor anomálií

Detekce [anomálií](../anomaly-detector/index.yml) je ideální pro detekci nedovolených dat v datech časových řad. V této ukázce používáme službu k nalezení anomálií v celé časové řadě.

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>Očekávaný výsledek

| časové razítko            |   value | Anomálie   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | Nepravda       |
| 1972-02-01T00:00:00Z |     799 | Nepravda       |
| 1972-03-01T00:00:00Z |     890 | Nepravda       |
| 1972-04-01T00:00:00Z |     900 | Nepravda       |
| 1972-05-01T00:00:00Z |     766 | Nepravda       |
| 1972-06-01T00:00:00Z |     805 | Nepravda       |
| 1972 – 07 – 01T00:00:00Z |     821 | Nepravda       |
| 1972 – 08-01T00:00:00Z |   20000 | Ano        |
| 1972 – 09 – 01T00:00:00Z |     883 | Nepravda       |
| 1972 – 10 – 01T00:00:00Z |     898 | Nepravda       |
| 1972-11-01T00:00:00Z |     957 | Nepravda       |
| 1972 – 12.01T00:00:00Z |     924 | Nepravda       |
| 1973-01-01T00:00:00Z |     881 | Nepravda       |
| 1973-02-01T00:00:00Z |     837 | Nepravda       |
| 1973-03-01T00:00:00Z |    9000 | Ano        |