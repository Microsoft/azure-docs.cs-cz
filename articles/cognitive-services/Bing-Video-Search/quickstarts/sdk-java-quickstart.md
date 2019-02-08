---
title: 'Rychlý start: Hledání videí pomocí Bingu Video Search SDK pro Javu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu používáme k odeslání žádosti pro vyhledávání videí pomocí sady SDK videa pro vyhledávání Bingu pro Javu.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ff214a545023364b09a0e757462a1b3f3d963d44
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885017"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-java"></a>Rychlý start: Vyhledávání videí pomocí sady SDK videa pro vyhledávání Bingu pro Javu

V tomto rychlém startu můžete zahájit hledání zpráv se Video Bingu hledání sady SDK pro Javu. Při vyhledávání videí Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch), který obsahuje další poznámky a funkce vyhledávání videí Bingu.

## <a name="prerequisites"></a>Požadavky

* [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* [Knihovna Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

Nainstalujte si závislosti sady SDK Vyhledávání videí Bingu pomocí systému Maven, Gradle nebo jiného systému správy závislostí. Soubor POM Mavenu vyžaduje následující deklaraci:

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initalize-a-project"></a>Vytvořit a inicializovat projekt


V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java a naimportujte následující knihovny.

    ```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
    ```

## <a name="create-a-search-client"></a>Vytvoření klienta vyhledávání

2. Implementace `VideoSearchAPIImpl` klienta, které vyžaduje váš koncový bod rozhraní API a instance `ServiceClientCredentials` třídy.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
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

## <a name="send-a-search-request-and-recieve-the-response"></a>Odeslat žádost o vyhledávání a přijetí odpovědi 

1. Vytvořit funkci s názvem `VideoSearch()` , která má váš klíč předplatného jako řetězec. Vytvořit instanci klienta hledání vytvořili dříve.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. V rámci `VideoSearch()`, pošlete žádost pro vyhledávání videí pomocí klienta, `SwiftKey` jako hledaný termín. Pokud rozhraní API pro vyhledávání videí vrátilo výsledek, získá první hodnota a tisk jeho id, název a adresu URL, společně s celkový počet videí vrátila. 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. Volejte metodu vyhledávání z hlavní metody.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace v jediné stránce](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je API pro vyhledávání videí Bingu?](../overview.md)
* [Ukázky kognitivních služeb sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)