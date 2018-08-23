---
title: 'Rychlý start: Odeslání vyhledávací dotazy s využitím rozhraní REST API pro API pro vyhledávání obrázků Bingu v Pythonu'
description: V tomto rychlém startu odesíláte vyhledávací dotazy do rozhraní API pro vyhledávání Bingu k získání seznamu určit prioritu relevantních obrázků pomocí Pythonu.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: bc527ba39b580935f113f56aa63f7bdd283ba304
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "41987508"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Rychlý start: Odeslání vyhledávací dotazy s využitím rozhraní REST API a Python

Rozhraní API pro vyhledávání obrázků Bingu poskytuje funkce podobné Bing.com/Images tím, že umožňuje odeslat dotaz vyhledávání uživatele ke službě Bing a získat seznam určit prioritu relevantních obrázků.

Tento návod ukazuje jednoduchý příklad volání do rozhraní API Bingu pro vyhledávání obrázků a následné zpracování výsledný objekt ve formátu JSON. Další informace najdete v tématu [dokumentace k Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

Tento příklad lze spustit jako poznámkového bloku Jupyter [MyBinder](https://mybinder.org) kliknutím na spustit vazače označení: 

[![Vazač](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-walkthrough"></a>Spuštění Průvodce
Chcete-li pokračovat v návodu, nastavte `subscription_key` k vašemu klíči rozhraní API pro službu rozhraní API Bingu.


```python
subscription_key = None
assert subscription_key
```

Dále ověřte, že `search_url` koncový bod je správná. Při psaní tohoto návodu se používá pouze jeden koncový bod pro rozhraní API Bingu pro vyhledávání. Pokud narazíte na chyby autorizace, zkontrolujte tuto hodnotu na koncový bod vyhledávání Bingu v řídicím panelu Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Nastavte `search_term` hledání obrázků Štěňata.


```python
search_term = "puppies"
```

Následující blok používá `requests` knihovny v jazyce Python vyžadují pro vyhledávání Bingu, rozhraní API a vrátí výsledky jako objekt JSON. Podívejte se, že předáváme klíč rozhraní API prostřednictvím `headers` slovníku a hledání termín prostřednictvím `params` slovníku. Pokud chcete zobrazit úplný seznam možností, které můžete použít k filtrování výsledků hledání, najdete [rozhraní REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) dokumentaci.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` Objekt obsahuje skutečné obrázků spolu s formátovaným metadat – například související položky. Například následující řádek kódu může extrahovat miniaturu adresy URL pro prvních 16 výsledky.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Potom můžeme použít `PIL` knihovny stáhnout obrázků miniatur a `matplotlib` knihovny k vykreslení do mřížky 4 USD \times 4$.


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
> [Kurz jednostránkovou aplikaci pro vyhledávání obrázků Bingu](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled Bingu pro vyhledávání obrázků](../overview.md)  
[Vyzkoušejte si to](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Získat bezplatnou zkušební verzi přístupový klíč](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Referenční dokumentace rozhraní API pro vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
