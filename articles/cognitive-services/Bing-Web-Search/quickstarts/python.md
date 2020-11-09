---
title: 'Rychlý start: Hledání pomocí Pythonu – rozhraní API Bingu pro vyhledávání na webu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete posílat požadavky na Vyhledávání na webu Bingu REST API pomocí Pythonu a přijímat odpověď JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 63706eefd4f46c055e8bcbd58a86fdded6f3527d
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381044"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Rychlý start: Použití Pythonu k volání rozhraní API Bingu pro vyhledávání na webu  

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto rychlém startu můžete provést první volání rozhraní API Bingu pro vyhledávání na webu. Tato aplikace Python odešle požadavek na hledání do rozhraní API a zobrazí odpověď ve formátu JSON. I když je tato aplikace napsaná v Pythonu, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Tento příklad je spuštěný jako poznámkový blok Jupyter na [MyBinder](https://mybinder.org). Pokud ho chcete spustit, vyberte spouštěcí znak pořadače:

[![Vazba](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Požadavky

* [Python 2. x nebo 3. x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Definování proměnných

1. Hodnotu `subscription_key` nahraďte platným klíčem předplatného ze svého účtu Azure.

   ```python
   subscription_key = "YOUR_ACCESS_KEY"
   assert subscription_key
   ```

2. Deklarujte koncový bod rozhraní API Bingu pro vyhledávání na webu. Můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

   ```python
   search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
   ```

3. Volitelně můžete upravit vyhledávací dotaz nahrazením hodnoty pro `search_term` .

   ```python
   search_term = "Azure Cognitive Services"
   ```

## <a name="make-a-request"></a>Vytvoření požadavku

Tento kód používá `requests` knihovnu k volání rozhraní API Bingu pro vyhledávání na webu a vrátí výsledky jako objekt JSON. Klíč k rozhraní API bude předán slovníku `headers` a hledaný výraz a parametry dotazu budou předány slovníku `params`. 

Úplný seznam možností a parametrů naleznete v tématu [rozhraní API Bingu pro vyhledávání na webu v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formátování a zobrazení odpovědi

`search_results`Objekt obsahuje výsledky hledání a taková metadata jako související dotazy a stránky. Tento kód používá k formátování a zobrazení odpovědi v prohlížeči knihovnu `IPython.display`.

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

Pokud chcete tento kód spustit lokálně, přečtěte si kompletní [ukázku k dispozici na GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz rozhraní API Bingu pro vyhledávání na webu jednostránkové aplikace](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]