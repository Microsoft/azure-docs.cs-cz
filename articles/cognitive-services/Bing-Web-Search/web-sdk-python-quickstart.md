---
title: Rychlé spuštění webové Python SDK vyhledávání | Microsoft Docs
description: Instalační program pro Web vyhledávání SDK konzolové aplikace.
titleSuffix: Azure Cognitive Services Web search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 2a5fed58be863b882b827dbed73862bc690bab1e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343669"
---
# <a name="web-search-sdk-python-quickstart"></a>Rychlé spuštění webové Python SDK vyhledávání

Sada SDK vyhledávání webové služby Bing obsahuje funkci rozhraní REST API pro webové dotazy a analýzy výsledků. 

[Zdrojový kód pro Python Bing Web vyhledávání SDK ukázky](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/web_search_samples.py) je k dispozici v centru Git.

## <a name="application-dependencies"></a>Závislosti aplikací
Pokud ji nemáte, nainstalujte Python. Sada SDK je kompatibilní s Python 2.7, 3.3, 3.4, 3.5 a 3.6.

Obecná doporučení pro vývoj Python je použití [virtuální prostředí](https://docs.python.org/3/tutorial/venv.html). Instalace a inicializace virtuálního prostředí s [venv modulu](https://pypi.python.org/pypi/virtualenv). Je nutné nainstalovat virtualenv pro Python 2.7.
```
python -m venv mytestenv
```
Instalace sady SDK vyhledávání webové služby Bing závislosti:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```
## <a name="web-search-client"></a>Webový klient vyhledávání
Získání [kognitivní služby přístupový klíč](https://azure.microsoft.com/try/cognitive-services/) pod *vyhledávání*. Přidejte importy a vytvořit její instanci `CognitiveServicesCredentials`:
```
from azure.cognitiveservices.search.websearch import WebSearchAPI
from azure.cognitiveservices.search.websearch.models import SafeSearch
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Potom vytvořte instanci klienta:
```
client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Hledat výsledky a tisku první výsledek webové stránky:
```
web_data = client.web.search(query="Yosemite")
print("\r\nSearched for Query# \" Yosemite \"")

# WebPages
if web_data.web_pages.value:

    print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

    first_web_page = web_data.web_pages.value[0]
    print("First web page name: {} ".format(first_web_page.name))
    print("First web page URL: {} ".format(first_web_page.url))

else:
    print("Didn't see any Web data..")
```
Tisk jiné typy výsledků, včetně obrázků, novinky a videa:
```
# Images
if web_data.images.value:

    print("\r\nImage Results#{}".format(len(web_data.images.value)))

    first_image = web_data.images.value[0]
    print("First Image name: {} ".format(first_image.name))
    print("First Image URL: {} ".format(first_image.url))

else:
    print("Didn't see any Image..")
        
# News
if web_data.news.value:

    print("\r\nNews Results#{}".format(len(web_data.news.value)))

    first_news = web_data.news.value[0]
    print("First News name: {} ".format(first_news.name))
    print("First News URL: {} ".format(first_news.url))

else:
    print("Didn't see any News..")
            
# Videos
if web_data.videos.value:

    print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

    first_video = web_data.videos.value[0]
    print("First Videos name: {} ".format(first_video.name))
    print("First Videos URL: {} ".format(first_video.url))

else:
    print("Didn't see any Videos..")

```
Vyhledejte (nejlepší restaurace v Praze), zkontrolujte počet výsledků a vytiskněte `name` a `URL` první výsledku.
```
def web_results_with_count_and_offset(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
        print("\r\nSearched for Query# \" Best restaurants in Seattle \"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))```

```
Vyhledejte "xbox" s `response_filter` přiřazené `News`.  Tisk podrobností výsledků zprávy.
```
def web_search_with_response_filter(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="xbox", response_filter=["News"])
        print("\r\nSearched for Query# \" xbox \" with response filters \"News\"")

        # News attribute since I filtered "News"
        if web_data.news.value:

            print("Webpage Results#{}".format(len(web_data.news.value)))

            first_web_page = web_data.news.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Vyhledávání pomocí dotazu "Niagara spadá", pomocí `answerCount` a `promote` parametry. Tisk podrobností výsledků.
```
def web_search_with_answer_count_promote_and_safe_search(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(
            query="Niagara Falls",
            answer_count=2,
            promote=["videos"],
            safe_search=SafeSearch.strict  # or directly "Strict"
        )
        print("\r\nSearched for Query# \" Niagara Falls\"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
## <a name="next-steps"></a>Další postup

[Ukázky kognitivní služby Python SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


