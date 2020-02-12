---
title: 'Rychlý Start: volání koncového bodu Vlastní vyhledávání Bingu pomocí sady Python SDK'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu pomocí sady Python SDK.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: d67075fad719b1780682c705f0e17f15c5801559
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136152"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Rychlý Start: volání koncového bodu Vlastní vyhledávání Bingu pomocí sady Python SDK 

Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu pomocí sady Python SDK. I když Vlastní vyhledávání Bingu má REST API kompatibilní s většinou programovacích jazyků, Vlastní vyhledávání Bingu SDK poskytuje snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) s dalšími zpracováním chyb a poznámkami.

## <a name="prerequisites"></a>Předpoklady

- Instance Vlastní vyhledávání Bingu. Další informace najdete v tématu [rychlý Start: Vytvoření první instance vlastní vyhledávání Bingu](quick-start.md) .
- Python [2. x nebo 3. x](https://www.python.org/) 
- [Sada SDK vlastní vyhledávání Bingu pro Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Instalace sady Python SDK

Pomocí následujícího příkazu nainstalujte sadu Vlastní vyhledávání Bingu SDK.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Vytvoření nové aplikace

Vytvořte nový soubor Pythonu v oblíbených editorech nebo v integrovaném vývojovém prostředí a přidejte následující importy.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Vytvoření vyhledávacího klienta a odeslání žádosti

1. Vytvořte proměnnou pro klíč předplatného.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-custom-endpoint'
    ```

2. Vytvořte instanci `CustomSearchClient`pomocí objektu `CognitiveServicesCredentials` s klíčem předplatného. 

    ```python
    client = CustomSearchClient(endpoint, CognitiveServicesCredentials(subscription_key))
    ```

3. Odešlete žádost o hledání pomocí `client.custom_instance.search()`. Přidejte hledaný termín do parametru `query` a nastavte `custom_config` na vlastní ID konfigurace, aby bylo možné použít vaši instanci hledání. Své ID můžete získat z [portálu vlastní vyhledávání Bingu](https://www.customsearch.ai/)kliknutím na kartu **Výroba** .

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Zobrazení výsledků hledání

Pokud byly nalezeny jakékoli výsledky hledání webové stránky, získejte první z nich a vytiskněte svůj název, adresu URL a nalezené webové stránky.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření vlastní vyhledávací webové aplikace](./tutorials/custom-search-web-page.md)
