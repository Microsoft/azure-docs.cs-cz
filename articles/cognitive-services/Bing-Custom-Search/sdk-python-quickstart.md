---
title: 'Rychlý start: Volání vlastního vyhledávání Bingu koncový bod pomocí sady Python SDK | Dokumentace Microsoftu'
titleSuffix: Azure Cognitive Services
description: Získat vlastní výsledky hledání pomocí SDK vlastní vyhledávání Bingu pro Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: c4c5059bc57ea33357145f6b119456dc6c5bdb7b
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571892"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Rychlý start: Volání vlastního vyhledávání Bingu koncový bod pomocí sady Python SDK 

V tomto rychlém startu můžete začít si vyžádat výsledky hledání z vaší instance vlastního vyhledávání Bingu, pomocí sady Python SDK. Zatímco vlastní vyhledávání Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, SDK vlastní vyhledávání Bingu poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) zpracování dalších chyb a poznámky.

## <a name="prerequisites"></a>Požadavky

- Instanci vlastního vyhledávání Bingu. Zobrazit [rychlý start: Vytvoření první instanci vlastního vyhledávání Bingu](quick-start.md) Další informace.
- Python [2.x nebo 3.x](https://www.python.org/) 
- [Vlastní vyhledávání Bingu sady SDK pro Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Nainstalovat Python SDK

Instalace sady SDK vlastní vyhledávání Bingu pomocí následujícího příkazu.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Vytvoření nové aplikace

Vytvořte nový soubor Pythonu v oblíbeném editoru nebo prostředí IDE a přidejte následující importy.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Vytvoření klienta vyhledávání a odeslat žádost

1. Vytvořte proměnnou pro váš klíč předplatného.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Vytvoření instance `CustomSearchClient`, použití `CognitiveServicesCredentials` objekt s klíči předplatného. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. Odeslat žádost o vyhledávání s `client.custom_instance.search()`. Připojit hledaný termín `query` parametr a nastavte `custom_config` ID vlastní konfigurace pro použití vašeho hledání instance. Můžete získat ID z vašeho [vlastní vyhledávání Bingu portál](https://www.customsearch.ai/), kliknutím **produkční** kartu.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Zobrazení výsledků hledání

Pokud nebyly nalezeny žádné výsledky hledání webové stránky, získat první z nich a tisk jeho název, adresu URL a celkový počet webových stránek nalezen.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s vlastní vyhledávání](./tutorials/custom-search-web-page.md)
