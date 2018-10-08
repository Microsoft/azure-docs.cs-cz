---
title: 'Rychlý start: Vyhledávání videí Bingu, Python'
titlesuffix: Azure Cognitive Services
description: Umožňuje získat informace a ukázky kódu, které vám pomůžou rychle začít používat rozhraní API Bingu pro vyhledávání videí.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 797eb476aa3386949b08efb957edf48a97e40d6b
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220006"
---
# <a name="quickstart-bing-video-search-api-with-python"></a>Rychlý start: Vyhledávání videí Bingu s Pythonem

Tento návod vám ukáže, jak používat rozhraní API Bingu pro vyhledávání videí, které je součástí Microsoft Cognitive Services v Azure. Technické podrobnosti o rozhraních API najdete v [referenčních informacích k rozhraním API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).

Tuto ukázku můžete spustit jako poznámkový blok Jupyter v [MyBinderu](https://mybinder.org) tak, že kliknete na odznáček pro spuštění Binderu: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Požadavky
Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k **rozhraním API pro vyhledávání Bingu**. Pro účely tohoto rychlého startu vám bude stačit [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Při aktivaci bezplatné zkušební verze budete potřebovat poskytnutý přístupový klíč, nebo můžete použít klíč placeného předplatného z řídicího panelu Azure.

## <a name="running-the-walkthrough"></a>Spuštění návodu

Nejprve nastavte `subscription_key` na váš klíč rozhraní API pro službu rozhraní API Bingu.


```python
subscription_key = None
assert subscription_key
```

Pak ověřte, že koncový bod `search_url` je správný. V tomto návodu se pro rozhraní API Bingu pro vyhledávání používá pouze jeden koncový bod. Pokud se při autorizaci objeví chyby, znovu zkontrolujte tuto hodnotu a porovnejte ji s koncovým bodem Vyhledávání Bingu na řídicím panelu Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

Nastavte `search_term` tak, aby se hledala videa koťat.


```python
search_term = "kittens"
```

Následující blok používá knihovnu `requests` v Pythonu, pomocí které zavolá rozhraní API Bingu pro vyhledávání a vrátí výsledky jako objekt JSON. Všimněte si, že klíč rozhraní API předáváme prostřednictvím slovníku `headers` a hledaný výraz prostřednictvím slovníku `params`. Pokud si chcete zobrazit úplný seznam možností, které můžete použít k filtrování výsledků hledání, přečtěte si dokumentaci rozhraní [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Objekt `search_results` obsahuje relevantní videa spolu s obsáhlými metadaty. Pokud se chcete podívat na jedno z videí, použijte jeho vlastnost `embedHtml` a vložte ho do `IFrame`.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Stránkování videí](paging-videos.md)
> [Změna velikosti a oříznutí obrázků miniatur](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>Viz také 

 [Hledání videí na webu](search-the-web.md) [Vyzkoušet](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
