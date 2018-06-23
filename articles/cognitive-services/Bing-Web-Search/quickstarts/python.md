---
title: Volání a odpověď – rychlý start Python pro kognitivní služby Azure, rozhraní API služby Bing webové Search | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat rozhraní API služby Bing webové Search v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342716"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>Volání a odpovědi: svůj první dotaz vyhledávání webové služby Bing v Pythonu

Rozhraní API služby Bing webové Search poskytuje podobné Bing.com/Search vrácením, že výsledky hledání, které určuje Bing jsou relevantní pro dotaz uživatele. Výsledky mohou zahrnovat webové stránky, obrázků, videí, zprávy a entitami, spolu s související vyhledávací dotazy, pravopisu, časových pásem, převod jednotek, překladů a výpočty. Typy výsledků, které máte jsou založené na jejich významu a úroveň rozhraní API pro vyhledávání Bingu které odebíráte.

Odkazovat [referenční dokumentace rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) technické podrobnosti o rozhraní API.

V tomto příkladu můžete spustit jako poznámkového bloku Jupyter na [MyBinder](https://mybinder.org) kliknutím na spuštění vazač oznámení: 

[![Vazač](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>Požadavky
Musíte mít [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API pro Bing vyhledávání**. [Bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) stačí pro tento rychlý start. Je nutné přístupový klíč zadaný při aktivaci bezplatné zkušební verze, nebo může použít klíč placené předplatné z řídicího panelu Azure.

## <a name="running-the-walkthrough"></a>Spuštění Průvodce

Nastavit `subscription_key` k vašemu klíči rozhraní API pro službu Bing rozhraní API.


```python
subscription_key = None
assert subscription_key
```

Dále ověřte, zda `search_url` koncový bod není správný. Při psaní tohoto textu pouze jeden koncový bod se použije Bing vyhledávání rozhraní API. Pokud dojde k chybám ověřování, zkontrolujte tuto hodnotu na koncový bod hledání Bing v řídicím panelu Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Nastavit `search_term` dotazu Bing kognitivní služeb společnosti Microsoft.


```python
search_term = "Microsoft Cognitive Services"
```

Následující blok používá `requests` knihovny v Pythonu volání rozhraní API pro vyhledávání Bing a vrátí výsledky jako objekt JSON. Pozorovat, že jsme předat klíč rozhraní API prostřednictvím `headers` slovníku a hledání termín prostřednictvím `params` slovníku. Pokud chcete zobrazit úplný seznam možností, které lze použít pro filtrování výsledků hledání, naleznete [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) dokumentaci.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` Objekt obsahuje výsledky hledání společně s bohatou metadata, například související dotazy a stránky. Následující řádky kódu formátu hlavní stránky vrácených dotazem.


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz vývoje webové služby Bing vyhledávání jednostránkové aplikace](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Hledání webové služby Bing – přehled](../overview.md)  
[Vyzkoušet](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Získat bezplatnou zkušební verzi přístupový klíč](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[referenční dokumentace rozhraní API vyhledávání webové služby Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
