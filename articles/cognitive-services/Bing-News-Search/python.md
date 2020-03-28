---
title: 'Úvodní příručka: Provedení vyhledávání zpráv pomocí Pythonu a rozhraní REST API pro vyhledávání zpráv Bingu'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start slouží k odeslání požadavku do rozhraní REST API hledání zpráv bingu pomocí Pythonu a obdržíte odpověď JSON.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448501"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Úvodní příručka: Provedení vyhledávání zpráv pomocí Pythonu a rozhraní REST API pro vyhledávání zpráv Bingu

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní API pro vyhledávání zpráv Bingu a obdržet odpověď JSON. Tato jednoduchá javascriptová aplikace odešle vyhledávací dotaz do api a zpracuje výsledky. Zatímco tato aplikace je napsána v Pythonu, API je RESTful webová služba kompatibilní s většinou programovacích jazyků.

Tuto ukázku kódu můžete spustit jako poznámkový blok Jupyter na [MyBinder](https://mybinder.org) kliknutím na spuštění Binder odznak: 

[![Pořadače](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Zdrojový kód pro tuto ukázku je také k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve svém oblíbeném rozhraní IDE nebo editoru a importujte modul požadavku. Vytvořte proměnné pro klíč předplatného, koncový bod a hledaný termín. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Vytvořit parametry pro požadavek

1. Přidejte klíč předplatného do nového `"Ocp-Apim-Subscription-Key"` slovníku pomocí klíče. Proveďte totéž pro parametry vyhledávání.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Odeslání žádosti a získání odpovědi

1. Pomocí knihovny požadavků můžete volat rozhraní API pro vizuální vyhledávání Bingu pomocí klíče předplatného a slovníkových objektů vytvořených v posledním kroku.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results`obsahuje odpověď z rozhraní API jako objekt JSON. Přístup k popisům článků obsažených v odpovědi.
    
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
