---
title: Rychlý Start klientské knihovny Vyhledávání zpráv Bingu Pythonu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: c1bd0d86a3fd9d19d67d84b9b05955421373e01e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "79503878"
---
V tomto rychlém startu můžete začít hledat zprávy pomocí klientské knihovny Vyhledávání zpráv Bingu pro Python. I když Vyhledávání zpráv Bingu má REST API kompatibilní s většinou programovacích jazyků, Klientská knihovna poskytuje snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Předpoklady

* [Python](https://www.python.org/) 2. x nebo 3. x

Doporučuje se použít [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html) pro vývoj v Pythonu. Virtuální prostředí můžete nainstalovat a inicializovat pomocí [modulu venv](https://pypi.python.org/pypi/virtualenv). Je nutné nainstalovat virtualenv pro Python 2,7. Virtuální prostředí můžete vytvořit pomocí:

```console
python -m venv mytestenv
```

Pomocí tohoto příkazu můžete nainstalovat Vyhledávání zpráv Bingu závislostí klientské knihovny:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V oblíbených IDE nebo editoru vytvořte nový soubor Pythonu a naimportujte následující knihovny. Vytvořte proměnnou pro svůj klíč předplatného a hledaný výraz.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Inicializace klienta a odeslání žádosti

1. Vytvořte instanci `CognitiveServicesCredentials`.
    
    ```python
    client = NewsSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

2. Odešlete vyhledávací dotaz do rozhraní Vyhledávání zpráv API, uložte odpověď.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Parsování odpovědi

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
> [Vytvoření jednostránkové webové aplikace](../../tutorial-bing-news-search-single-page-app.md)
