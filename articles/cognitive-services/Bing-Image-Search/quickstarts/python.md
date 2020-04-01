---
title: 'Úvodní příručka: Hledání obrázků pomocí rozhraní REST API a Pythonu pro vyhledávání obrázků bingu'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start slouží k odesílání požadavků na vyhledávání obrázků do rozhraní REST API pro vyhledávání obrázků Bingu pomocí Pythonu a přijímat odpovědi JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 6287b9b8e6129fd62a896e5ac1fcca29febbf01a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478543"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Úvodní příručka: Hledání obrázků pomocí rozhraní REST API a Pythonu pro vyhledávání obrázků bingu

Pomocí tohoto rychlého startu můžete začít odesílat požadavky na hledání do rozhraní API pro vyhledávání obrázků Bingu. Tato aplikace Pythonu odešle vyhledávací dotaz do rozhraní API a zobrazí adresu URL prvního obrázku ve výsledcích. Zatímco tato aplikace je napsána v Pythonu, API je restful webová služba kompatibilní s většinou programovacích jazyků.

Tuto ukázku můžete spustit jako poznámkový blok Jupyter v [MyBinderu](https://mybinder.org) tak, že kliknete na odznáček pro spuštění Binderu:

[![Pořadače](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Zdrojový kód pro tuto ukázku je k dispozici [na GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) s dalším zpracováním chyb a anotacemi.


## <a name="prerequisites"></a>Požadavky

* [Python 2.x nebo 3.x](https://www.python.org/)
* [Knihovna obrázků Pythonu (PIL)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve svém oblíbeném rozhraní IDE nebo editoru a importujte následující moduly. Vytvořte proměnnou pro klíč předplatného, koncový bod hledání a hledaný termín. `search_url`může být globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Přidejte klíč předplatného `Ocp-Apim-Subscription-Key` do záhlaví vytvořením slovníku a přidáním klíče jako hodnoty. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Vytvoření a odeslání požadavku na vyhledávání

1. Vytvořte slovník parametrů požadavku na vyhledávání. Přidejte hledaný `q` výraz do parametru. Použijte "public" `license` pro parametr k vyhledávání obrázků ve veřejné doméně. Použijte "foto" `imageType` pro vyhledávání pouze pro fotografie.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Pomocí `requests` knihovny můžete volat rozhraní API pro vyhledávání obrázků Bingu. Přidejte záhlaví a parametry do požadavku a vraťte odpověď jako objekt JSON. Získejte adresy URL na několik miniatur z `thumbnailUrl` pole odpovědi.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>Zobrazit odpověď

1. Vytvořte nový obrázek se čtyřmi sloupci a čtyřmi řádky pomocí knihovny matplotlib. 

2. Iterate přes řádky a sloupce obrázku a pomocí `Image.open()` metody knihovny PIL přidat miniaturu obrázku do každého prostoru. 

3. Slouží `plt.show()` k nakreslení obrázku a zobrazení obrázků.

    ```python
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


## <a name="example-json-response"></a>Příklad odpovědi JSON

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledávání obrázků Bingu – kurz jednostránkové aplikace](../tutorial-bing-image-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání obrázků?](../overview.md)  
* [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) pro vyhledávací api Bingu. 
* [Získání přístupového klíče služeb Cognitive Services zdarma](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentace ke službě Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
