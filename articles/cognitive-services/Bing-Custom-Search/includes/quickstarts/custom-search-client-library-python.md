---
title: Rychlý start klientské knihovny Klienta Pythonu vlastního hledání Bingu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78252881"
---
Začínáme s klientskou knihovnou vlastního vyhledávacího řešení Bing pro Python. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly. Rozhraní API pro vlastní vyhledávání Bingu umožňuje vytvářet přizpůsobené vyhledávací prostředí bez reklam pro témata, která vás zajímají. Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Pomocí klientské knihovny vlastního vyhledávání Bingu pro Python:
* Najděte výsledky hledání na webu z instance vlastního vyhledávání Bingu.

[Referenční dokumentace](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) | [Ukázky](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/) [zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [knihovny (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | 


## <a name="prerequisites"></a>Požadavky

- Instance vlastního vyhledávání Bingu. Další informace najdete [v tématu Úvodní příručka: Vytvoření první instance vlastního vyhledávání Bingu.](../../quick-start.md)
- Python [2.x nebo 3.x](https://www.python.org/) 
- [Vlastní sada Hledání Bingu pro Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Instalace klientské knihovny Pythonu

Nainstalujte klientskou knihovnu vlastního vyhledávání Bingu pomocí následujícího příkazu.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Vytvoření nové aplikace

Vytvořte nový soubor Pythonu ve svém oblíbeném editoru nebo ide a přidejte následující importy.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Vytvoření vyhledávacího klienta a odeslání požadavku

1. Vytvořte proměnnou pro klíč předplatného a koncový bod.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Vytvořte instanci `CustomSearchClient` `CognitiveServicesCredentials` aplikace pomocí objektu s klíčem předplatného. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Odešlete požadavek na hledání pomocí služby `client.custom_instance.search()`. Přidejte hledaný výraz `query` k parametru a nastavte `custom_config` na vlastní ID konfigurace, abyste použili instanci hledání. ID můžete získat z [portálu vlastního vyhledávání Bingu](https://www.customsearch.ai/)kliknutím na kartu **Produkční.**

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Zobrazení výsledků hledání

Pokud byly nalezeny výsledky hledání webových stránek, získejte první a vytiskněte její název, adresu URL a celkový počet nalezených webových stránek.

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
> [Vytvoření webové aplikace pro vlastní vyhledávání](../../tutorials/custom-search-web-page.md)
