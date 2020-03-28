---
title: 'Rychlý start: Hledání pomocí Pythonu – rozhraní API Bingu pro vyhledávání na webu'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start slouží k odesílání požadavků do rozhraní REST API pro vyhledávání na webu Bingu pomocí Pythonu a k přijetí odpovědi JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c1af1142faca76cc58b6b3ca9a7106bc0433ea18
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74976362"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Rychlý start: Použití Pythonu k volání rozhraní API Bingu pro vyhledávání na webu  

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní API pro vyhledávání na webu Bingu a obdržet odpověď JSON. Tato aplikace Pythonu odešle požadavek na vyhledávání do rozhraní API a zobrazí odpověď. Aplikace je sice napsaná v Pythonu, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Tento příklad je spuštěný jako poznámkový blok Jupyter na [MyBinder](https://mybinder.org). Vyberte odznak spouštění pořadače:

[![Pořadače](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Požadavky

* [Python 2.x nebo 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Definování proměnných

Hodnotu `subscription_key` nahraďte platným klíčem předplatného ze svého účtu Azure.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Deklarujte koncový bod rozhraní API Bingu pro vyhledávání na webu. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Vyhledávací dotaz můžete přizpůsobit. Stačí místo `search_term` zadat jinou hodnotu.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Vytvoření požadavku

Tento blok používá knihovnu `requests` k volání rozhraní API Bingu pro vyhledávání na webu a k vrácení výsledků jako objektu JSON. Klíč k rozhraní API bude předán slovníku `headers` a hledaný výraz a parametry dotazu budou předány slovníku `params`. Seznam všech možností a parametrů najdete v dokumentaci k [rozhraní API Bingu pro vyhledávání na webu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
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
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Ukázkový kód na GitHubu

Pokud chcete kód spustit na místním počítači, [ukázka je dostupná na GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové vyhledávání Bingu – kurz jednostránkové aplikace](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
