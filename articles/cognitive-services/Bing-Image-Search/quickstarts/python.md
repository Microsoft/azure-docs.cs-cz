---
title: 'Rychlý start: Hledání obrázků - API REST pro vyhledávání obrázků Bingu a Python'
titleSuffix: Azure Cognitive Services
description: Použít tento rychlý start k odesílání požadavků vyhledávání obrázků Bingu Image Search REST API pomocí Pythonu a přijímat odpověďmi ve formátu JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 8/20/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: a93a044279cccd883de5f946bb236cad4b088ae2
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53261973"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Rychlý start: Hledání obrázků pomocí API REST pro vyhledávání obrázků Bingu a Pythonu

V tomto rychlém startu poprvé zavoláte rozhraní API Bingu pro vyhledávání obrázků a dostanete odpověď ve formátu JSON. Tato jednoduchá aplikace Pythonu odesílá vyhledávací dotaz do rozhraní API a zobrazuje nezpracované výsledky.

Aplikace je sice napsaná v Pythonu, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Tuto ukázku můžete spustit jako poznámkový blok Jupyter v [MyBinderu](https://mybinder.org) tak, že kliknete na odznáček pro spuštění Binderu:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Zdrojový kód k této ukázce je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="running-the-quickstart"></a>Spuštění rychlého startu

Chcete-li začít, nastavte `subscription_key` na platný klíč předplatného pro službu rozhraní API Bingu.

```python
subscription_key = None
assert subscription_key
```

Pak ověřte, že koncový bod `search_url` je správný. V tomto návodu se pro rozhraní API Bingu pro vyhledávání používá pouze jeden koncový bod. Pokud se při autorizaci objeví chyby, znovu zkontrolujte tuto hodnotu a porovnejte ji s koncovým bodem vyhledávání Bingu na řídicím panelu Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Nastavte `search_term` hledání obrázků štěňat pomocí klíčového slova „puppies“.


```python
search_term = "puppies"
```

Následující blok používá knihovnu `requests` v Pythonu, pomocí které zavolá rozhraní API Bingu pro vyhledávání a vrátí výsledky jako objekt JSON. Všimněte si, že klíč rozhraní API předáváme prostřednictvím slovníku `headers` a hledaný výraz prostřednictvím slovníku `params`. Pokud si chcete zobrazit úplný seznam možností, které můžete použít k filtrování výsledků hledání, přečtěte si dokumentaci rozhraní [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Objekt `search_results` obsahuje skutečné obrázky spolu s obsáhlými metadaty, jako jsou například související položky. Například následující řádek kódu může extrahovat adresy URL miniatur pro prvních 16 výsledků.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Potom pomocí knihovny `PIL` stáhněte obrázky miniatur a pomocí knihovny `matplotlib` je vykreslete do mřížky $4 x 4$.


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

Odpovědi od rozhraní API Bingu pro vyhledávání obrázků se vrátí jako JSON. Ukázková odpověď je zkrácená, aby zobrazovala jenom jeden výsledek.

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
> [Vyhledávání obrázků Bingu – kurz jednostránkové aplikace](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Vyzkoušet online interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání přístupového klíče služeb Cognitive Services zdarma](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentace Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
