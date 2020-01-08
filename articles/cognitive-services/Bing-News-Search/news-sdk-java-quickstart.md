---
title: 'Rychlý Start: provedení hledání zpráv pomocí sady SDK pro jazyk Java Vyhledávání zpráv Bingu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete vyhledat zprávy pomocí sady Vyhledávání zpráv Bingu SDK pro jazyk Java a zpracovat odpověď.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b8c2ca3bf919d70123b7b5b6d13c0301e0c1fd13
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75383574"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>Rychlý Start: hledání zpráv pomocí sady Vyhledávání zpráv Bingu SDK pro jazyk Java

V tomto rychlém startu můžete začít hledat zprávy s Vyhledávání zpráv Bingu SDK pro jazyk Java. I když Vyhledávání zpráv Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch)

## <a name="prerequisites"></a>Požadavky

Nainstalujte závislosti Vyhledávání zpráv Bingu SDK pomocí Maven, Gradle nebo jiného systému pro správu závislostí. Soubor POM Mavenu vyžaduje následující deklaraci:

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java a naimportujte následující knihovny.

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>Vytvoření vyhledávacího klienta a přihlašovacích údajů pro Store

1. Vytvořte metodu nazvanou `getClient()`, která vrátí nového klienta `NewsSearchAPIImpl` vyhledávání. Přidejte svůj koncový bod jako první parametr nového objektu`NewsSearchAPIImpl` a nový objekt `ServiceClientCredentials` pro uložení vašich přihlašovacích údajů. 

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Chcete-li vytvořit objekt `ServiceClientCredentials`, přepište funkci `applyCredentialsFilter()`. Předat `OkHttpClient.Builder` metodě a použít metodu `addNetworkInterceptor()` Tvůrce k vytvoření přihlašovacích údajů pro volání sady SDK.

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslání a přijetí žádosti o vyhledávání

1. Vytvořte metodu, která volá `getClient()` a odešle požadavek na hledání do služby Vyhledávání zpráv Bingu. Vyfiltrujte hledání pomocí parametrů *trhu* a *počtu* a pak si vytiskněte informace o prvním výsledku zprávy: jméno, URL, datum publikování, popis, název zprostředkovatele a celkový počet odhadovaných shod pro vaše hledání.

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. Chcete-li spustit kód, přidejte metodu hledání do `main()` metody.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
