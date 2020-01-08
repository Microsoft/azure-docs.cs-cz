---
title: 'Rychlý Start: provedení zprávy s Pythonem a Vyhledávání zpráv Bingu REST API'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odeslat žádost Vyhledávání zpráv Bingu REST API pomocí Pythonu a přijmout odpověď JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 1c424c75a4df193ec412355607c68abeda0560a5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448501"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Rychlý Start: provedení hledání zpráv pomocí Pythonu a Vyhledávání zpráv Bingu REST API

Tento rychlý Start použijte k provedení prvního volání rozhraní API Bingu pro vyhledávání zpráv a přijetí odpovědi JSON. Tato jednoduchá aplikace JavaScriptu pošle vyhledávací dotaz do rozhraní API a zpracuje výsledky. I když je tato aplikace napsaná v Pythonu, je rozhraní API webovou službou RESTful, která je kompatibilní s většinou programovacích jazyků.

Tuto ukázku kódu můžete spustit jako Jupyter Poznámkový blok v [MyBinder](https://mybinder.org) kliknutím na spouštěcí označení pořadače: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Zdrojový kód pro tuto ukázku je také k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu v oblíbených IDE nebo editoru a importujte modul žádosti. Vytvořte proměnné pro klíč předplatného, koncový bod a hledaný termín. Můžete použít globální koncový bod nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Vytvoření parametrů pro požadavek

1. Přidejte klíč předplatného do nového slovníku pomocí `"Ocp-Apim-Subscription-Key"` jako klíč. Totéž proveďte pro parametry hledání.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Odeslat žádost a získat odpověď

1. Pomocí knihovny requests volejte rozhraní API pro vizuální vyhledávání Bingu pomocí svého klíče předplatného a objektů slovníku vytvořených v posledním kroku.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` obsahuje odpověď z rozhraní API jako objekt JSON. Přístup k popisům článků obsažených v odpovědi.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Zobrazení výsledků

Tyto popisy se potom dají vykreslit jako tabulka s vyhledávaným klíčovým slovem zvýrazněným **tučným písmem**.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
