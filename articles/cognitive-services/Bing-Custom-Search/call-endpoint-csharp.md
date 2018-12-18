---
title: 'Rychlý start: Volání pomocí koncových bodů vlastního vyhledávání Bingu C# | Dokumentace Microsoftu'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete začít si vyžádat výsledky hledání od vaší instance vlastního vyhledávání Bingu ve C#.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: maheshb
ms.openlocfilehash: 764a6a060a3ce2c7af9e4051b02ad45d14d27900
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557814"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Rychlý start: Volání pomocí koncových bodů vlastního vyhledávání BinguC# 

V tomto rychlém startu můžete začít si vyžádat výsledky hledání od vaší instance vlastního vyhledávání Bingu. Zatímco tato aplikace je napsána v C#, rozhraní API pro vlastní vyhledávání Bingu je kompatibilní s Většina programovacích jazyků rozhraní RESTful webová služba. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Požadavky

- Instanci vlastního vyhledávání Bingu. Zobrazit [rychlý start: Vytvoření první instanci vlastního vyhledávání Bingu](quick-start.md) Další informace.
- Microsoft [.Net Core](https://www.microsoft.com/net/download/core)
- Libovolná edice [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](http://www.mono-project.com/).
- Nainstalovaný balíček [NuGet pro vlastní vyhledávání](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). 
    - V Průzkumníku řešení v sadě Visual Studio klikněte pravým tlačítkem na projekt a v nabídce vyberte `Manage NuGet Packages`. Nainstalujte balíček `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Při instalaci balíčku NuGet pro vlastní vyhledávání se nainstalují také následující sestavení:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte novou aplikaci konzoly C# v sadě Visual Studio. Pak přidejte do projektu následující balíčky.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Vytvořte následující třídy úložiště search výsledky vrácené rozhraní API pro vlastní vyhledávání Bingu.

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

3. V hlavní metodě váš projekt Vytvořte proměnné pro váš klíč rozhraní API pro vyhledávání Bingu vlastní předplatného, ID search instance vlastní konfigurace a hledaný termín.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Vytvořit žádost o adresu URL připojením hledaný termín `q=` parametr dotazu a search instance vlastní ID konfigurace `customconfig=`. oddělení parametrů s `&` znak. 

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslat a přijmout žádost o vyhledávání 

1. Vytvořit požadavek klienta a přidat váš klíč předplatného na `Ocp-Apim-Subscription-Key` záhlaví.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Provedení požadavku hledání a získejte odpověď jako objekt JSON.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>Zpracování a zobrazení výsledků

1. Iterujte přes objekt odpovědi k zobrazení informací o jednotlivých výsledek hledání, včetně názvu, adresu url a datum, byl naposledy procházen webovou stránku.

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s vlastní vyhledávání](./tutorials/custom-search-web-page.md)