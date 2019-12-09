---
title: 'Rychlý Start: hledání imagí – Vyhledávání obrázků Bingu REST API aC#'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odesílat žádosti o vyhledávání obrázků do Vyhledávání obrázků Bingu REST API C#pomocí a přijímat odpovědi JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.openlocfilehash: e52101b873e03cd6fcd847e9aa2a2d801f8f583d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918847"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-c"></a>Rychlý Start: hledání imagí pomocí Vyhledávání obrázků Bingu REST API aC#

V tomto rychlém startu můžete začít odesílat žádosti o vyhledávání rozhraní API Bingu pro vyhledávání obrázků. Tato C# aplikace pošle vyhledávací dotaz do rozhraní API a zobrazí adresu URL prvního obrázku ve výsledcích. I když je tato aplikace napsaná v C#, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingImageSearchv7Quickstart.cs) s dalším zpracováním chyb a poznámkami.

## <a name="prerequisites"></a>Předpoklady
* Libovolná edice sady [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/).
* Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet.
* Pokud používáte Linux/MacOS, můžete tuto aplikaci spustit pomocí [mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nové konzolové řešení s názvem `BingSearchApisQuickStart` v sadě Visual Studio. Pak přidejte následující obory názvů do souboru hlavního kódu.

    ```csharp
    using System;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    using Newtonsoft.Json.Linq;
    ```

2. Vytvořte proměnné pro koncový bod rozhraní API, klíč předplatného a hledaný výraz. `uriBase` může být globální koncový bod nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

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

## <a name="create-a-struct-to-format-the-bing-image-search-response"></a>Vytvoření struktury pro formátování odpovědi vyhledávání obrázků Bingu

Definujte strukturu `SearchResult` tak, aby obsahovala výsledky hledání obrázků, a informace v hlavičce JSON.

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

## <a name="create-a-method-to-send-search-requests"></a>Vytvoření metody k odesílání žádostí o vyhledávání

Vytvořte metodu s názvem `BingImageSearch`, která provede volání rozhraní API a nastaví typ vrácení na strukturu `SearchResult` vytvořenou dříve.

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

## <a name="create-and-handle-an-image-search-request"></a>Vytvoření a zpracování žádostí o vyhledávání obrázků

V metodě `BingImageSearch` proveďte následující kroky.

1. Sestavte URI pro žádost o vyhledávání. Hledaný termín `SearchTerm` musí být před připojením k řetězci zformátován.

    ```csharp
    static SearchResult BingImageSearch(string SearchTerm){

        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(SearchTerm);
    //...
    ```

2. Odešlete webový požadavek a získejte odpověď jako řetězec JSON.

    ```csharp
    WebRequest request = WebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = subscriptionKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    ```

3. Vytvořte objekt výsledků hledání a extrahujte hlavičky HTTP Bingu. Pak vraťte `searchResult`.

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

## <a name="process-and-view-the-response"></a>Zpracování a zobrazení odpovědi

1. V hlavní metodě volejte `BingImageSearch()` a uložte vrácenou odpověď. Potom deserializujte JSON na objekt.

    ```csharp
    SearchResult result = BingImageSearch(searchTerm);
    //deserialize the JSON response from the Bing Image Search API
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result.jsonResult);
    ```

2. Získejte první vrácené obrázek z `jsonObj`a vytiskněte název a adresu URL obrázku.
    ```csharp
    var firstJsonObj = jsonObj["value"][0];
    Console.WriteLine("Title for the first image result: " + firstJsonObj["name"]+"\n");
    //After running the application, copy the output URL into a browser to see the image.
    Console.WriteLine("URL for the first image result: " + firstJsonObj["webSearchUrl"]+"\n");
    ```  


## <a name="example-json-response"></a>Příklad odpovědi JSON

Odpovědi od rozhraní API Bingu pro vyhledávání obrázků se vrátí jako JSON. Ukázková odpověď je zkrácená, aby zobrazovala jenom jeden výsledek.

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
    }]
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledávání obrázků Bingu – kurz jednostránkové aplikace](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Vyzkoušet online interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) pro rozhraní API pro vyhledávání Bingu. 
* [Získat zdarma přístupový klíč služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentace Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
