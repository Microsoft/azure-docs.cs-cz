---
title: 'Rychlý start: Rozhraní API Bingu pro vyhledávání zpráv, Python'
titlesuffix: Azure Cognitive Services
description: Umožňuje získat informace a ukázky kódu, které vám pomůžou rychle začít používat rozhraní API Bingu pro vyhledávání zpráv.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: 738b139cb2070f2244442311d3670757caac6541
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308813"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Rychlý start pro rozhraní API pro vyhledávání zpráv v Pythonu
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
