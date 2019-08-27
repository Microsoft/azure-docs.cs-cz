---
title: 'Rychlý start: Hledání imagí – Vyhledávání obrázků Bingu SDK pro Java'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete vytvořit své první vyhledávání obrázků pomocí sady SDK Vyhledávání obrázků Bingu, což je obálka pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace Javy posílá dotaz pro vyhledání obrázku, parsuje odpověď JSON a zobrazuje adresu URL prvního nalezeného obrázku.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 08/26/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 0a9e9a4ee42d36e0ae6da3e92e12ae4144f7b670
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034468"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-java"></a>Rychlý start: Hledání imagí pomocí sady Vyhledávání obrázků Bingu SDK pro jazyk Java

Pomocí tohoto rychlého startu můžete vytvořit své první vyhledávání obrázků pomocí sady SDK Vyhledávání obrázků Bingu, což je obálka pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace Javy posílá dotaz pro vyhledání obrázku, parsuje odpověď JSON a zobrazuje adresu URL prvního nalezeného obrázku.

Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) s dalším zpracováním chyb a poznámkami.

## <a name="prerequisites"></a>Požadavky
Načtěte si pod **Search** (Hledání) [přístupový klíč služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).  Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Nejnovější verze sady [Java Development Kit](https://aka.ms/azure-jdks) (JDK)

Nainstalujte si závislosti sady SDK Vyhledávání obrázků Bingu pomocí systému Maven, Gradle nebo jiného systému správy závislostí. Soubor POM Mavenu vyžaduje následující deklaraci:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyhledávání obrázků Bingu – kurz jednostránkové aplikace](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Vyzkoušet online interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání přístupového klíče služeb Cognitive Services zdarma](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Ukázky Java pro sadu Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Dokumentace Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
