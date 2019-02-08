---
title: 'Rychlý start: Hledání videí pomocí Bingu Video Search SDK pro Python'
titleSuffix: Azure Cognitive Services
description: Použít tento rychlý start k odesílání požadavků pro vyhledávání videí pomocí sady SDK videa pro vyhledávání Bingu pro Python
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ead69b0165831fef04e68b4c2eb7ea43115ca4ea
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867796"
---
#  <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>Rychlý start: Vyhledávání videí pomocí sady SDK videa pro vyhledávání Bingu pro Python

V tomto rychlém startu pomocí zahájíte hledání zpráv se sadou SDK vyhledávání Bingu videa pro Python. Při vyhledávání videí Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py). Obsahuje další poznámky a funkce.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Požadavky

- [Python](https://www.python.org/) 2.x nebo 3.x
- Video Bingu hledání sady SDK pro python

Doporučuje se používat python [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html). Můžete nainstalovat a inicializovat virtuální prostředí se [venv modulu](https://pypi.python.org/pypi/virtualenv). Nainstalujte virtualenv pro Python 2.7 pomocí:

```console
python -m venv mytestenv
```
   
Video Bingu vyhledat sada SDK se instalace:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve vašich oblíbených prostředím IDE nebo editorem a přidejte následující příkazy pro import. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. Vytvořte proměnnou pro váš klíč předplatného. 
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>Vytvoření klienta služby search

Vytvořte instanci třídy `CognitiveServicesCredentials` a vytvořte instanci klienta:

    ```python
    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-get-a-response"></a>Odeslat žádost o vyhledávání a získat odpověď

1. Použití `client.videos.search()` s vyhledávacímu dotazu do odeslat požadavek na rozhraní API Bingu pro vyhledávání Video a získejte odpověď.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Pokud odpověď obsahuje výsledky hledání, získat první z nich a tisk jeho ID, název a adresu url.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace v jediné stránce](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je API pro vyhledávání videí Bingu?](../overview.md)
* [Ukázky kognitivních služeb sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)