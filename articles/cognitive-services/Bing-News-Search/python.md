---
title: Python rychlý úvodní kurz pro Azure kognitivní služby vyhledávání zprávy Bing rozhraní API | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat rozhraní API služby Bing zprávy Search v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 0fde478b650513aa1527c1d47f5b453ba094506c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342688"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Rychlý start pro vyhledávání zprávy Bing rozhraní API s Pythonem
Tento návod ukazuje jednoduchý příklad volání do rozhraní API služby Bing zprávy Search a po zpracování výsledný objekt JSON. Další informace najdete v tématu [nové hledání Bing dokumentaci](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

V tomto příkladu můžete spustit jako poznámkového bloku Jupyter na [MyBinder](https://mybinder.org) kliknutím na spuštění vazač oznámení: 

[![Vazač](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

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
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Nastavit `search_term` a hledat články s příspěvky o společnosti Microsoft.


```python
search_term = "Microsoft"
```

Následující blok používá `requests` knihovny v Pythonu volání rozhraní API pro vyhledávání Bing a vrátí výsledky jako objekt JSON. Pozorovat, že jsme předat klíč rozhraní API prostřednictvím `headers` slovníku a hledání termín prostřednictvím `params` slovníku. Pokud chcete zobrazit úplný seznam možností, které lze použít pro filtrování výsledků hledání, naleznete [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) dokumentaci.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` Objekt obsahuje relevantní nových článků společně s bohatou metadat. Například následující řádek kódu extrahuje popisy článků.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

Tyto popisy lze poté vykreslit jako tabulku s hledaná klíčová slova zvýrazněných v **tučné**.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Příspěvky stránkování](paging-news.md)
> [pomocí decoration značky zvýraznění textu](hit-highlighting.md)

## <a name="see-also"></a>Další informace najdete v tématech 

 [Vyhledávání na webu pro zprávy](search-the-web.md)  
 [Vyzkoušet](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
