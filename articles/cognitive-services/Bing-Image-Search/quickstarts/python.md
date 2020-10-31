---
title: 'Rychlý Start: hledání imagí pomocí Vyhledávání obrázků Bingu REST API a Pythonu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odesílat žádosti o vyhledávání obrázků do Vyhledávání obrázků Bingu REST API pomocí Pythonu a přijímat odpovědi JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: e5c82587eca9ac85820bda9690b54ab1879d5328
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087872"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Rychlý Start: hledání imagí pomocí Vyhledávání obrázků Bingu REST API a Pythonu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto rychlém startu se dozvíte, jak odesílat rozhraní API Bingu pro vyhledávání obrázků žádosti o vyhledávání. Tato aplikace Python odešle vyhledávací dotaz do rozhraní API a zobrazí adresu URL prvního obrázku ve výsledcích. I když je tato aplikace napsaná v Pythonu, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Pokud chcete tento příklad spustit jako Jupyter Poznámkový blok v [MyBinder](https://mybinder.org), vyberte **spouštěcí znak pořadače** :

[![Vazba](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Zdrojový kód pro tuto ukázku je k dispozici [na GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) s dalšími zpracováním chyb a poznámkami.


## <a name="prerequisites"></a>Předpoklady

* [Python 2. x nebo 3. x](https://www.python.org/)
* [Knihovna pil (Python Imaging Library)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V oblíbených IDE nebo editoru vytvořte nový soubor Pythonu a naimportujte následující moduly. Vytvořte proměnnou pro klíč předplatného, koncový bod hledání a hledaný termín. V případě `search_url` můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Přidejte klíč předplatného do `Ocp-Apim-Subscription-Key` záhlaví tak, že vytvoříte slovník a přidáte klíč jako hodnotu. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Vytvoření a odeslání žádosti o vyhledávání

1. Vytvořte slovník pro parametry vyhledávacího požadavku. Přidejte hledaný termín do `q` parametru. Nastavte `license` parametr na pro `public` hledání imagí ve veřejné doméně. Nastavte na hodnotu pro `imageType` `photo` Hledání fotek.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Použijte `requests` knihovnu pro volání rozhraní API Bingu pro vyhledávání obrázků. Přidejte do žádosti hlavičku a parametry a vraťte odpověď jako objekt JSON. Načte adresy URL z několika miniaturních obrázků z pole odpovědi `thumbnailUrl` .

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>Zobrazit odpověď

1. Vytvoří nový obrázek se čtyřmi sloupci a čtyřmi řádky pomocí knihovny matplotlib. 

2. Iterujte pomocí řádků a sloupců na obrázku a pomocí metody knihovny PIL `Image.open()` přidejte k jednotlivým prostorům miniaturu obrázku. 

3. Slouží `plt.show()` k vykreslení obrázku a zobrazení obrázků.

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
* [Podrobnosti o cenách pro rozhraní API pro vyhledávání Bingu](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Dokumentace k Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
