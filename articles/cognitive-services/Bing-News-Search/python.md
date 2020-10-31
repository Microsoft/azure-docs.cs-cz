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
ms.date: 06/16/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 9d73bbbfb5d6339e4bd1c6c4d38ec268b2461150
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095488"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Rychlý Start: provedení hledání zpráv pomocí Pythonu a Vyhledávání zpráv Bingu REST API

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto rychlém startu můžete provést první volání rozhraní API Bingu pro vyhledávání zpráv. Tato jednoduchá aplikace v Pythonu pošle vyhledávací dotaz do rozhraní API a zpracuje výsledek JSON. 

I když je tato aplikace napsaná v Pythonu, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Chcete-li spustit ukázku kódu jako Poznámkový blok Jupyter v [MyBinder](https://mybinder.org), vyberte **spouštěcí znak pořadače** : 

[![spustit pořadač](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Zdrojový kód pro tuto ukázku je také k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

Vytvořte nový soubor Pythonu v oblíbených IDE nebo editoru a importujte modul žádosti. Vytvořte proměnné pro klíč předplatného, koncový bod a hledaný termín. Můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

## <a name="create-parameters-for-the-request"></a>Vytvoření parametrů pro požadavek

Přidejte klíč předplatného do nového slovníku pomocí `Ocp-Apim-Subscription-Key` jako klíč. Totéž proveďte pro parametry hledání.

```python
headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
```

## <a name="send-a-request-and-get-a-response"></a>Odeslat žádost a získat odpověď

1. Pomocí knihovny requests volejte rozhraní API pro vizuální vyhledávání Bingu pomocí vašeho klíče předplatného a objektů slovníku, které jste vytvořili v předchozím kroku.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = json.dumps(response.json())
    ```

2. Přístup k popisům článků obsažených v odpovědi z rozhraní API, které je uloženo v `search_results` podobě objektu JSON. 
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="display-the-results"></a>Zobrazit výsledky

Tyto popisy se potom dají vykreslit jako tabulka s vyhledávaným klíčovým slovem zvýrazněným tučným písmem.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
