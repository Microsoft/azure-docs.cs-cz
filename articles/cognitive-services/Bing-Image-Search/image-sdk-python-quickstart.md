---
title: Rychlé spuštění bitové kopie Python SDK vyhledávání | Microsoft Docs
description: Instalační program pro aplikaci konzoly SDK Search bitové kopie.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: e30852439ad8ec2d5ddc667b75167e8b5d35be33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343653"
---
# <a name="image-search-sdk-python-quickstart"></a>Rychlý start Python SDK vyhledávání bitové kopie

Sada SDK vyhledávání bitové kopie Bingu obsahuje funkci rozhraní REST API pro webové dotazy a analýzy výsledků. 

[Zdrojový kód pro Python Bing Image vyhledávání SDK ukázky](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) je k dispozici v centru Git.

## <a name="application-dependencies"></a>Závislosti aplikací
Pokud ji nemáte, nainstalujte Python. Sada SDK je kompatibilní s Python 2.7, 3.3, 3.4, 3.5 a 3.6.

Obecná doporučení pro vývoj Python je použití [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html). Instalace a inicializace virtuálního prostředí s [venv modulu](https://pypi.python.org/pypi/virtualenv). Je nutné nainstalovat virtualenv pro Python 2.7.
```
python -m venv mytestenv
```
Instalace sady SDK vyhledávání bitové kopie Bingu závislosti:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Obrázek hledání klienta
Získání [kognitivní služby přístupový klíč](https://azure.microsoft.com/try/cognitive-services/) pod *vyhledávání*. Přidejte importy:
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Vytvoření instance `CognitiveServicesCredentials`a vytvoření instancí klienta:
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Hledání bitové kopie v dotazu (Yosemite) filtrovaný pro animovaný GIF a široké aspekt. Zkontrolujte počet výsledků a vytiskněte insightsToken, miniatur adresy URL a webové adresy URL první výsledku.
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
Hledat bitových kopií pro (Yosemite) filtrovaný pro animovaný GIF a široké aspekt.  Zkontrolujte počet výsledků.  Tiskové out `insightsToken`, `thumbnail url` a `web url` první výsledku.
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

Získáte trendů výsledků:
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

[Ukázky kognitivní služby Python SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


