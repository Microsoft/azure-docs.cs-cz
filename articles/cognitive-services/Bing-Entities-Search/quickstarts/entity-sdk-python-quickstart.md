---
title: 'Rychlý Start: hledání entit pomocí sady SDK pro Python – Vyhledávání entit Bingu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete vyhledat entity s Vyhledávání entit Bingu SDK pro Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 33ab516b5b501a79ba84c5aba9c3231634f3c662
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448699"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Rychlý start: Sada SDK Bingu pro vyhledávání entit s využitím Pythonu

V tomto rychlém startu můžete začít vyhledávat entity pomocí Vyhledávání entit Bingu SDK pro Python. I když Vyhledávání entit Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py)

## <a name="prerequisites"></a>Požadavky

* Python [2. x nebo 3. x](https://www.python.org/)

* [Sada SDK vyhledávání entit Bingu pro Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Doporučuje se používat virtuální prostředí Python. Virtuální prostředí můžete nainstalovat a inicializovat pomocí modulu venv. Virtualenv můžete nainstalovat pomocí:

```Console
python -m venv mytestenv
```

Nainstalujte sadu Vyhledávání entit Bingu SDK pomocí:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu v oblíbeném prostředí IDE nebo editoru a přidejte následující příkazy importu. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Vytvořte proměnnou pro klíč předplatného a vytvořte instanci klienta vytvořením nového objektu `CognitiveServicesCredentials`.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Odeslat žádost o vyhledávání a přijmout odpověď

1. Odešlete žádost o vyhledávání do Vyhledávání entit Bingu s `client.entities.search()` a vyhledávacím dotazem. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Pokud byly vráceny entity, převeďte `entity_data.entities.value` na seznam a vytiskněte první výsledek.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání entit?](../overview.md )