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
ms.openlocfilehash: ba76e1da73cac1b4f7df79a4c7e903a85ae58931
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599650"
---
# <a name="quickstart-bing-visual-search-sdk-python"></a>Rychlý start: Python SDK pro vizuální vyhledávání Bingu

SDK Vizuálního vyhledávání Bingu používá funkce REST API pro webové žádosti a parsování výsledků.
[Zdrojový kód pro ukázky sady SDK Vizuálního vyhledávání Bingu v Pythonu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) je dostupný v Git Hubu.

Situace s kódy jsou popsané pod těmito nadpisy:
* [Klient Vizuálního vyhledávání](#client)
* [Kompletní konzolová aplikace](#complete-console)
* [Post binárního souboru obrázku s objektem cropArea](#binary-crop)
* [Parametr KnowledgeRequest](#knowledge-req)
* [Značky, akce a actionType](#tags-actions)

## <a name="application-dependencies"></a>Závislosti aplikace
* Pro účely tohoto rychlého startu budete muset spustit si předplatné v cenové úrovni S9, jak je znázorněno v [Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

K zahájení předplatného na webu Azure portal:
1. Zadejte do textového pole v horní části webu Azure portal s textem "BingSearchV7" `Search resources, services, and docs`.  
2. V části Marketplace v rozevíracím seznamu vyberte `Bing Search v7`.
3. Zadejte `Name` pro nový prostředek.
4. Vyberte `Pay-As-You-Go` předplatného.
5. Vyberte `S9` cenovou úroveň.
6. Klikněte na tlačítko `Enable` k zahájení odběru.
 
* Pokud nemáte Python, nainstalujte si ho. Sada SDK je kompatibilní s Pythonem 2.7, 3.3, 3.4, 3.5 a 3.6.
* Obecně se pro vývoj v jazyce Python doporučuje používat [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html). Nainstalujte a inicializujte virtuální prostředí s [modulem venv](https://pypi.python.org/pypi/virtualenv). Nainstalujte virtualenv pro Python 2.7.
```
python -m venv mytestenv
```
Nainstalujte závislosti sady SDK Vizuálního vyhledávání Bingu:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-visualsearch
```

<a name="client"></a> 
## <a name="visual-search-client"></a>Klient Vizuálního vyhledávání
Pokud chcete vytvořit instanci klienta `VisualSearchAPI`, naimportujte si následující knihovny:
```
import http.client, urllib.parse
import json
import os.path
from msrest.authentication import CognitiveServicesCredentials
from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,
)
```
Hodnotu řetězce subscriptionKey nahraďte platným klíčem předplatného.
```
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
```
Potom vytvořte instanci klienta:
```
var client = WebSearchAPI(ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```
Pomocí klienta vyhledejte obrázky a parsujte výsledky:
```
PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
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

<a name="complete-console"></a> 
## <a name="complete-console-application"></a>Kompletní konzolová aplikace

Následující konzolová aplikace provede dříve definovaný dotaz a parsuje výsledky:
```
import http.client, urllib.parse
import json
import os.path
from msrest.authentication import CognitiveServicesCredentials
from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,
)

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'

PATH = 'C:\\Users\\v-gedod\\azure-cognitive-samples\\mytestenv\\TestImages\\'

from azure.cognitiveservices.search.visualsearch import VisualSearchAPI
from azure.cognitiveservices.search.visualsearch.models import (
    VisualSearchRequest,
    CropArea,
    ImageInfo,
    Filters,
    KnowledgeRequest,)

from msrest.authentication import CognitiveServicesCredentials

client = VisualSearchAPI(CognitiveServicesCredentials(subscription_key))

image_path = os.path.join(PATH, "image.jpg")

with open(image_path, "rb") as image_fd:

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(VisualSearchRequest().serialize())

    print("\r\nSearch visual search request with binary of dog image")
    result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
        
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


# Uncomment these methods to include code under the following headings of this documentation.
#search_image_binary_with_crop_area(client, subscription_key, PATH)
#search_url_with_filters(client, subscription_key)
#search_insights_token_with_crop_area(client, subscription_key)

```

Ukázky vyhledávání Bingu ukazují různé funkce sady SDK.  Přidejte následující funkce do dříve definované třídy `VisualSrchSDK`.

<a name="binary-crop"></a>
## <a name="image-binary-post-with-croparea"></a>Post binárního souboru obrázku s objektem cropArea

Následující kód pošle binární soubor obrázku do textu žádosti POST spolu s objektem cropArea.  Potom vytiskne imageInsightsToken, počet značek, počet akcí a první actionType.

```
def search_image_binary_with_crop_area(client, sub_key, file_path):

    #client = VisualSearchAPI(CognitiveServicesCredentials(sub_key))

    image_path = os.path.join(file_path, "image.jpg")
    with open(image_path, "rb") as image_fd:

        crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)
        knowledge_request = VisualSearchRequest(image_info=ImageInfo(crop_area=crop_area))

        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(knowledge_request.serialize())

        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)

        if not result:
            print("No visual search result data.")
            return

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
<a name="knowledge-req"></a>
## <a name="knowledgerequest-parameter"></a>Parametr KnowledgeRequest

Následující kód pošle adresu URL obrázku v parametru `knowledgeRequest`, spolu s filtrem \"site:pinterest.com\". Potom vytiskne `imageInsightsToken`, počet značek, počet akcí a první actionType.
```
def search_url_with_filters(client_in, sub_key):

    client = client_in

    image_url = "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80"
    filters = Filters(site="pinterest.com")

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(url=image_url),
        knowledge_request=KnowledgeRequest(filters=filters)
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with url of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

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
<a name="tags-actions"></a>
## <a name="tags-actions-and-actiontype"></a>Značky, akce a actionType

Následující kód pošle token insights obrázku v parametru knowledgeRequest spolu s objektem cropArea. Potom vytiskne imageInsightsToken, počet značek, počet akcí a první actionType.

```
    client = client_in

    image_insights_token = "bcid_CA6BDBEA28D57D52E0B9D4B254F1DF0D*ccid_6J+8V1zi*thid_R.CA6BDBEA28D57D52E0B9D4B254F1DF0D"
    crop_area = CropArea(top=0.1,bottom=0.5,left=0.1,right=0.9)

    knowledge_request = VisualSearchRequest(
        image_info=ImageInfo(
            image_insights_token=image_insights_token,
            crop_area=crop_area
        ),
    )

    # You need to pass the serialized form of the model
    knowledge_request = json.dumps(knowledge_request.serialize())

    print("\r\nSearch visual search request with URL of dog image")
    result = client.images.visual_search(knowledge_request=knowledge_request)

    if not result:
        print("No visual search result data.")
        return

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

[Ukázky kognitivních služeb sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)