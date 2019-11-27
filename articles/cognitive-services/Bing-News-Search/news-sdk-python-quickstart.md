---
title: 'Rychlý Start: provedení hledání zpráv pomocí sady SDK pro Python – Vyhledávání zpráv Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete vyhledat zprávy pomocí sady SDK Vyhledávání zpráv Bingu SDK pro Python a zpracovat odpověď.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2f3d8c88e7949265f12405760acbccebb89d1df5
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379060"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Rychlý Start: provedení zprávy s Vyhledávání zpráv Bingu SDK pro Python

V tomto rychlém startu můžete začít hledat zprávy s Vyhledávání zpráv Bingu SDK pro Python. I když Vyhledávání zpráv Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Požadavky

* [Python](https://www.python.org/) 2. x nebo 3. x

Doporučuje se použít [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html) pro vývoj v Pythonu. Virtuální prostředí můžete nainstalovat a inicializovat pomocí [modulu venv](https://pypi.python.org/pypi/virtualenv). Je nutné nainstalovat virtualenv pro Python 2,7. Virtuální prostředí můžete vytvořit pomocí:

```console
python -m venv mytestenv
```

Pomocí tohoto příkazu můžete nainstalovat Vyhledávání zpráv Bingu závislosti sady SDK:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V oblíbených IDE nebo editoru vytvořte nový soubor Pythonu a naimportujte následující knihovny. Vytvořte proměnnou pro svůj klíč předplatného a hledaný výraz.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Inicializace klienta a odeslání žádosti

1. Vytvořte instanci `CognitiveServicesCredentials`. Vytvořte instanci klienta:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Odešlete vyhledávací dotaz do rozhraní Vyhledávání zpráv API, uložte odpověď.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Analyzovat odpověď

Pokud se najde nějaké výsledky hledání, vytiskněte první výsledek webové stránky:

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
