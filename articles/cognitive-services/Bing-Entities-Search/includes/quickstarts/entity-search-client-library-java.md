---
title: Rychlý Start klientské knihovny s Vyhledávání entit Bingu Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 8c987aa14e922573d01aa35fab609edf01e109b4
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79136771"
---
Pomocí tohoto rychlého startu můžete začít vyhledávat entity pomocí Vyhledávání entit Bingu klientské knihovny pro Java. I když Vyhledávání entit Bingu má REST API kompatibilní s většinou programovacích jazyků, Klientská knihovna poskytuje snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Požadavky

* [Sada Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* Klientská knihovna Vyhledávání entit Bingu pro Java

Nainstalujte závislosti Vyhledávání entit Bingu klientské knihovny pomocí nástroje Maven, Gradle nebo jiného systému pro správu závislostí. Soubor POM Mavenu vyžaduje deklaraci:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


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

1. Implementujte `dominantEntityLookup` klienta, který vyžaduje koncový bod rozhraní API, a instanci `ServiceClientCredentials` třídy. Můžete použít globální koncový bod nebo vlastní koncový bod [subdomény](../../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    K implementaci rozhraní `ServiceClientCredentials`použijte následující postup:

   1. potlačí `applyCredentialsFilter()` funkci s `OkHttpClient.Builder` objektem jako parametr. 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. V `applyCredentialsFilter()`rámci volejte `builder.addNetworkInterceptor()`. Vytvořte nový `Interceptor` objekt a přepište jeho `intercept()` metodu pro převzetí objektu `Chain` zachytávací.

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

   3. V rámci `intercept` funkce vytvořte proměnné pro svůj požadavek. Použijte `Request.Builder()` k sestavení vaší žádosti. Přidejte do `Ocp-Apim-Subscription-Key` záhlaví klíč předplatného a vraťte `chain.proceed()` se k objektu Request.
            
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

1. Vytvořte novou instanci klienta Search s vaším klíčem předplatného. slouží `client.entities().search()` k odeslání žádosti o vyhledávání vyhledávacího dotazu `satya nadella`a získání odpovědi. 
    
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
> [Sestavení jednostránkové webové aplikace](../../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání entit?](../../overview.md)
