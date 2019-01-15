---
title: 'Rychlý start: Hledání zpráv s využitím Pythonu – rozhraní API REST vyhledávání zpráv Bingu'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete odeslat požadavek na Bingu News Search REST API pomocí Pythonu a přijetí odpovědi JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 6d5f21bd2ddcc08296551f061f02d792a5a545a7
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260665"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Rychlý start: Hledání zpráv pomocí Pythonu a rozhraní API REST vyhledávání zpráv Bingu

V tomto rychlém startu můžete provést první volání do rozhraní API pro vyhledávání zpráv Bingu a získejte odpověď ve formátu JSON. Tato jednoduchá aplikace JavaScript odešle vyhledávací dotaz na rozhraní API a zpracovává výsledky. Zatímco tato aplikace je napsaný v Pythonu, je rozhraní API RESTful webových služeb kompatibilní většina programovacích jazyků.

Tento vzorový kód můžete spouštět jako poznámkového bloku Jupyter [MyBinder](https://mybinder.org) kliknutím na spustit vazače označení: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Zdrojový kód pro tuto ukázku je také k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve vašich oblíbených prostředím IDE nebo editorem a importujte modul požadavku. Vytváření proměnných pro váš klíč předplatného, koncový bod a hledaný termín. Vyhledání vašeho koncového bodu na řídicím panelu Azure.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Parametry pro žádost o vytvoření

1. Přidat váš klíč předplatného do nového slovníku, pomocí `"Ocp-Apim-Subscription-Key"` jako klíč. Proveďte totéž pro parametry hledání.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Odešle žádost a získejte odpověď

1. Použijte knihovnu žádosti pro volání API Bingu pro vizuální vyhledávání pomocí váš klíč předplatného a slovníku objekty vytvořené v předchozím kroku.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` obsahuje odpověď z rozhraní API jako objekt JSON. Získat přístup k popisu článků obsažených v odpovědi.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Zobrazení výsledků

Tyto popisy se potom dají vykreslit jako tabulka s vyhledávaným klíčovým slovem zvýrazněným **tučným písmem**.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
[Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)