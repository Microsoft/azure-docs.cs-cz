---
title: 'Rychlý start: Odeslání vyhledávací dotazy pomocí rozhraní API pro vyhledávání obrázků Bingu a Pythonu.'
description: V tomto rychlém startu odesíláte vyhledávací dotazy do rozhraní API pro vyhledávání Bingu k získání seznamu obrázků s využitím Pythonu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: 42b9af2d6c7223dc3f5d269dd2003f1c99d8c7da
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578323"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Rychlý start: Odeslání vyhledávací dotazy s využitím rozhraní REST API a Python

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání obrázků a získejte odpověď ve formátu JSON. Tato jednoduchá aplikace Python odešle vyhledávací dotaz na rozhraní API a zobrazí nezpracované výsledky.

Zatímco tato aplikace je napsaný v Pythonu, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

Tento příklad lze spustit jako poznámkového bloku Jupyter [MyBinder](https://mybinder.org) kliknutím na spustit vazače označení: 

[![Vazač](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Kromě toho je k dispozici zdrojový kód pro tuto ukázku [na Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-quickstart"></a>Spuštění tohoto rychlého startu

Chcete-li začít, nastavte `subscription_key` ke klíči platné předplatné pro službu rozhraní API Bingu.

```python
subscription_key = None
assert subscription_key
```

Dále ověřte, že `search_url` koncový bod je správná. Při psaní tohoto návodu se používá pouze jeden koncový bod pro rozhraní API Bingu pro vyhledávání. Pokud narazíte na chyby autorizace, zkontrolujte tuto hodnotu na koncový bod vyhledávání Bingu v řídicím panelu Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Nastavte `search_term` hledání obrázků Štěňata.


```python
search_term = "puppies"
```

Následující blok používá `requests` knihovny v jazyce Python vyžadují pro vyhledávání Bingu, rozhraní API a vrátí výsledky jako objekt JSON. Podívejte se, že předáváme klíč rozhraní API prostřednictvím `headers` slovníku a hledání termín prostřednictvím `params` slovníku. Pokud chcete zobrazit úplný seznam možností, které můžete použít k filtrování výsledků hledání, najdete [rozhraní REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) dokumentaci.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` Objekt obsahuje skutečné obrázků spolu s formátovaným metadat – například související položky. Například následující řádek kódu může extrahovat miniaturu adresy URL pro prvních 16 výsledky.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Potom použijte `PIL` knihovny stáhnout obrázků miniatur a `matplotlib` knihovny k vykreslení do mřížky 4 USD \times 4$.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
plt.show()
```

## <a name="sample-json-response"></a>Ukázková odpověď JSON

Odpovědi z rozhraní API Bingu pro vyhledávání obrázků se vrátí jako JSON. Tato ukázková odpověď byl zkrácen na zobrazit jeden výsledek.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz jednostránkovou aplikaci pro vyhledávání obrázků Bingu](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je pro vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Zkuste online interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání bezplatné přístupového klíče služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentace ke službě Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční dokumentace rozhraní API pro vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)