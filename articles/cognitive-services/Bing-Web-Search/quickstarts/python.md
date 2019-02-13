---
title: 'Rychlý start: Hledání s využitím Pythonu – rozhraní API webové vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Použít tento rychlý start k odesílání požadavků na rozhraní Web API vyhledávání Bingu REST pomocí Pythonu a přijetí odpovědi JSON
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 02/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: e191821c8e2c6e189e8d7f8befcb11009f2ed7d8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172361"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Rychlý start: Použití Pythonu k volání rozhraní API webové vyhledávání Bingu  

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání webu a přijetí odpovědi JSON. Tuto aplikaci v Pythonu odešle požadavek hledání do rozhraní API a zobrazí odpovědi. Aplikace je sice napsaná v Pythonu, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Tento příklad je spuštěný jako poznámkový blok Jupyter na [MyBinder](https://mybinder.org). Klikněte na odznáček pro spuštění Binderu:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Požadavky

* [Python 2.x nebo 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

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
