---
title: Rychlý start Python SDK vyhledávání zprávy | Microsoft Docs
description: Nastavení pro zprávy vyhledávání SDK konzolové aplikace.
titleSuffix: Azure News Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 6d212d1477ecf583a038e33e72aab3d60f6aa050
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343661"
---
# <a name="news-search-sdk-python-quickstart"></a>Rychlý start Python SDK vyhledávání zprávy

Sada SDK vyhledávání zprávy obsahuje funkci rozhraní REST API pro webové dotazy a analýzy výsledků. 

[Zdrojový kód pro Python SDK služby Search zprávy Bing ukázky](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) je k dispozici v centru Git.

## <a name="application-dependencies"></a>Závislosti aplikací
Pokud ji nemáte, nainstalujte Python. Sada SDK je kompatibilní s Python 2.7, 3.3, 3.4, 3.5 a 3.6.

Obecná doporučení pro vývoj Python je použití [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html). Instalace a inicializace virtuálního prostředí s [venv modulu](https://pypi.python.org/pypi/virtualenv). Je nutné nainstalovat virtualenv pro Python 2.7.
```
python -m venv mytestenv
```
Instalace sady SDK vyhledávání zprávy Bing závislosti:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>Příspěvky hledání klienta
Získání [kognitivní služby přístupový klíč](https://azure.microsoft.com/try/cognitive-services/) pod *vyhledávání*. Přidejte importy:
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Vytvoření instance `CognitiveServicesCredentials`. Vytvořte instanci klienta:
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Hledat výsledky a tisku první výsledek webové stránky:
```
news_result = client.news.search(query="Quantum Computing", market="en-us", count=10)
print("Search news for query \"Quantum Computing\" with market and count")

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
Hledání s filtry pro nejnovější informace o "Umělé Intelligence" s `freshness` a `sortBy` parametry. Ověřte počet výsledků a vytiskněte `totalEstimatedMatches`, `name`, `url`, `description`, `published time`, a `name of provider` z první položky výsledek zprávy.
```
def news_search_with_filtering(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.search(
            query="Artificial Intelligence",
            market="en-us",
            freshness="Week",
            sort_by="Date"
        )
        print("\r\nSearch most recent news for query \"Artificial Intelligence\" with freshness and sortBy")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Prohledat kategorie příspěvky film a TV Zábava s bezpečné vyhledávání. Ověřte počet výsledků a vytiskněte `category`, `name`, `url`, `description`, `published time`, a `name of provider` z první položky výsledek zprávy.
```
def news_category(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.category(
            category="Entertainment_MovieAndTV",
            market="en-us",
            safe_search="strict"
        )
        print("\r\nSearch category news for movie and TV entertainment with safe search")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news category: {}".format(first_news_result.category))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))


```
Hledání trendů témata zprávy v Bing.  Ověřte počet výsledků a vytiskněte `name`, `text of query`, `webSearchUrl`, `newsSearchUrl`, a `image Url` první zprávy výsledku.
```
def news_trending(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_topics = client.news.trending(market="en-us")
        print("\r\nSearch news trending topics in Bing")

        if trending_topics.value:
            first_topic = trending_topics.value[0]
            print("News result count: {}".format(len(trending_topics.value)))
            print("First topic name: {}".format(first_topic.name))
            print("First topic query: {}".format(first_topic.query.text))
            print("First topic image url: {}".format(first_topic.image.url))
            print("First topic webSearchUrl: {}".format(first_topic.web_search_url))
            print("First topic newsSearchUrl: {}".format(first_topic.news_search_url))
        else:
            print("Didn't see any topics result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

## <a name="next-steps"></a>Další postup

[Ukázky kognitivní služby Python SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


