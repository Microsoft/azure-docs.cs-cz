---
title: Rychlý Start klientské knihovny Vlastní vyhledávání Bingu Pythonu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "78252881"
---
Začněte s knihovnou klienta Vlastní vyhledávání Bingu pro Python. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Rozhraní API pro vlastní vyhledávání Bingu vám umožní vytvářet v přizpůsobených prostředích vyhledávání bez reklamy pro témata, která vás zajímají. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Použití klientské knihovny Vlastní vyhledávání Bingu pro Python pro:
* Vyhledá výsledky hledání na webu z vaší instance Vlastní vyhledávání Bingu.

[Referenční dokumentace](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch)  |  [Balíček (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/)  |  [Ukázky](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Předpoklady

- Instance Vlastní vyhledávání Bingu. Další informace najdete v tématu [rychlý Start: Vytvoření první instance vlastní vyhledávání Bingu](../../quick-start.md) .
- Python [2. x nebo 3. x](https://www.python.org/) 
- [Sada SDK vlastní vyhledávání Bingu pro Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Instalace klientské knihovny Pythonu

Nainstalujte knihovnu klienta Vlastní vyhledávání Bingu pomocí následujícího příkazu.

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

1. Vytvořte proměnnou pro klíč předplatného a koncový bod.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Vytvořte instanci `CustomSearchClient` pomocí `CognitiveServicesCredentials` objektu s klíčem předplatného. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Odešlete žádost o vyhledávání pomocí `client.custom_instance.search()` . Přidejte hledaný termín k `query` parametru a nastavte `custom_config` na vlastní ID konfigurace, aby bylo možné použít vaši instanci hledání. Své ID můžete získat z [portálu vlastní vyhledávání Bingu](https://www.customsearch.ai/)kliknutím na kartu **Výroba** .

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
> [Vytvoření vlastní vyhledávací webové aplikace](../../tutorials/custom-search-web-page.md)
