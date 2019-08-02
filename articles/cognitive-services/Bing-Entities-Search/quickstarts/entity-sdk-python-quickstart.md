---
title: 'Rychlý start: Hledání entit s Vyhledávání entit Bingu SDK pro Python'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete vyhledat entity s Vyhledávání entit Bingu SDK pro Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 0a4a4e3ce6f318a693b8ee760a0d892eaecb2c82
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479005"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Rychlý start: Vyhledávání entit Bingu SDK pomocí Pythonu

V tomto rychlém startu můžete začít vyhledávat entity pomocí Vyhledávání entit Bingu SDK pro Python. I když Vyhledávání entit Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na GitHubu. [](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py)

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

2. Vytvořte proměnnou pro svůj klíč předplatného a vytvořte instanci klienta vytvořením nového `CognitiveServicesCredentials` objektu.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Odeslat žádost o vyhledávání a přijmout odpověď

1. Odešlete žádost o vyhledávání do vyhledávání entit Bingu `client.entities.search()` pomocí vyhledávacího dotazu. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Pokud byly vráceny entity, převeďte `entity_data.entities.value` je na seznam a vytiskněte první výsledek.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání entit?](../overview.md )