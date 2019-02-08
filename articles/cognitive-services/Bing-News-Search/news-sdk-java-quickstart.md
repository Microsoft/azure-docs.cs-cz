---
title: 'Rychlý start: Hledání zpráv - SDK vyhledávání zpráv Bingu pro jazyk Java'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu slouží k vyhledání zpráv pomocí sady SDK vyhledávání zpráv Bingu pro Javu a zpracovat odpověď.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 0922010ad6993c8bb1571c24595370895e49c498
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866161"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>Rychlý start: Hledání zpráv sadou SDK vyhledávání zpráv Bingu pro Javu

V tomto rychlém startu pomocí zahájíte hledání zpráv pomocí SDK vyhledávání zpráv Bingu pro Javu. Při vyhledávání zpráv Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).

## <a name="prerequisites"></a>Požadavky

Nainstalujte sadu SDK vyhledávání zpráv Bingu závislosti pomocí Maven, Gradle nebo jiný systém správy závislostí. Soubor POM Mavenu vyžaduje následující deklaraci:

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

## <a name="create-a-search-client-and-store-credentials"></a>Vytváření přihlašovacích údajů klienta a úložiště hledání

1. Vytvořit metodu nazvanou `getClient()` , která vrátí nový `NewsSearchAPIImpl` vyhledávání klienta. Přidat koncový bod jako první parametr pro novou`NewsSearchAPIImpl` objektu a nový `ServiceClientCredentials` objekt pro uložení svých přihlašovacích údajů.

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Chcete-li vytvořit `ServiceClientCredentials` objektu, přepsat `applyCredentialsFilter()` funkce. Předat `OkHttpClient.Builder` metody a použít Tvůrce `addNetworkInterceptor()` metoda vytvořte své přihlašovací údaje pro volání sady SDK.

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

## <a name="send-and-receive-a-search-request"></a>Odeslat a přijmout žádost o vyhledávání

1. Vytvoření metody, která volá `getClient()` a odešle požadavek hledání na Bingu pro vyhledávání zpráv služby. Filtrovat hledání s použitím *trhu* a *počet* parametry, pak vytiskněte informace o první výsledek zprávy: název, URL, datum publikování, popis, název zprostředkovatele a celkový počet odhad odpovídá pro příslušné hledání.

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

2. Přidat metodu vyhledávání do `main()` metoda ke spouštění kódu.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
[Vytvoření jednostránkové webové aplikace](tutorial-bing-news-search-single-page-app.md)
