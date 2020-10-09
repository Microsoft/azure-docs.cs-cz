---
title: Cognitive Services pro velké objemy dat – ukázky Pythonu
description: Vyzkoušejte si Cognitive Services ukázky v Pythonu pro Azure Databricks ke spuštění kanálu MMLSpark pro velké objemy dat.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: caf492c2cd3940fd7f37e2a4462c8376a127f393
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86189813"
---
# <a name="python-samples-for-cognitive-services-for-big-data"></a>Ukázky Pythonu pro Cognitive Services pro velké objemy dat

Následující fragmenty kódu jsou připravené ke spuštění a vám pomůžou začít s používáním Cognitive Services ve Sparku pomocí Pythonu.

V ukázkách v tomto článku se používají tyto Cognitive Services:

- Analýza textu – získání mínění (nebo nálady) sady vět.
- Počítačové zpracování obrazu – získá značky (popisy jednoho slova) přidružené k sadě imagí.
- Vyhledávání obrázků Bingu – hledejte na webu obrázky související s dotazem přirozeného jazyka.
- Zvukové soubory s mluveným převodem na text přepisovat k extrakci textových přepisů.
- Detektor anomálií – zjištění anomálií v datech časové řady

## <a name="prerequisites"></a>Požadavky

1. Postupujte podle kroků uvedených v části [Začínáme](getting-started.md) s nastavením Azure Databricks a Cognitive Services prostředí. V tomto kurzu se dozvíte, jak nainstalovat MMLSpark a jak vytvořit cluster Spark v datacihlách.
1. Po vytvoření nového poznámkového bloku v Azure Databricks zkopírujte níže **sdílený kód** a vložte ho do nové buňky v poznámkovém bloku.
1. Vyberte níže ukázku služby a zkopírujte ji do druhé nové buňky v poznámkovém bloku.
1. Nahraďte zástupné symboly klíče předplatného služby vlastním klíčem.
1. Zvolte tlačítko spustit (ikona trojúhelníku) v pravém horním rohu buňky a pak vyberte **Spustit buňku**.
1. Zobrazí výsledky v tabulce pod buňkou.

## <a name="shared-code"></a>Sdílený kód

Chcete-li začít, je nutné přidat tento kód do projektu:

```python
from mmlspark.cognitive import *

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key = "ADD_YOUR_SUBSCRIPION_KEY"
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY"
# An Anomaly Dectector subscription key
anomaly_key = "ADD_YOUR_SUBSCRIPION_KEY"

# Validate the key
assert service_key != "ADD_YOUR_SUBSCRIPION_KEY"
```    

## <a name="text-analytics-sample"></a>Ukázka Analýza textu

