---
title: 'Rychlý Start: hledání videí pomocí sady SDK pro Python – Vvyhledávání videí Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odesílat žádosti o vyhledávání videí pomocí sady SDK Vvyhledávání videí Bingu pro Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 62f929f3428ef7534d833e54b0b8f492636b8a7e
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378753"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>Rychlý Start: provedení videa s Vvyhledávání videí Bingu SDK pro Python

V tomto rychlém startu můžete začít hledat zprávy s Vvyhledávání videí Bingu SDK pro Python. I když Vvyhledávání videí Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) s dalšími poznámkami a funkcemi.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Požadavky

- [Python](https://www.python.org/) 2. x nebo 3. x
- Sada SDK Vvyhledávání videí Bingu pro Python

Doporučuje se používat [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html)Python. Virtuální prostředí můžete nainstalovat a inicializovat pomocí [modulu venv](https://pypi.python.org/pypi/virtualenv). Nainstalujte virtualenv pro Python 2,7 s:

```console
python -m venv mytestenv
```

Nainstalujte sadu Vvyhledávání videí Bingu SDK pomocí:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu v oblíbeném prostředí IDE nebo editoru a přidejte následující příkazy importu. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Vytvořte proměnnou pro klíč předplatného. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>Vytvoření vyhledávacího klienta

Vytvořte instanci třídy `CognitiveServicesCredentials` a vytvořte instanci klienta:

```python
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Odeslat žádost o hledání a získat odpověď

1. K odeslání žádosti do rozhraní API Bingu pro vyhledávání videí použijte `client.videos.search()` s vyhledávacím dotazem a získejte odpověď.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Pokud odpověď obsahuje výsledky hledání, získejte první z nich a vytiskněte jeho ID, název a adresu URL.

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
> [Vytvoření webové aplikace s jednou stránkou](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Viz také 

- [Co je rozhraní API Bingu pro vyhledávání videí?](../overview.md)
- [Ukázky kognitivních služeb sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
