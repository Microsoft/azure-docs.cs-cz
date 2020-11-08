---
title: 'Recept: průzkum inteligentního umění s Cognitive Services pro velké objemy dat'
titleSuffix: Azure Cognitive Services
description: Tento recept ukazuje, jak vytvořit prohledávatelný uměleckou databázi pomocí Azure Search a MMLSpark.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a65ff28a38e42e05844063a330c0325f16b2247
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363285"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>Recept: průzkum inteligentního umění s Cognitive Services pro velké objemy dat

V tomto příkladu použijeme Cognitive Services pro velké objemy dat a přidáte inteligentní poznámky do kolekce otevřeného přístupu z metropolitního Museumu umění (ste). To nám umožní vytvořit inteligentní vyhledávací modul pomocí Azure Search, a to i bez ručních poznámek. 

## <a name="prerequisites"></a>Požadavky

* Musíte mít klíč předplatného pro Počítačové zpracování obrazu a Kognitivní hledání. Podle pokynů v části [Vytvoření účtu Cognitive Services](../../cognitive-services-apis-create-account.md) se přihlaste k odběru počítačové zpracování obrazu a Získejte svůj klíč.
  > [!NOTE]
  > Informace o cenách najdete v tématu [Azure kognitivní hledání](https://azure.microsoft.com/services/search/#pricing).

## <a name="import-libraries"></a>Importovat knihovny

Spusťte následující příkaz, který importuje knihovny pro tento recept.

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>Nastavení klíčů předplatného

Spusťte následující příkaz pro nastavení proměnných pro klíče služby. Vložte klíče předplatného pro Počítačové zpracování obrazu a Azure Kognitivní hledání.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>Čtení dat

Spusťte následující příkaz, který načte data z kolekce otevřeného přístupu pro splnění.

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>Analýza imagí

Spusťte následující příkaz, který použije Počítačové zpracování obrazu v kolekci Open accessed accesss pro splnění. V důsledku toho získáte vizuální funkce z kreseb.

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>Vytvoření indexu hledání

Spusťte následující příkaz, který zapíše výsledky do Azure Search a vytvoří vyhledávací web pro kresby s obohacenými metadaty z Počítačové zpracování obrazu.

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>Dotazování indexu hledání

Spusťte následující příkaz pro dotazování indexu Azure Search.

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>Další kroky

Naučte se používat [Cognitive Services pro velké objemy dat pro detekci anomálií](anomaly-detection.md).