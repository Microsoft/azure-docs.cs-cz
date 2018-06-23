---
title: Volání a odpověď – rychlý start Python pro kognitivní služby Azure, rozhraní API Search bitové kopie Bingu | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat rozhraní API služby Bing Image Search v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 3b5d6a961ce4bcde8aaf73f1fbd30689a6c2c2d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342686"
---
# <a name="call-and-response-your-first-bing-image-search-query-in-python"></a>Volání a odpovědi: svůj první dotaz vyhledávání bitové kopie Bingu v Pythonu

Rozhraní API služby Bing Image Search poskytuje podobné Bing.com/Images tím, že umožňuje odesílat vyhledávací dotaz uživatele ke službě Bing a vrátit seznam příslušné bitové kopie.

Tento návod ukazuje jednoduchý příklad volání do rozhraní API služby Bing Image Search a po zpracování výsledný objekt JSON. Další informace najdete v tématu [vyhledávání bitové kopie Bingu dokumentaci](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

V tomto příkladu můžete spustit jako poznámkového bloku Jupyter na [MyBinder](https://mybinder.org) kliknutím na spuštění vazač oznámení: 

[![Vazač](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Požadavky

Musíte mít [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API pro Bing vyhledávání**. [Bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) stačí pro tento rychlý start. Je nutné přístupový klíč zadaný při aktivaci bezplatné zkušební verze, nebo může použít klíč placené předplatné z řídicího panelu Azure.

## <a name="running-the-walkthrough"></a>Spuštění Průvodce
Chcete-li pokračovat v návodu, nastavte `subscription_key` k vašemu klíči rozhraní API pro službu Bing rozhraní API.


```python
subscription_key = None
assert subscription_key
```

Dále ověřte, zda `search_url` koncový bod není správný. Při psaní tohoto textu pouze jeden koncový bod se použije Bing vyhledávání rozhraní API. Pokud dojde k chybám ověřování, zkontrolujte tuto hodnotu na koncový bod hledání Bing v řídicím panelu Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Nastavit `search_term` hledání bitové kopie Štěňata.


```python
search_term = "puppies"
```

Následující blok používá `requests` knihovny v Pythonu volání rozhraní API pro vyhledávání Bing a vrátí výsledky jako objekt JSON. Pozorovat, že jsme předat klíč rozhraní API prostřednictvím `headers` slovníku a hledání termín prostřednictvím `params` slovníku. Pokud chcete zobrazit úplný seznam možností, které lze použít pro filtrování výsledků hledání, naleznete [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) dokumentaci.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` Objektu obsahuje skutečné bitových kopií spolu s bohatou metadata, například související položky. Následující kód například může extrahovat miniaturu adresy URL pro prvních 16 výsledky.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Pak můžeme použít `PIL` knihovny stáhnout miniatury a `matplotlib` knihovna k vykreslení je k mřížce \times 4$ $4.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz služby Search bitové kopie Bingu jednostránkové aplikace](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled vyhledávání bitové kopie Bingu](../overview.md)  
[Vyzkoušet](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Získat bezplatnou zkušební verzi přístupový klíč](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Referenční dokumentace rozhraní API vyhledávání bitové kopie Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
