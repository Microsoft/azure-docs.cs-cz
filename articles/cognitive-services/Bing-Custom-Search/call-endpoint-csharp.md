---
title: 'Rychlý Start: volání koncového bodu C# vlastní vyhledávání Bingu pomocí | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu C#v.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: maheshb
ms.openlocfilehash: 88a8cc0dbedeb9398637ab9a02323e9b2a6b8627
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975257"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Rychlý Start: volání koncového bodu Vlastní vyhledávání Bingu pomocíC# 

Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu. I když je tato aplikace napsaná v C#, rozhraní API pro vlastní vyhledávání Bingu je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs)

## <a name="prerequisites"></a>Předpoklady

- Instance Vlastní vyhledávání Bingu. Další informace najdete v tématu [rychlý Start: Vytvoření první instance vlastní vyhledávání Bingu](quick-start.md) .
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Libovolná edice sady [Visual Studio 2019 nebo novější](https://www.visualstudio.com/downloads/)
- Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).
- Balíček NuGet [Vlastní vyhledávání Bingu](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) . 
    - Z **Průzkumník řešení** v aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt a v nabídce vyberte možnost **Spravovat balíčky NuGet** . Nainstalujte balíček `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Při instalaci balíčku NuGet pro vlastní vyhledávání se nainstalují také následující sestavení:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte novou C# konzolovou aplikaci v aplikaci Visual Studio. Pak přidejte do projektu následující balíčky.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Vytvořte následující třídy pro uložení výsledků hledání vrácených rozhraní API pro vlastní vyhledávání Bingu.

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. V hlavní metodě projektu vytvořte proměnné pro klíč předplatného rozhraní API pro vlastní vyhledávání Bingu, ID vlastní konfigurace vaší instance hledání a hledaný termín.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Vytvořte adresu URL požadavku připojením hledaného termínu k parametru dotazu `q=` a ID vlastní konfigurace vaší instance hledání `customconfig=`. parametry oddělte znakem `&`. `url` může být globální koncový bod nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslání a přijetí žádosti o vyhledávání 

1. Vytvořte klienta žádosti a přidejte svůj klíč předplatného do hlavičky `Ocp-Apim-Subscription-Key`.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Proveďte požadavek Search a získejte odpověď jako objekt JSON.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
   ## <a name="process-and-view-the-results"></a>Zpracování a zobrazení výsledků

3. Iterujte přes objekt odpovědi, aby se zobrazily informace o každém výsledku hledání, včetně jeho názvu, adresy URL a data posledního procházení webové stránky.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření vlastní vyhledávací webové aplikace](./tutorials/custom-search-web-page.md)
