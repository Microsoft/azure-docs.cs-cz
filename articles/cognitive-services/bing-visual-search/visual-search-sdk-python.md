---
title: 'Rychlý start: Pro vizuální vyhledávání Bingu sady SDK, Python'
titleSuffix: Azure Cognitive Services
description: Nastavení pro konzolovou aplikaci v Pythonu sady SDK pro vizuální vyhledávání
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: 91fddb6da12817428fef009e8720a37534a64f24
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743608"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-python"></a>Rychlý start: Získejte přehledy obrázků pomocí sady SDK vizuální vyhledávání Bingu pro Python

V tomto rychlém startu pomocí zahájíte získat přehledy obrázků ze služby pro vizuální vyhledávání Bingu, pomocí sady Python SDK. Zatímco kompatibilní s Většina programovacích jazyků rozhraní REST API pro vizuální vyhledávání Bingu, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) 

## <a name="prerequisites"></a>Požadavky

* [Python](https://www.python.org/) 2.x nebo 3.x
* Doporučuje se použít [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html). Nainstalujte a inicializujte virtuální prostředí s [modulem venv](https://pypi.python.org/pypi/virtualenv). Nainstalujte virtualenv pro Python 2.7.
* Pro vizuální vyhledávání Bingu sady SDK pro Python. Můžete ho nainstalovat pomocí následujících příkazů:
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`



[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve vašich oblíbených prostředím IDE nebo editorem a přidejte následující příkazy pro import. 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    ```
2. Vytváření proměnných pro váš klíč předplatného, ID konfigurace vlastní a image, kterou chcete nahrát. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. Vytvoření instance klienta

    ```python
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"))
    ```

## <a name="send-the-search-request"></a>Poslat žádost o vyhledávání

1. S touto imagí soubor otevřen, serializaci `VisualSearchRequest()`a předat ji jako `knowledge_request` parametr `visual_search()`.

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. Pokud nebyly vráceny žádné výsledky, tisknout, je, značky a akce v první značku.

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové jednostránkové aplikace](tutorial-bing-visual-search-single-page-app.md)