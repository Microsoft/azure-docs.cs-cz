---
title: 'Rychlý start: Sada SDK pro Vyhledávání videí, Python'
titleSuffix: Azure Cognitive Services
description: Nastavení pro konzolovou aplikaci sady SDK Vyhledávání videí.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/15/2018
ms.author: rosh
ms.openlocfilehash: 9784894ea9a9deb350171e0d19042eb65644093b
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225646"
---
# <a name="quickstart-bing-video-search-sdk-with-python"></a>Rychlý start: Sada SDK k Vyhledávání videí Bingu s Pythonem

Sada SDK Vyhledávání obrázků Bingu obsahuje funkce rozhraní REST API pro dotazy na webu a parsování výsledků.

[Zdrojový kód pro ukázky sady SDK Vyhledávání videí Bingu v Pythonu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) je dostupný v Git Hubu.


## <a name="application-dependencies"></a>Závislosti aplikace
Pokud nemáte Python, nainstalujte si ho. Sada SDK je kompatibilní s Pythonem 2.7, 3.3, 3.4, 3.5 a 3.6.

Obecně se pro vývoj v jazyce Python doporučuje používat [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html). Nainstalujte a inicializujte virtuální prostředí s [modulem venv](https://pypi.python.org/pypi/virtualenv). Nainstalujte virtualenv pro Python 2.7.
```
python -m venv mytestenv
```
Nainstalujte závislosti sady SDK Vyhledávání videí Bingu:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```
## <a name="video-search-client"></a>Klient Vyhledávání videí
Načtěte si pod *Search* (Hledání) [přístupový klíč služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Přidejte importy:
```
subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Vytvořte instanci třídy `CognitiveServicesCredentials` a vytvořte instanci klienta:
```
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Vyhledejte videa pro výraz (SwiftKey) a pak ověřte počet výsledků. Vytiskněte `ID`, `name` a adresu `URL` prvního výsledku videa.
```
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

try:
    video_result = client.videos.search(query="SwiftKey")
    print("Search videos for query \"SwiftKey\"")

    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")

except Exception as err:
    print("Encountered exception. {}".format(err))

```
Vyhledejte videa pro výraz (Bellevue Trailer), který je zdarma, krátký a v rozlišení 1080p. Ověřte počet výsledků a vytiskněte `ID`, `name` a adresu `URL` prvního výsledku videa.
```
def video_search_with_filtering(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        video_result = client.videos.search(
            query="Bellevue Trailer",
            pricing=VideoPricing.free,  # Can use the str "free" too
            length=VideoLength.short,   # Can use the str "short" too
            resolution=VideoResolution.hd1080p  # Can use the str "hd1080p" too
        )
        print("Search videos for query \"Bellevue Trailer\" that is free, short and 1080p resolution")

        if video_result.value:
            first_video_result = video_result.value[0]
            print("Video result count: {}".format(len(video_result.value)))
            print("First video id: {}".format(first_video_result.video_id))
            print("First video name: {}".format(first_video_result.name))
            print("First video url: {}".format(first_video_result.content_url))
        else:
            print("Didn't see any video result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

Získejte výsledky videí, které jsou v kurzu. Ověřte dlaždice banneru a kategorie:
```
def video_trending(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_result = client.videos.trending()
        print("Search trending video")

        # Banner tiles
        if trending_result.banner_tiles:
            first_banner_tile = trending_result.banner_tiles[0]
            print("Banner tile count: {}".format(len(trending_result.banner_tiles)))
            print("First banner tile text: {}".format(first_banner_tile.query.text))
            print("First banner tile url: {}".format(first_banner_tile.query.web_search_url))
        else:
            print("Couldn't find banner tiles!")

        # Categorires
        if trending_result.categories:
            first_category = trending_result.categories[0]
            print("Category count: {}".format(len(trending_result.categories)))
            print("First category title: {}".format(first_category.title))
            if first_category.subcategories:
                first_subcategory = first_category.subcategories[0]
                print("Subcategory count: {}".format(len(first_category.subcategories)))
                print("First subcategory title: {}".format(first_subcategory.title))
                if first_subcategory.tiles:
                    first_tile = first_subcategory.tiles[0]
                    print("Subcategory tile count: {}".format(len(first_subcategory.tiles)))
                    print("First tile text: {}".format(first_tile.query.text))
                    print("First tile url: {}".format(first_tile.query.web_search_url))
                else:
                    print("Couldn't find subcategory tiles!")
            else:
                print("Couldn't find subcategories!")
        else:
            print("Couldn't find categories!")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Vyhledejte videa pro výraz (Bellevue Trailer) a pak vyhledejte podrobné informace o prvním videu.
```
def video_detail(subscription_key):

    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        video_result = client.videos.search(query="Bellevue Trailer")
        first_video_result = video_result.value[0]

        video_details = client.videos.details(
            query="Bellevue Trailer",
            id=first_video_result.video_id,
            modules=[VideoInsightModule.all]  # Can use ["all"] too
        )
        print("Search detail for video id={}, name={}".format(
            first_video_result.video_id,
            first_video_result.name
        ))

        if video_details.video_result:
            print("Expected Video id: {}".format(video_details.video_result.video_id))
            print("Expected Video name: {}".format(video_details.video_result.name))
            print("Expected Video url: {}".format(video_details.video_result.content_url))
        else:
            print("Couldn't find expected video")

        if video_details.related_videos.value:
            first_related_video = video_details.related_videos.value[0]
            print("Related video count: {}".format(len(video_details.related_videos.value)))
            print("First related video id: {}".format(first_related_video.video_id))
            print("First related video name: {}".format(first_related_video.name))
            print("First related video content url: {}".format(first_related_video.content_url))
        else:
            print("Couldn't find any related video!")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
## <a name="next-steps"></a>Další kroky

[Ukázky kognitivních služeb sady Python SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

