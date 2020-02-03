---
title: 'Rychlý Start: hledání entit pomocí sady SDK pro jazyk Java – Vyhledávání entit Bingu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete vyhledat entity pomocí sady Vyhledávání entit Bingu SDK pro jazyk Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 49e3492bae1dfe248e07227caff11a6d00e59c08
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716469"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Rychlý Start: odeslání žádosti o vyhledávání pomocí sady Vyhledávání entit Bingu SDK pro jazyk Java

V tomto rychlém startu můžete začít vyhledávat entity pomocí sady Vyhledávání entit Bingu SDK pro jazyk Java. I když Vyhledávání entit Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Předpoklady

* [Sada Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* Sada Vyhledávání entit Bingu SDK pro jazyk Java

Nainstalujte si závislosti sady SDK Bingu pro vyhledávání entit pomocí systému Maven, Gradle nebo jiného systému správy závislostí. Soubor POM Mavenu vyžaduje deklaraci:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java a naimportujte následující knihovny.

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. Vytvoření proměnné pro klíč předplatného

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Vytvoření vyhledávacího klienta

1. Implementujte klienta `dominantEntityLookup`, který vyžaduje koncový bod rozhraní API a instanci `ServiceClientCredentials` třídy. Můžete použít globální koncový bod nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    K implementaci `ServiceClientCredentials`použijte následující postup:

   1. Přepište funkci `applyCredentialsFilter()` s objektem `OkHttpClient.Builder` jako parametr. 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. V rámci `applyCredentialsFilter()`volejte `builder.addNetworkInterceptor()`. Vytvořte nový objekt `Interceptor` a přepište jeho `intercept()` metodu pro převzetí `Chain`ho objektu zachytávací.

       ```java
       //...
       builder.addNetworkInterceptor(
           new Interceptor() {
               @Override
               public Response intercept(Chain chain) throws IOException {
               //...    
               }
           });
       ///...
       ```

   3. V rámci funkce `intercept` vytvořte proměnné pro svůj požadavek. K sestavení vaší žádosti použijte `Request.Builder()`. Přidejte klíč předplatného do hlavičky `Ocp-Apim-Subscription-Key` a vraťte `chain.proceed()` objektu Request.
            
       ```java
       //...
       public Response intercept(Chain chain) throws IOException {
           Request request = null;
           Request original = chain.request();
           Request.Builder requestBuilder = original.newBuilder()
                   .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
           request = requestBuilder.build();
           return chain.proceed(request);
       }
       //...
       ```
      ## <a name="send-a-request-and-receive-a-response"></a>Odeslat žádost a přijmout odpověď

1. Vytvořte novou instanci klienta Search s vaším klíčem předplatného. k odeslání žádosti o vyhledávání `satya nadella`vyhledávacího dotazu použijte `client.entities().search()` a získejte odpověď. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. Pokud byly vráceny nějaké entity, převeďte je na seznam. Iterujte je prostřednictvím nich a vytiskněte dominantní entitu.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entries = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entries) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s jednou stránkou](../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání entit?](../overview.md )
