---
title: 'Rychlý start: Hledání s využitím Pythonu – rozhraní API webové vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Použít tento rychlý start k odesílání požadavků na rozhraní Web API vyhledávání Bingu REST pomocí Pythonu a přijetí odpovědi JSON
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 4c42461c31e821128c8aa583b5620c1274a9e955
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53260051"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Rychlý start: Použití Pythonu k volání rozhraní API webové vyhledávání Bingu  
Načtěte si pod **Search** (Hledání) [přístupový klíč služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).  Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

V tomto rychlém startu poprvé zavoláte rozhraní API Bingu pro vyhledávání na webu a dostanete odpověď JSON, a nezabere vám to ani 10 minut.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Tento příklad je spuštěný jako poznámkový blok Jupyter na [MyBinder](https://mybinder.org). Klikněte na odznáček pro spuštění Binderu:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="define-variables"></a>Definování proměnných

Hodnotu `subscription_key` nahraďte platným klíčem předplatného ze svého účtu Azure.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Deklarujte koncový bod rozhraní API Bingu pro vyhledávání na webu. Pokud se při autorizaci objeví chyby, znovu zkontrolujte tuto hodnotu a porovnejte ji s koncovým bodem Hledání Bingem na řídicím panelu Azure.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Vyhledávací dotaz přizpůsobíte tím, že místo `search_term` zadáte jinou hodnotu.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Vytvoření požadavku

Tento blok používá knihovnu `requests` k volání rozhraní API Bingu pro vyhledávání na webu a k vrácení výsledků jako objektu JSON. Klíč k rozhraní API bude předán slovníku `headers` a hledaný výraz a parametry dotazu budou předány slovníku `params`. Seznam všech možností a parametrů najdete v dokumentaci k [rozhraní API Bingu pro vyhledávání na webu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formátování a zobrazení odpovědi

Objekt `search_results` obsahuje výsledky hledání a metadata, jako jsou související dotazy a stránky. Tento kód používá k formátování a zobrazení odpovědi v prohlížeči knihovnu `IPython.display`.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Vzorový kód na Githubu

Pokud chcete kód spustit na místním počítači, [ukázka je dostupná na GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Webové vyhledávání Bingu – kurz jednostránkové aplikace](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
