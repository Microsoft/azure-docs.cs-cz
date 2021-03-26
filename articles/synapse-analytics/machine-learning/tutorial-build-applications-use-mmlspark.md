---
title: 'Kurz: sestavování aplikací strojového učení pomocí Microsoft Machine Learning pro Apache Spark (Preview)'
description: Naučte se používat Microsoft Machine Learning pro Apache Spark k vytváření aplikací strojového učení v Azure synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruixinxu
ms.author: ruxu
ms.openlocfilehash: 928e2ef8b373626a91a291b1798f3ebb7ef290e8
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608829"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>Kurz: sestavování aplikací strojového učení pomocí Microsoft Machine Learning pro Apache Spark (Preview)

V tomto článku se naučíte, jak používat Microsoft Machine Learning for Apache Spark ([MMLSpark](https://github.com/Azure/mmlspark)) k vytváření aplikací strojového učení. MMLSpark rozbalí řešení distribuovaného strojového učení Apache Spark přidáním mnoha nástrojů pro hloubkové učení a datové vědy, jako je [Azure Cognitive Services](../../cognitive-services/big-data/cognitive-services-for-big-data.md), [OpenCV](https://opencv.org/), [LightGBM](https://github.com/Microsoft/LightGBM) a další.  MMLSpark umožňuje vytvářet výkonné a vysoce škálovatelné prediktivní a analytické modely z různých zdrojů dat Spark.
Synapse Spark poskytuje integrované MMLSpark knihovny, včetně:

- [Pro dostupné](https://github.com/Azure/mmlspark/blob/master/docs/vw.md) – služby knihoven pro strojové učení, které umožňují v tweety povolit analýzu textu jako mínění Analysis.
- [Cognitive Services ve Sparku](https://github.com/Azure/mmlspark/blob/master/docs/cogsvc.md) – Chcete-li kombinovat funkci Azure Cognitive Services v kanálech SparkML, aby bylo možné odvodit návrh řešení pro rozpoznávání služby Data Modeling, jako je detekce anomálií.
- [LightBGM](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) – LightGBM je rozhraní pro zvýšení barevného přechodu, které využívá algoritmy učení založené na stromové struktuře. Je navržená tak, aby byla distribuovaná a vyšší efektivita.
- Podmíněné KNN – škálovatelné modely KNN s podmíněnými dotazy.
- [Http ve Sparku](https://github.com/Azure/mmlspark/blob/master/docs/http.md) – povoluje orchestraci distribuovaných mikroslužeb v integraci Sparku a protokolu HTTP založeného na protokolu HTTP.

Tento kurz se zabývá ukázkami, které používají Azure Cognitive Services v MMLSpark pro 

- Analýza textu – získání mínění (nebo nálady) sady vět.
- Počítačové zpracování obrazu – získá značky (popisy jednoho slova) přidružené k sadě imagí.
- Vyhledávání obrázků Bingu – hledejte na webu obrázky související s dotazem přirozeného jazyka.
- Detektor anomálií – zjištění anomálií v datech časové řady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky 

- [Pracovní prostor Azure synapse Analytics](../get-started-create-workspace.md) s účtem úložiště Azure Data Lake Storage Gen2 nakonfigurovaný jako výchozí úložiště. Musíte být *přispěvatelem dat objektů BLOB úložiště* Data Lake Storage Gen2 systému souborů, se kterým pracujete.
- V pracovním prostoru Azure synapse Analytics je fond Spark. Podrobnosti najdete v tématu [Vytvoření fondu Spark ve službě Azure synapse](../quickstart-create-sql-pool-studio.md).
- Kroky před konfigurací popsané v tomto kurzu [konfigurují Cognitive Services v Azure synapse](./tutorial-configure-cognitive-services-synapse.md).


## <a name="get-started"></a>Začínáme
Začněte tím, že naimportujete klíče služby mmlspark a configurate. 

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY" 
# An Anomaly Dectector subscription key
anomaly_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# Your linked key vault for Synapse workspace
key_vault = "YOUR_KEY_VAULT_NAME"


cognitive_service_key = mssparkutils.credentials.getSecret(key_vault, service_key)
bingsearch_service_key = mssparkutils.credentials.getSecret(key_vault, bing_search_key)
anomalydetector_key = mssparkutils.credentials.getSecret(key_vault, anomaly_key)

```


## <a name="text-analytics-sample"></a>Ukázka analýzy textu

Služba [Analýza textu](../../cognitive-services/text-analytics/index.yml) poskytuje několik algoritmů pro extrakci inteligentních přehledů z textu. Můžete například najít mínění zadaného vstupního textu. Služba vrátí skóre od 0,0 do 1,0, kde nízké skóre naznačují negativní mínění a vysoké skóre značí kladné mínění. Tato ukázka používá tři jednoduché věty a vrátí mínění pro každou z nich.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>Očekávané výsledky

|text | mínění|
|--|--|
| Frustrovaní tento nespěcháte hodinový provoz. | negativní |
| Toto je pes | neutrální |
| Jsem si dnes spokojeni, Slunečné! | pozitivní |

## <a name="computer-vision-sample"></a>Ukázka počítačového vidění
[Počítačové zpracování obrazu](../../cognitive-services/computer-vision/index.yml) analyzuje obrázky pro identifikaci struktury, jako jsou obličeje, objekty a popisy přirozeného jazyka. V této ukázce označíte následující obrázek. Značky jsou jedním z popisů textu v obrázku, jako jsou například rozpoznatelné objekty, lidé, krajin a akce.


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>Očekávané výsledky

|image | tags|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [skating, person, Man, venkovní, jedoucí, sport, skateboard, Young, Board, tričko, vzduch, Park, Boy, strana, přechod, rampa, zdvih, štych, nalétající; |

## <a name="bing-image-search-sample"></a>Ukázka vyhledávání obrázků Bingu
[Vyhledávání obrázků Bingu](../../cognitive-services/bing-image-search/overview.md) vyhledá web, aby se načetly obrázky související s dotazem přirozeného jazyka uživatele. V této ukázce používáme textový dotaz, který vyhledává obrázky s uvozovkami. Vrátí seznam adres URL obrázků, které obsahují fotografie související s naším dotazem.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>Očekávané výsledky

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Ukázka detektoru anomálií

Detekce [anomálií](../../cognitive-services/anomaly-detector/index.yml) je ideální pro detekci nedovolených dat v datech časových řad. V této ukázce používáme službu k nalezení anomálií v celé časové řadě.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>Očekávané výsledky

|časové razítko | hodnota | Anomálie |
|--|--|--|
| 1972-01-01T00:00:00Z|826,0|false (nepravda)|
|1972-02-01T00:00:00Z|799,0|false (nepravda)|
|1972-03-01T00:00:00Z|890,0|false (nepravda)|
|1972-04-01T00:00:00Z|900,0|false (nepravda)|
|1972-05-01T00:00:00Z|766,0|false (nepravda)|
|1972-06-01T00:00:00Z|805,0|false (nepravda)|
|1972 – 07 – 01T00:00:00Z|821,0|false (nepravda)|
|1972 – 08-01T00:00:00Z|20000,0|true|
|1972 – 09 – 01T00:00:00Z|883,0|false (nepravda)|
|1972 – 10 – 01T00:00:00Z|898,0|false (nepravda)|
|1972-11-01T00:00:00Z|957,0|false (nepravda)|
|1972 – 12.01T00:00:00Z|924,0|false (nepravda)|
|1973-01-01T00:00:00Z|881,0|false (nepravda)|
|1973-02-01T00:00:00Z|837,0|false (nepravda)|
|1973-03-01T00:00:00Z|9000,0|true|

## <a name="next-steps"></a>Další kroky

* [Podívejte se na ukázkové poznámkové bloky synapse](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks) 
* [Úložiště GitHub MMLSpark](https://github.com/Azure/mmlspark)