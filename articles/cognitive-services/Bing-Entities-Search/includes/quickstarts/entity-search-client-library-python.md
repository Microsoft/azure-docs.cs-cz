---
title: Rychlý Start klientské knihovny Vyhledávání entit Bingu Pythonu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "79136757"
---
Pomocí tohoto rychlého startu můžete začít vyhledávat entity pomocí klientské knihovny Vyhledávání entit Bingu pro Python. I když Vyhledávání entit Bingu má REST API kompatibilní s většinou programovacích jazyků, Klientská knihovna poskytuje snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Předpoklady

* Python [2. x nebo 3. x](https://www.python.org/)

* [Sada SDK vyhledávání entit Bingu pro Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Doporučuje se používat virtuální prostředí Python. Virtuální prostředí můžete nainstalovat a inicializovat pomocí modulu venv. Virtualenv můžete nainstalovat pomocí:

```Console
python -m venv mytestenv
```

Nainstalujte knihovnu klienta Vyhledávání entit Bingu pomocí nástroje:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu v oblíbeném prostředí IDE nebo editoru a přidejte následující příkazy importu. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Vytvořte proměnnou pro klíč předplatného a koncový bod. Vytvořte instanci klienta vytvořením nového `CognitiveServicesCredentials` objektu s klíčem.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Odeslat žádost o vyhledávání a přijmout odpověď

1. Odešlete žádost o vyhledávání do Vyhledávání entit Bingu pomocí `client.entities.search()` vyhledávacího dotazu. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Pokud byly vráceny entity, převeďte je `entity_data.entities.value` na seznam a vytiskněte první výsledek.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání entit?](../../overview.md )