---
title: 'Rychlý start: Sada SDK Bingu pro vyhledávání zpráv, Python'
titleSuffix: Azure Cognitive Services
description: Nastavení pro konzolovou aplikaci sady SDK Bingu pro vyhledávání zpráv.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 8e4343b053835c0fc2219373ad60f96c7b80636a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803337"
---
# <a name="quickstart-bing-news-search-sdk-with-python"></a>Rychlý start: Sada SDK Bingu pro vyhledávání zpráv s využitím Pythonu

Sada SDK pro vyhledávání zpráv obsahuje funkce rozhraní REST API pro dotazy na webu a parsování výsledků. 

[Zdrojový kód ukázek sady SDK Bingu pro vyhledávání zpráv v Pythonu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) je k dispozici na GitHubu.

## <a name="application-dependencies"></a>Závislosti aplikace
Pokud nemáte Python, nainstalujte si ho. Sada SDK je kompatibilní s Pythonem 2.7, 3.3, 3.4, 3.5 a 3.6.

Obecně se pro vývoj v jazyce Python doporučuje používat [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html). Nainstalujte a inicializujte virtuální prostředí s [modulem venv](https://pypi.python.org/pypi/virtualenv). Je potřeba nainstalovat prostředí virtualenv pro Python 2.7.
```
python -m venv mytestenv
```
Nainstalujte závislosti sady SDK Bingu pro vyhledávání zpráv:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>Klient pro vyhledávání zpráv
Načtěte si pod *Search* (Hledání) [přístupový klíč služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Přidejte importy:
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Vytvořte instanci `CognitiveServicesCredentials`. Vytvořte instanci klienta:
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Vyhledejte výsledky a vypište první výsledek hledávání webových stránek:
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
Použijte filtry a vyhledejte nejnovější zprávy pro výraz „Artificial Intelligence“ (Umělá inteligence) s využitím parametrů `freshness` a `sortBy`. Zkontrolujte počet výsledků a pro první položku výsledků hledání zpráv vypište celkový odhadovaný počet shod (`totalEstimatedMatches`), název (`name`), adresu URL (`url`), popis (`description`), datum publikování (`published time`) a název zprostředkovatele (`name of provider`).
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
Vyhledejte zprávy z kategorie filmů a televizní zábavy s využitím bezpečného hledání. Zkontrolujte počet výsledků a pro první položku výsledků hledání zpráv vypište kategorii (`category`), název (`name`), adresu URL (`url`), popis (`description`), datum publikování (`published time`) a název zprostředkovatele (`name of provider`).
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
Vyhledejte populární témata zpráv v Bingu.  Zkontrolujte počet výsledků a pro první výsledek hledání zpráv vypište název (`name`), text dotazu (`text of query`), adresu URL webu (`webSearchUrl`), adresu URL zprávy (`newsSearchUrl`) a adresu URL obrázku (`image Url`).
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

## <a name="next-steps"></a>Další kroky

[Ukázky kognitivních služeb sady Python SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


