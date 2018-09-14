---
title: 'Rychlý start: Vyhledávání obrázků s využitím SDK vyhledávání obrázků Bingu a Python'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijte k vyhledání a Image můžete vyhledat na webu pomocí SDK vyhledávání obrázků Bingu a Python.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 4a24f1e4e051b627034f1d4664e94e0f47c43014
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578289"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-python"></a>Rychlý start: Vyhledávání obrázků pomocí SDK vyhledávání obrázků Bingu a Pythonu.

V tomto rychlém startu používejte k vytvoření vaší první vyhledávání obrázků pomocí SDK vyhledávání Bingu Image, který tvoří obálku pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace Python odešle dotaz vyhledávání obrázků, analyzuje odpověď JSON a zobrazí adresa URL první obrázku vrátil.

Zdrojový kód pro tuto ukázku je k dispozici [na Githubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) zpracování dalších chyb a poznámky.

## <a name="prerequisites"></a>Požadavky 

* [Python 2.7 nebo 3.4](https://www.python.org/) a vyšší.

* [Imagí Azure Search SDK](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) pro Python
    * Nainstalovat s použitím `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytváření a inicializace aplikace

1. Vytvořte nový skript Pythonu v oblíbeném prostředí IDE nebo editoru a následující importy:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Vytváření proměnných pro klíč a hledání období předplatného.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Vytvoření klienta vyhledávání obrázků

3. Vytvoření instance `CognitiveServicesCredentials`a použít ho k vytvoření instance klienta:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
4. Odešlete vyhledávací dotaz na rozhraní API Bingu pro vyhledávání obrázků:
    ```python
    image_results = client.images.search(query=search_term)
    ```
## <a name="process-and-view-the-results"></a>Zpracování a zobrazit výsledky

Analyzovat výsledky hledání obrázků, vrátila v odpovědi.


Pokud odpověď obsahuje výsledky hledání, uloží první výsledek a vytiskne jeho podrobnosti, jako je například miniatury adresa URL, původní adresu URL, společně s celkový počtem vrátil bitové kopie.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz jednostránkovou aplikaci pro vyhledávání obrázků Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je pro vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Zkuste online interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání bezplatné přístupového klíče služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Ukázky Pythonu pro Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Dokumentace ke službě Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční dokumentace rozhraní API pro vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)