---
title: Rychlý Start klientské knihovny Vyhledávání obrázků Bingu Pythonu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: d5d47f097fa216d69b8ed59fdb057378724c2228
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625412"
---
Pomocí tohoto rychlého startu můžete udělat své první hledání obrázků pomocí klientské knihovny Vyhledávání obrázků Bingu, což je obálka pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace Pythonu posílá dotaz pro vyhledání obrázku, parsuje odpověď JSON a zobrazuje adresu URL prvního nalezeného obrázku.

Zdrojový kód pro tuto ukázku je k dispozici [na GitHubu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) s dalšími zpracováním chyb a poznámkami.

## <a name="prerequisites"></a>Předpoklady

* [Python 2.7 nebo 3.4](https://www.python.org/) a vyšší.

* [Klientská knihovna pro Azure vyhledávání obrázků](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) pro Python
    * Dá se nainstalovat pomocí `pip install azure-cognitiveservices-search-imagesearch`.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Ve vašem oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový skript Pythonu a přidejte následující importy:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Vytvořte proměnné pro klíč předplatného a hledaný výraz.

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Vytvoření klienta vyhledávání obrázků

1. Vytvořte instanci třídy `CognitiveServicesCredentials` a použijte ji k vytvoření instance klienta:

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```
1. Odešlete vyhledávací dotaz do rozhraní API Bingu pro vyhledávání obrázků:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>Zpracování a zobrazení výsledků

Parsujte výsledky obrázků vrácené v odpovědi.


Pokud odpověď obsahuje výsledky hledání, uloží se první výsledek a vytisknou se jeho podrobnosti, třeba adresa URL miniatury a původní adresa URL, společně s celkovým počtem nalezených obrázků.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledávání obrázků Bingu – kurz jednostránkové aplikace](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Viz také

* [Co je Vyhledávání obrázků Bingu?](../../overview.md)  
* [Vyzkoušet online interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ukázky Pythonu pro sadu Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Dokumentace ke službě Azure Cognitive Services](../../../index.yml)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)