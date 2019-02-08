---
title: 'Rychlý start: Vyhledávání entit Bingu sady SDK, Python'
titlesuffix: Azure Cognitive Services
description: Nastavení pro konzolovou aplikaci sady SDK Bingu pro vyhledávání entit.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: a2d901c1cec4556e4061c21548d7a695c06e8cf8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861744"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Rychlý start: Vyhledávání entit Bingu SDK pomocí Pythonu

V tomto rychlém startu pomocí zahájíte hledání entit sadou SDK pro vyhledávání entit Bingu pro Python. Při vyhledávání entit Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Požadavky

* Python [2.x nebo 3.x](https://www.python.org/)

* [Vyhledávání entit Bingu sady SDK pro Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Doporučuje se, že používáte virtuální prostředí python. Můžete nainstalovat a inicializovat virtuální prostředí pomocí venv modulu. Můžete nainstalovat virtualenv for Python 2.7 pomocí:

```Console
python -m venv mytestenv
```

Nainstalujte sadu SDK s vyhledávání entit Bingu:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve vašich oblíbených prostředím IDE nebo editorem a přidejte následující příkazy pro import. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Vytvořte proměnnou pro váš klíč předplatného a vytvoření instance klienta tak, že vytvoříte nový `CognitiveServicesCredentials` objektu s ním.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Odeslat žádost o vyhledávání a přijetí odpovědi

1. Odeslat požadavek hledání na Bingu pro vyhledávání entit s `client.entities.search()` a vyhledávací dotaz. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Pokud byly vráceny entity, převeďte `entity_data.entities.value` na seznamu a tisk první výsledek.
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

* [Co je API pro vyhledávání entit Bingu?](../overview.md )