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
ms.date: 9/21/2017
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 8ce8353df9a6f8354c56d9c9115645c0b7f2136a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251653"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Rychlý start: Hledání zpráv pomocí Pythonu a rozhraní API REST vyhledávání zpráv Bingu

Tento návod představuje jednoduchý příklad volání rozhraní API Bingu pro vyhledávání zpráv a následného zpracování výsledného objektu JSON. Další informace najdete v [dokumentaci k vyhledávání zpráv Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

Tuto ukázku můžete spustit jako poznámkový blok Jupyter v [MyBinderu](https://mybinder.org) tak, že kliknete na odznáček pro spuštění Binderu: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

## <a name="prerequisites"></a>Požadavky

Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k **rozhraním API pro vyhledávání Bingu**. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Budete potřebovat přístupový klíč k dispozici při aktivaci vaší bezplatné zkušební verze.  Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="running-the-walkthrough"></a>Spuštění návodu
Nejprve nastavte `subscription_key` na váš klíč rozhraní API pro službu rozhraní API Bingu.


```python
subscription_key = None
assert subscription_key
```

Pak ověřte, že koncový bod `search_url` je správný. V tomto návodu se pro rozhraní API Bingu pro vyhledávání používá pouze jeden koncový bod. Pokud se při autorizaci objeví chyby, znovu zkontrolujte tuto hodnotu a porovnejte ji s koncovým bodem vyhledávání Bingu na řídicím panelu Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Nastavte `search_term` na hledání novinových článků o Microsoftu.


```python
search_term = "Microsoft"
```

Následující blok používá knihovnu `requests` v Pythonu, pomocí které zavolá rozhraní API Bingu pro vyhledávání a vrátí výsledky jako objekt JSON. Všimněte si, že klíč rozhraní API předáváme prostřednictvím slovníku `headers` a hledaný výraz prostřednictvím slovníku `params`. Pokud si chcete zobrazit úplný seznam možností, které můžete použít k filtrování výsledků hledání, přečtěte si dokumentaci rozhraní [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Objekt `search_results` obsahuje relevantní novinové články spolu s obsáhlými metadaty. Třeba následující řádek kódu extrahuje popisy článků.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

Tyto popisy se potom dají vykreslit jako tabulka s vyhledávaným klíčovým slovem zvýrazněným **tučným písmem**.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Stránkování zpráv](paging-news.md)
> [Zvýraznění textu pomocí dekoračních značek](hit-highlighting.md)

## <a name="see-also"></a>Další informace najdete v tématech 

 [Vyhledávání zpráv na internetu](search-the-web.md)  
 [Vyzkoušet](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
