---
title: 'Rychlý start: Hledání zpráv - Bingu News Search SDK pro Python'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu slouží k vyhledání zpráv pomocí sady SDK vyhledávání zpráv Bingu pro Python a zpracování odpovědi.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 4b4706baa5148bba955a980b6184e0747f023066
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875089"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Rychlý start: Provádění sadou SDK pro vyhledávání zpráv Bingu pro vyhledávání zpráv pro Python

V tomto rychlém startu pomocí zahájíte hledání zpráv pomocí sady SDK vyhledávání zpráv Bingu pro Python. Při vyhledávání zpráv Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Požadavky

* [Python](https://www.python.org/) 2.x nebo 3.x

Doporučuje se použít [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html) pro vývoj v jazyce python. Můžete nainstalovat a inicializovat virtuální prostředí se [venv modulu](https://pypi.python.org/pypi/virtualenv). Je nutné nainstalovat virtualenv for Python 2.7. Můžete vytvořit virtuální prostředí pomocí:

```console
python -m venv mytestenv
```

Závislosti SDK vyhledávání zpráv Bingu můžete nainstalovat pomocí tohoto příkazu:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve vašich oblíbených prostředím IDE nebo editorem a importovat následující knihovny. Vytvořte proměnnou pro váš klíč předplatného a hledaný termín.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Inicializace klienta a odeslat žádost

1. Vytvořte instanci `CognitiveServicesCredentials`. Vytvořte instanci klienta:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Odeslat dotaz na rozhraní API pro vyhledávání zpráv, ukládat odpovědi.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Analyzovat odpověď

Pokud se nenajdou žádné výsledky hledání, tisk první výsledek webové stránky:

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
[Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
