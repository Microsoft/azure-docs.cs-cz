---
title: Python rychlý úvodní kurz pro Azure kognitivní služby Bing Video vyhledávání rozhraní API | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat rozhraní API služby Bing Video Search v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: ce4356f05e69540bc3bc3241e2ec1751ff7a7276
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342684"
---
# <a name="quickstart-for-bing-video-search-api-with-python"></a>Rychlý start pro Bing Video vyhledávání rozhraní API s Pythonem

Tento návod ukazuje, jak používat rozhraní API týkající se hledání Video Bing, součástí kognitivní služby společnosti Microsoft na platformě Azure. Odkazovat [referenční dokumentace rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) technické podrobnosti o rozhraní API.

V tomto příkladu můžete spustit jako poznámkového bloku Jupyter na [MyBinder](https://mybinder.org) kliknutím na spuštění vazač oznámení: 

[![Vazač](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Požadavky
Musíte mít [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API pro Bing vyhledávání**. [Bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) stačí pro tento rychlý start. Je nutné přístupový klíč zadaný při aktivaci bezplatné zkušební verze, nebo může použít klíč placené předplatné z řídicího panelu Azure.

## <a name="running-the-walkthrough"></a>Spuštění Průvodce

Nastavte nejprve, `subscription_key` k vašemu klíči rozhraní API pro službu Bing rozhraní API.


```python
subscription_key = None
assert subscription_key
```

Dále ověřte, zda `search_url` koncový bod není správný. Při psaní tohoto textu pouze jeden koncový bod se použije Bing vyhledávání rozhraní API. Pokud dojde k chybám ověřování, zkontrolujte tuto hodnotu na koncový bod hledání Bing v řídicím panelu Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

Nastavit `search_term` hledání videa koťata


```python
search_term = "kittens"
```

Následující blok používá `requests` knihovny v Pythonu volání rozhraní API pro vyhledávání Bing a vrátí výsledky jako objekt JSON. Pozorovat, že jsme předat klíč rozhraní API prostřednictvím `headers` slovníku a hledání termín prostřednictvím `params` slovníku. Pokud chcete zobrazit úplný seznam možností, které lze použít pro filtrování výsledků hledání, naleznete [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) dokumentaci.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` Objekt obsahuje relevantní videa společně s bohatou metadat. Chcete-li zobrazit jednu videí, použijte jeho `embedHtml` vlastnost a vložte ji do `IFrame`.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Stránkování videa](paging-videos.md)
> [Resizing a oříznutí obrázky miniatur](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>Další informace najdete v tématech 

 [Vyhledávání na webu pro videa](search-the-web.md) [vyzkoušet](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
