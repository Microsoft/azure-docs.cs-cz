---
title: Rychlý start klientské knihovny Vpython u bingu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: 7a9fab8ba8bb9d21c9284cbf14bc67226d2ef9d3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289739"
---
Pomocí tohoto rychlého startu můžete začít hledat novinky v klientské knihovně Bing Video Search pro Python. Zatímco hledání videa Bing má rozhraní REST API kompatibilní s většinou programovacích jazyků, klientská knihovna poskytuje snadný způsob integrace služby do vašich aplikací. Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) s dalšími anotacemi a funkcemi.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Požadavky

- [Python](https://www.python.org/) 2.x nebo 3.x
- Klientská knihovna Bing Video Search pro python

Doporučujeme použít [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html)pythonu . Virtuální prostředí můžete nainstalovat a inicializovat pomocí [modulu venv](https://pypi.python.org/pypi/virtualenv). Nainstalujte virtualenv pro Python 2.7 s:

```console
python -m venv mytestenv
```

Nainstalujte klientskou knihovnu Bing Video Search pomocí:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve svém oblíbeném rozhraní IDE nebo editoru a přidejte následující příkazy importu. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchClient
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Vytvořte proměnnou pro klíč předplatného. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    ```

## <a name="create-the-search-client"></a>Vytvoření vyhledávacího klienta

Vytvořte instanci třídy `CognitiveServicesCredentials` a vytvořte instanci klienta:

```python
client = VideoSearchAPI(endpoint, CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Odeslání žádosti o vyhledávání a získání odpovědi

1. Pomocí `client.videos.search()` vyhledávacího dotazu odešlete požadavek do rozhraní API pro vyhledávání videí Bingu a získejte odpověď.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Pokud odpověď obsahuje výsledky hledání, získejte první a vytiskněte její ID, název a adresu URL.

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace pro jednu stránku](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Viz také 

- [Co je rozhraní API Bingu pro vyhledávání videí?](../../overview.md)
- [Ukázky sady Cognitive Services .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
