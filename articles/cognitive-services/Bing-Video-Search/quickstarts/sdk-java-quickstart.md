---
title: 'Rychlý Start: hledání videí pomocí sady SDK pro jazyk Java Vvyhledávání videí Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odesílat žádosti o vyhledávání videí pomocí sady SDK Vvyhledávání videí Bingu SDK pro jazyk Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: cbe5f0023b16aec1ed68e7a78ddb3103f40eb85b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378598"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-java"></a>Rychlý Start: provedení videa s Vvyhledávání videí Bingu SDK pro jazyk Java

V tomto rychlém startu můžete začít hledat zprávy s Vvyhledávání videí Bingu SDK pro jazyk Java. I když Vvyhledávání videí Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch)s dalšími poznámkami a funkcemi.

## <a name="prerequisites"></a>Požadavky

* [Sada Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

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

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu


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

## <a name="create-a-search-client"></a>Vytvoření vyhledávacího klienta

1. Implementujte klienta `VideoSearchAPIImpl`, který vyžaduje koncový bod rozhraní API a instanci `ServiceClientCredentials` třídy.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
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

## <a name="send-a-search-request-and-receive-the-response"></a>Odeslat žádost o vyhledávání a přijmout odpověď 

1. Vytvořte funkci nazvanou `VideoSearch()`, která převezme klíč předplatného jako řetězec. Vytvořte instanci vyhledávacího klienta vytvořeného dříve.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. V rámci `VideoSearch()`odešlete žádost o vyhledávání videí pomocí klienta s `SwiftKey` jako hledaný termín. Pokud rozhraní API Vyhledávání videí vrátilo výsledek, získá první výsledek a vytiskne jeho ID, název a adresu URL spolu s celkovým počtem vrácených videí. 
    
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

3. Zavolejte metodu hledání z metody Main.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace s jednou stránkou](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Viz také 

* [Co je rozhraní API Bingu pro vyhledávání videí?](../overview.md)
* [Ukázky kognitivních služeb sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)