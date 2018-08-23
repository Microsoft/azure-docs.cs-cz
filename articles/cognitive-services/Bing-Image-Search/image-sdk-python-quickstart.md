---
title: 'Rychlý start: Žádosti a filtrovat Image pomocí sady SDK v jazyce Python'
description: V tomto rychlém startu žádosti a filtrovat Image vrácený Bingu pro vyhledávání obrázků, pomocí Pythonu.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 4729f103bb9b50d4ff039907db8eb677f3dc290a
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "41988135"
---
 # <a name="quickstart-request-and-filter-images-using-the-sdk-and-python"></a>Rychlý start: Žádost a filtr imagí pomocí sady SDK a Python

Sady SDK vyhledávání obrázků Bingu obsahuje funkce rozhraní REST API pro dotazy a analýzy výsledků. 

[Zdrojový kód pro ukázky SDK vyhledávání obrázků Bingu Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) je k dispozici v centru Git.

## <a name="application-dependencies"></a>Závislosti aplikace
Pokud ho nemáte, nainstalujte Python. Sada SDK je kompatibilní s Pythonem 2.7, 3.3, 3.4, 3.5 a 3.6.

Obecná doporučení pro vývoj v jazyce Python se má používat [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html). Instalace a inicializovat virtuální prostředí se [venv modulu](https://pypi.python.org/pypi/virtualenv). Je nutné nainstalovat virtualenv for Python 2.7.
```
python -m venv mytestenv
```
Nainstalujte závislosti sady SDK vyhledávání obrázků Bingu:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Obrázek hledání klienta
Získání [přístupového klíče služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) pod *hledání*. Přidejte importy:
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Vytvoření instance `CognitiveServicesCredentials`a vytvořit instanci klienta:
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Vyhledávání obrázků na dotaz (Yosemite) vyfiltrují animované gify a široké aspekt. Zkontrolujte počet výsledků a vytiskne insightsToken, miniatur adresy URL a webovou adresu URL první výsledek.
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
Hledat bitových kopií pro (Yosemite) filtrované za animované gify a široké aspekt.  Zkontrolujte počet výsledků.  Vytiskne `insightsToken`, `thumbnail url` a `web url` první výsledku.
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

Získání trendu výsledků:
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>Další postup

[Ukázky cognitive Services SDK pro Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


