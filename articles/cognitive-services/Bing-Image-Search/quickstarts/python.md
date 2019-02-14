---
title: 'Rychlý start: Hledání obrázků - API REST pro vyhledávání obrázků Bingu a Python'
titleSuffix: Azure Cognitive Services
description: Použít tento rychlý start k odesílání požadavků vyhledávání obrázků Bingu Image Search REST API pomocí Pythonu a přijímat odpověďmi ve formátu JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 0fa60f8dc7a1bb0f72080e91adb1149c1c4c082d
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234443"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Rychlý start: Hledání obrázků pomocí API REST pro vyhledávání obrázků Bingu a Pythonu

V tomto rychlém startu můžete zahájit odesílání požadavků hledání rozhraní API Bingu pro vyhledávání obrázků. Tuto aplikaci v Pythonu odešle vyhledávací dotaz na rozhraní API a adresu URL první obrázek se zobrazí ve výsledcích. Zatímco tato aplikace je napsaný v Pythonu, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

Tuto ukázku můžete spustit jako poznámkový blok Jupyter v [MyBinderu](https://mybinder.org) tak, že kliknete na odznáček pro spuštění Binderu:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) s dalším zpracováním chyb a poznámkami.


## <a name="prerequisites"></a>Požadavky

* [Python 2.x nebo 3.x](https://www.python.org/)
* [Python vytvořením bitové kopie knihovny (PIL)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve vašich oblíbených prostředím IDE nebo editorem a importovat následující moduly. Vytvořte proměnnou pro váš klíč předplatného, Hledat koncový bod a hledané výrazy.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví vytvořením slovník a přidáním klíč jako hodnotu. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Vytvořit a odeslat žádost o vyhledávání

1. Parametry žádosti o vytvoření slovníku pro hledání. Přidat hledaný termín `q` parametru. Použití "public" `license` parametr pro hledání obrázků ve veřejné domény. Použití "fotografii" pro `imageType` prohledával pouze fotografie.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Použití `requests` knihovny pro volání rozhraní API Bingu pro vyhledávání obrázků. K žádosti o přidat záhlaví a parametry a vrací odpověď jako objekt JSON. 

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

## <a name="view-the-response"></a>Zobrazení odpovědi

1. Vytvořte nový obrázek se čtyřmi sloupci a čtyři řádky pomocí knihovny matplotlib. 

2. Iterovat přes řádky a sloupce na obrázku a použít knihovnu PIL `Image.open()` metodu pro přidání do každého prostoru miniatura obrázku. 

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    ```

3. Použití `plt.show()` nakreslete obrázek a zobrazí obrázky.

## <a name="example-json-response"></a>Příklad JSON odpovědi

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
    }]
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyhledávání obrázků Bingu – kurz jednostránkové aplikace](../tutorial-bing-image-search-single-page-app.md)

* [Co je API pro vyhledávání obrázků Bingu?](../overview.md)  
* [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) pro rozhraní API pro vyhledávání Bingu. 
* [Získání přístupového klíče služeb Cognitive Services zdarma](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentace Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
