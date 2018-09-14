---
title: 'Rychlý start: Odeslání vyhledávací dotazy pomocí rozhraní API Bingu pro vyhledávání obrázků a'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijte k vyhledání a Image můžete vyhledat na webu pomocí rozhraní API webové vyhledávání Bingu.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/07/2018
ms.author: aahi
ms.openlocfilehash: 22eb54f6adec0335dd89a5ae906117542bb11717
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580408"
---
# <a name="quickstart-send-search-queries-using-the-bing-image-search-api-and-c"></a>Rychlý start: Odeslání vyhledávací dotazy s využitím rozhraní API pro vyhledávání obrázků Bingu a C#

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání obrázků a zobrazení výsledků na vyhledávacím z odpovědi JSON. Tato jednoduchá aplikace C# odešle dotaz vyhledávání image HTTP rozhraní API a zobrazí adresa URL první obrázku vrátil.

Zatímco tato aplikace je napsána v jazyce C#, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

Zdrojový kód pro tuto ukázku je k dispozici [na Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingImageSearchv7Quickstart.cs) zpracování dalších chyb a poznámky kódu.

## <a name="prerequisites"></a>Požadavky

* Libovolná edice [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* [Json.NET](https://www.newtonsoft.com/json) rozhraní .NET framework, k dispozici jako balíček NuGet. 
* Pokud používáte Linux nebo MacOS, bude možné spustit tuto aplikaci pomocí [Mono](http://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytváření a inicializace projektu

1. Vytvoření nového řešení konzoly s názvem `BingSearchApisQuickStart` v sadě Visual Studio. Pak přidejte následující obory názvů do souboru hlavní kód.

    ```csharp
    using System;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    using Newtonsoft.Json.Linq;
    ```

2. Vytváření proměnných pro koncový bod rozhraní API, váš klíč předplatného a hledané výrazy.

    ```csharp
    //...
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        // Replace the this string with your valid access key.
        const string subscriptionKey = "enter your key here";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";
        const string searchTerm = "tropical ocean";
    //...
    ```

## <a name="create-a-struct-to-format-the-bing-image-search-response"></a>Vytvoření struktury formátovat odpověď pro vyhledávání obrázků Bingu

Definování `SearchResult` struktury tak, aby obsahovala výsledky hledání obrázků a informace v hlavičce JSON.
    
```csharp
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        //...
            struct SearchResult
            {
                public String jsonResult;
                public Dictionary<String, String> relevantHeaders;
            }
//...
```

## <a name="create-a-method-to-send-search-requests"></a>Vytvořte metodu k odesílání žádostí o hledání

Vytvořit metodu s názvem `BingImageSearch` provádět volání rozhraní API a nastavit návratový typ na `SearchResult` struktury vytvořili dříve.

```csharp
//...
namespace BingSearchApisQuickstart
{
    //...
    class Program
    {
        //...
        static SearchResult BingImageSearch(string searchTerm)
        {
        }
//...
```

## <a name="create-and-handle-an-image-search-request"></a>Vytvořit a zpracovat požadavek hledání obrázků
 
V `BingImageSearch` metoda, proveďte následující kroky.

1. Vytvořte identifikátor URI pro požadavek hledání. Všimněte si, že hledaný termín `toSearch` musí být ve formátu, než se připojí na řetězec. 

    ```csharp
    static SearchResult BingImageSearch(string toSearch){

        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. Provést webový požadavek a získání odpovědi jako řetězec formátu JSON.

    ```csharp
    WebRequest request = WebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = subscriptionKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    ```

3. Vytvoření objektu výsledků hledání a rozbalte záhlaví HTTP Bingu. Pak se vraťte `searchResult`.

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

## <a name="process-and-view-the-response"></a>Proces a zobrazení odpovědi

1. V hlavní metodě volání `BingImageSearch()` a ukládat vrácená odpověď. Potom deserializují ve formátu JSON na objekt.

    ```csharp
    SearchResult result = BingImageSearch(searchTerm);
    //deserialize the JSON response from the Bing Image Search API
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result.jsonResult);
    ```

2. Načíst první vrácené image z `jsonObj`a vytiskne název a adresa URL obrázku. 
    ```csharp
    var firstJsonObj = jsonObj["value"][0];
    Console.WriteLine("Title for the first image result: " + firstJsonObj["name"]+"\n");
    //After running the application, copy the output URL into a browser to see the image. 
    Console.WriteLine("URL for the first image result: " + firstJsonObj["webSearchUrl"]+"\n");
    ```  
       
3. Ujistěte se, že se váš klíč předplatného odebrat z kódu vaší aplikace.

## <a name="json-response"></a>Odpověď JSON

Odpovědi z rozhraní API Bingu pro vyhledávání obrázků se vrátí jako JSON. Tato ukázková odpověď byl zkrácen na zobrazit jeden výsledek.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz jednostránkovou aplikaci pro vyhledávání obrázků Bingu](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je pro vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Zkuste online interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání bezplatné přístupového klíče služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentace ke službě Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční dokumentace rozhraní API pro vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)