Služba [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) poskytuje několik algoritmů pro extrakci inteligentních přehledů z textu. Můžete například najít mínění zadaného vstupního textu. Služba vrátí skóre od 0,0 do 1,0, kde nízké skóre naznačují negativní mínění a vysoké skóre značí kladné mínění.  Tato ukázka používá tři jednoduché věty a vrátí mínění pro každou z nich.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format
display(sentiment.transform(df).select("text", col("sentiment")[0].getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Očekávaný výsledek

| text                                      | mínění                                             |
|:------------------------------------------|:------------------------------------------------------|
| Jsem si dnes spokojeni, Slunečné!           | 0.9789592027664185                                    |
| Frustrovaní tento přenos nespěcháte hodin | 0.023795604705810547                                  |
| Služba rozpoznávání na Spark aint je chybná.  | 0.8888956308364868                                    |

## <a name="computer-vision-sample"></a>Ukázka Počítačové zpracování obrazu

[Počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) analyzuje obrázky pro identifikaci struktury, jako jsou obličeje, objekty a popisy přirozeného jazyka. V této ukázce označíte seznam imagí. Značky jsou jedním z popisů textu v obrázku, jako jsou například rozpoznatelné objekty, lidé, krajin a akce.

```python

# Create a dataframe with the image URLs
df = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select("image", "analysis_results.description.tags"))
```

### <a name="expected-result"></a>Očekávaný výsledek

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ["skating" Person "Man" "skateboard" "mladý" "deskou" "Boy", "" ";" "na" "" "" na "" ""
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ["pes" venku "dřevěný" "malé" "hnědé" "budova" "" na "" stůl "" "," "" na "" stojící "" stůl "" "", "" "," "na" "" na "" "" "                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ["" venku "" House "" na "pracovišti" "na" "" "," malý "" domov "" "malý" "staroslověnština" kámen "" velký "", "" velké "pastviny" "v" ovčí "" cihla "' ' ' ulice ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' '. ' ' '. ' ' '. ' ' '. ' ' '


## <a name="bing-image-search-sample"></a>Ukázka Vyhledávání obrázků Bingu

[Vyhledávání obrázků Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview) vyhledá web, aby se načetly obrázky související s dotazem přirozeného jazyka uživatele. V této ukázce používáme textový dotaz, který vyhledává obrázky s uvozovkami. Vrátí seznam adres URL obrázků, které obsahují fotografie související s naším dotazem.

```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 10
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(100)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bing_search_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")

# This displays the full results returned, uncomment to use
# display(bingSearch.transform(bingParameters))

# Since we have two services, they are put into a pipeline
pipeline = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
display(pipeline.transform(bingParameters))
```

### <a name="expected-result"></a>Očekávaný výsledek

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |


## <a name="speech-to-text-sample"></a>Ukázka převodu řeči na text
Služba převod [řeči na text](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) převede datové proudy nebo soubory mluveného zvuku na text. V této ukázce jsme přepisovat dva zvukové soubory. První soubor je snadno srozumitelný a druhý je náročnější.

```python

# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",),
                           ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```

### <a name="expected-result"></a>Očekávaný výsledek

| url | Zobrazenýtext |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Vlastní řeč nabízí nástroje, které vám umožní vizuálně zkontrolovat kvalitu rozpoznávání modelu tím, že porovná zvuková data s odpovídajícím výsledkem rozpoznávání z vlastního portálu pro rozpoznávání řeči. Můžete si přehrát nahraný zvuk a zjistit, jestli je zadaný výsledek rozpoznávání správný. Tento nástroj umožňuje rychlou kontrolu kvality základního rozpoznávání řeči od Microsoftu k textovému modelu nebo škole vlastního modelu, aniž by se musel přepisovat libovolná zvuková data. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Přidejte vizuální kontrolu gentleman Sir.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Já jsem slyšet. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Líbí se mi možnost reassurance pro rádi, kterou můžu slyšet také. |


## <a name="anomaly-detector-sample"></a>Ukázka detektoru anomálií

Detekce [anomálií](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/) je ideální pro detekci nedovolených dat v datech časových řad. V této ukázce používáme službu k nalezení anomálií v celé časové řadě.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df = spark.createDataFrame([
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
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomaly_key)
  .setLocation("eastus")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
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

## <a name="arbitrary-web-apis"></a>Libovolná webová rozhraní API

Pomocí protokolu HTTP v Sparku je možné v kanálu pro velké objemy dat použít libovolnou webovou službu. V tomto příkladu používáme [rozhraní API World Bank](http://api.worldbank.org/v2/country/) k získání informací o různých zemích po celém světě.

```python
from requests import Request
from mmlspark.io.http import HTTPTransformer, http_udf
from pyspark.sql.functions import udf, col

# Use any requests from the python requests library
def world_bank_request(country):
  return Request("GET", "http://api.worldbank.org/v2/country/{}?format=json".format(country))

# Create a dataframe with spcificies which countries we want data on
df = (spark.createDataFrame([("br",),("usa",)], ["country"])
  .withColumn("request", http_udf(world_bank_request)(col("country"))))

# Much faster for big data because of the concurrency :)
client = (HTTPTransformer()
      .setConcurrency(3)
      .setInputCol("request")
      .setOutputCol("response"))

# Get the body of the response
def get_response_body(resp):
  return resp.entity.content.decode()

# Show the details of the country data returned
display(client.transform(df).select("country", udf(get_response_body)(col("response")).alias("response")))
```

### <a name="expected-result"></a>Očekávaný výsledek

| country   | Odpověď |
|:----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| BR | [{"Page": 1; "Pages": 1, "per_page": "50", "Total": 1}, [{"ID": "BRA", "iso2Code": "BR", "Name": "Brazílie"; "region": {"ID": "LCN", "iso2Code": "ZJ", "value": "Latinská Amerika & karibské"}, "adminregion": {"ID": "nedostatek", "iso2Code": "XJ"; "value": "Latinská Amerika & karibské (s výjimkou vysokého příjmu)"}, "incomeLevel": {"ID": "most", "iso2Code": "XT", "value": "horní střední příjem"}, "lendingType": {"ID": "IBD", "iso2Code": "XF", "value": "IBRD"}; "capitalCity": "Brazílie", "Zeměpisná": "-47.9292", "zeměpisně": "-15,7801"}]] |
| uživatelem  | [{"Page": 1; "Pages": 1, "per_page": "50", "Total": 1}, [{"ID": "USA"; "iso2Code": "US", "Name": "USA"; "region": {"ID": "NAC", "iso2Code": "XU", "value": "Severní Amerika"}, "adminregion": {"ID": "", "iso2Code": "", "value": ""}; "incomeLevel": {"ID": "HIC", "iso2Code": "XD", "value": "vysoký příjem"}, "lendingType": {"ID": "LNX", "iso2Code": "XX", "value": "NOT klasifikované"}; "capitalCity": "Washington D.C.", "Zeměpisná": "-77.032", "zeměpisně": "38.8895"}]] |

## <a name="see-also"></a>Viz také

* [Recept: detekce anomálií](./recipes/anomaly-detection.md)
* [Návod: Art Explorer](./recipes/art-explorer.md)
