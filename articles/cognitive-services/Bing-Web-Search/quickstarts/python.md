---
title: 'Rychlý start: Hledání pomocí Pythonu – rozhraní API Bingu pro vyhledávání na webu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu poprvé zavoláte rozhraní API Bingu pro vyhledávání na webu. Použijete k tomu Python a dostanete odpověď JSON.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: erhopf
ms.openlocfilehash: 22dc88d2b924587818f7767105872f01f2e43faf
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129144"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Rychlý start: Použití Pythonu k volání rozhraní API Bingu pro vyhledávání na webu  

V tomto rychlém startu poprvé zavoláte rozhraní API Bingu pro vyhledávání na webu a dostanete odpověď JSON, a nezabere vám to ani 10 minut.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

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

## <a name="sample-code-on-github"></a>Ukázkový kód na GitHubu

Pokud chcete kód spustit na místním počítači, [ukázka je dostupná na GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové vyhledávání Bingu – kurz jednostránkové aplikace](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
