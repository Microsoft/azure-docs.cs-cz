---
title: 'Rychlý start: Hledat entity sadou SDK vyhledávání entit Bingu pro Javu'
titlesuffix: Azure Cognitive Services
description: Použít v tomto rychlém startu pro vyhledávání entit s SDK vyhledávání entit Bingu pro jazyk Java
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 1534b2a85067867800c147c86ee7d82374de8794
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866191"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Rychlý start: Odeslat žádost o vyhledávání sadou SDK vyhledávání entit Bingu pro Javu

Použijte v tomto rychlém startu má být prohledávání u entit s SDK vyhledávání entit Bingu pro jazyk Java. Při vyhledávání entit Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Požadavky

* [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* Vyhledávání entit Bingu sady SDK pro Javu

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

2. Vytvořte proměnnou pro váš klíč předplatného

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Vytvoření klienta vyhledávání

2. Implementace `dominantEntityLookup` klienta, které vyžaduje váš koncový bod rozhraní API a instance `ServiceClientCredentials` třídy.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    K implementaci `ServiceClientCredentials`, postupujte podle těchto kroků:

    1. Přepsat `applyCredentialsFilter()` funkce, se `OkHttpClient.Builder` objektu jako parametr. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. V rámci `applyCredentialsFilter()`, volání `builder.addNetworkInterceptor()`. Vytvořte nový `Interceptor` objektu a přepsat její `intercept()` metoda trvat `Chain` zachycování objektu.

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

    3. V rámci `intercept` funkce, vytváření proměnných pro vaši žádost. Použití `Request.Builder()` na vaši žádost o sestavení. Přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví a vraťte se `chain.proceed()` na objekt žádosti.
            
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
## <a name="send-a-request-and-receive-a-response"></a>Žádost o odeslání a přijetí odpovědi

1. Vytvořte novou instanci třídy klienta vyhledávání s klíči předplatného. použít `client.entities().search()` odeslat žádost o vyhledávání vyhledávacího dotazu `satya nadella`a získat odpověď. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

2. Nebyly vráceny žádné entity, převeďte je do seznamu. Iterovat přes ně a vytisknout dominantní entity.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entrys = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entrys) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }

## Next steps

> [!div class="nextstepaction"]
> [Build a single-page web app](../tutorial-bing-entities-search-single-page-app.md)

* [What is the Bing Entity Search API?](../overview.md )