---
title: 'Rychlý start: Vyhledávání obrázků s využitím SDK vyhledávání obrázků Bingu a Java'
description: V tomto rychlém startu použijte k vyhledání a Image můžete vyhledat na webu pomocí SDK vyhledávání obrázků Bingu a Java.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 12bd6f9a9a0b43b4571a7e0311ffbea54c7b9054
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574057"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-java"></a>Rychlý start: Vyhledávání obrázků pomocí SDK vyhledávání obrázků Bingu a Javy.

V tomto rychlém startu používejte k vytvoření vaší první vyhledávání obrázků pomocí SDK vyhledávání Bingu Image, který tvoří obálku pro rozhraní API a obsahuje stejné funkce. Tato jednoduchá aplikace Java odešle dotaz vyhledávání obrázků, analyzuje odpověď JSON a zobrazí adresa URL první obrázku vrátil.

Zdrojový kód pro tuto ukázku je k dispozici [na Githubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) zpracování dalších chyb a poznámky. 

## <a name="prerequisites"></a>Požadavky 

Nejnovější verzi [Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)

Instalace závislostí SDK vyhledávání obrázků Bingu pomocí Maven, Gradle nebo jiný systém správy závislostí. Souboru Maven POM vyžaduje následující deklarace:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
 </dependencies> 
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytváření a inicializace aplikace

1. Vytvoření nového projektu v Javě ve vašich oblíbených prostředím IDE nebo editorem a přidejte následující importy do vaší třídy implimentation:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. V hlavní metodě vytváření proměnných pro váš klíč předplatného a hledané výrazy. Potom vytvořte instanci klienta Bingu pro vyhledávání obrázků.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-bing-image-search-api"></a>Odeslat žádost o vyhledávání do rozhraní API Bingu pro vyhledávání obrázků

1. Pomocí `bingImages().search()`, odeslání požadavku HTTP obsahující vyhledávacího dotazu. Uložit jako odpověď `ImagesModel`.
    ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Analyzovat a zobrazit výsledek

Analyzovat výsledky hledání obrázků, vrátila v odpovědi.
Pokud odpověď obsahuje výsledky hledání, uloží první výsledek a vytiskne jeho podrobnosti, jako je například miniatury adresa URL, původní adresu URL, společně s celkový počtem vrátil bitové kopie.  

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
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz jednostránkovou aplikaci pro vyhledávání obrázků Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je pro vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Zkuste online interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání bezplatné přístupového klíče služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api) 
* [Ukázky v Javě pro Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
* [Dokumentace ke službě Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční dokumentace rozhraní API pro vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
