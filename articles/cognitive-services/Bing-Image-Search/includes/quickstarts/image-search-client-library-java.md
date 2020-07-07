---
title: Rychlý Start klientské knihovny s Vyhledávání obrázků Bingu Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: aeb326bbc14dd2c44a0d3ab250bb611a6b8f6b0f
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85805530"
---
Pomocí tohoto rychlého startu můžete udělat své první hledání obrázků pomocí klientské knihovny Vyhledávání obrázků Bingu, což je obálka pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace Javy posílá dotaz pro vyhledání obrázku, parsuje odpověď JSON a zobrazuje adresu URL prvního nalezeného obrázku.

Zdrojový kód pro tuto ukázku je k dispozici [na GitHubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) s dalšími zpracováním chyb a poznámkami.

## <a name="prerequisites"></a>Požadavky

Nejnovější verze sady [Java Development Kit](https://aka.ms/azure-jdks) (JDK)

Nainstalujte závislosti Vyhledávání obrázků Bingu klientské knihovny pomocí nástroje Maven, Gradle nebo jiného systému pro správu závislostí. Soubor POM Mavenu vyžaduje následující deklaraci:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový projekt Java v oblíbeném prostředí IDE nebo editoru a přidejte následující importy do vaší implementace třídy:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. V hlavní metodě vytvořte proměnné pro klíč předplatného a hledaný výraz. Potom vytvořte instanci klienta vyhledávání obrázků Bingu.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>Odeslání žádosti o vyhledávání do rozhraní API

1. Pomocí `bingImages().search()` odešlete požadavek HTTP obsahující vyhledávací dotaz. Uložte odpověď jako `ImagesModel`.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Parsování a zobrazení výsledku

Parsujte výsledky obrázků vrácené v odpovědi.
Pokud odpověď obsahuje výsledky hledání, uložte první výsledek a vytiskněte jeho podrobnosti, jako je například adresa URL miniatury, původní adresa URL a celkový počet vrácených imagí.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledávání obrázků Bingu – kurz jednostránkové aplikace](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Viz také:

* [Co je Vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Vyzkoušet online interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ukázky Java pro sadu Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Dokumentace ke službě Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
