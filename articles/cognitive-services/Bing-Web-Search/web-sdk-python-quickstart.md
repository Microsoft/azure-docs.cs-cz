---
title: 'Rychlý start: Použití sady SDK Bingu pro vyhledávání na webu pro Python'
titleSuffix: Azure Cognitive Services
description: Sada SDK Bingu pro vyhledávání na webu usnadňuje integraci webového vyhledávání Bingu do vaší aplikace v Pythonu. V tomto rychlém startu se naučíte poslat požadavek, přijmout odpověď JSON, filtrovat a parsovat výsledky.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: c83848bc3c8f14aa49c1f87f1cd44224bb3e8081
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127631"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-python"></a>Rychlý start: Použití sady SDK Bingu pro vyhledávání na webu pro Python

Sada SDK Bingu pro vyhledávání na webu usnadňuje integraci webového vyhledávání Bingu do vaší aplikace v Pythonu. V tomto rychlém startu se naučíte poslat požadavek, přijmout odpověď JSON, filtrovat a parsovat výsledky.

Chcete se rovnou podívat na kód? [Ukázky sady SDK Bingu pro vyhledávání na webu pro Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) jsou k dispozici na GitHubu.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Požadavky

Sada SDK Bingu pro vyhledávání na webu je kompatibilní s Pythonem 2.7, 3.3, 3.4, 3.5 a 3.6. Pro tento článek Rychlý start doporučujeme použít virtuální prostředí.

* Python 2.7, 3.3, 3.4, 3.5 nebo 3.6
* [virtualenv](https://docs.python.org/3/tutorial/venv.html) pro Python 2.7
* [venv](https://pypi.python.org/pypi/virtualenv) pro Python 3.x

## <a name="create-and-configure-your-virtual-environment"></a>Vytvoření a konfigurace virtuálního prostředí

Pokyny k nastavení a konfiguraci virtuálního prostředí se liší pro python 2.x a Python 3.x. K vytvoření a inicializaci virtuálního prostředí použijte následující postup.

### <a name="python-2x"></a>Python 2.x

Vytvoření virtuálního prostředí příkazem `virtualenv` Pythonu 2.7:

```console
virtualenv mytestenv
```

Aktivace prostředí:

```console
cd mytestenv
source bin/activate
```

Instalace závislostí sady SDK Bingu pro vyhledávání na webu:

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

Vytvoření virtuálního prostředí příkazem `venv` Pythonu 3.x:

```console
python -m venv mytestenv
```

Instalace závislostí sady SDK Bingu pro vyhledávání na webu:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>Vytvoření klienta a vytištění prvních výsledků

Nastavili jste virtuální prostředí a nainstalovali závislosti. Teď vytvoříme klienta. Klient bude zpracovávat požadavky a odpovědi rozhraní API Bingu pro vyhledávání na webu.

Pokud odpověď obsahuje webové stránky, obrázky, zprávy nebo videa, vytiskne se první výsledek každé kategorie.

1. Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru.
2. Tento ukázkový kód zkopírujte do svého projektu:  
    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchAPI
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client.
    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```
3. Hodnotu `subscription_key` nahraďte platným klíčem předplatného.
4. Spusťte program. Například: `python your_program.py`.

## <a name="define-functions-and-filter-results"></a>Definice funkcí a filtrování výsledků

Po prvním volání rozhraní API Bingu pro vyhledávání na webu se pojďme podívat na několik funkcí, které doplňují funkce sady SDK o přesnější dotazy a filtrování výsledků. Každou funkci můžete přidat do programu v Pythonu, který jste vytvořili v předchozí části.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Omezení počtu výsledků vrácených Bingem

V této ukázce jsou použité parametry `count` a `offset` k omezení počtu výsledků vrácených [metodou `search`](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search) sady SDK. Vytiskne se `name` a `URL` prvního výsledku.

1. Do projektu v Pythonu přidejte tento kód:
    ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```
2. Spusťte program.

### <a name="filter-for-news-and-freshness"></a>Filtrování zpráv podle stáří

V této ukázce použijeme parametry `response_filter` a `freshness` k filtrování výsledků hledání vrácených [metodou `search`](https://docs.microsoft.com//api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search) sady SDK. Vrácené výsledky hledání jsou omezené na nové zprávy a stránky, které Bing našel za posledních 24 hodin. Vytiskne se `name` a `URL` prvního výsledku.

1. Do projektu v Pythonu přidejte tento kód:
    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```
2. Spusťte program.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Použití bezpečného hledání, počtu odpovědí a propagačního filtru

V této ukázce použijeme parametry `answer_count`, `promote` a `safe_search` k filtrování výsledků hledání vrácených [metodou `search`](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search) sady SDK. Zobrazí se `name` a `URL` prvního výsledku.

1. Do projektu v Pythonu přidejte tento kód:
    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
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
2. Spusťte program.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až projekt dokončíte, nezapomeňte z kódu programu odebrat klíč předplatného a deaktivovat virtuální prostředí.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ukázky kognitivních služeb sady Python SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="see-also"></a>Viz také

* [Referenční informace k sadě Azure Python SDK](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/websearch)
