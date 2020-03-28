---
title: 'Úvodní příručka: Volání vlastního koncového bodu služby Bing pomocí jazyka C# | Dokumenty společnosti Microsoft'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z instance vlastního vyhledávání Bingu v c#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 7ea8b272871e681bd9caacf8cf1a84eb91d8849d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238906"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Úvodní příručka: Zavolejte ke svému koncovému bodu služby Bing pomocí aplikace C # 

Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z instance vlastního vyhledávání Bingu. Zatímco tato aplikace je napsána v jazyce C#, rozhraní API pro vlastní vyhledávání Bingu je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Požadavky

- Instance vlastního vyhledávání Bingu. Další informace najdete [v tématu Úvodní příručka: Vytvoření první instance vlastního vyhledávání Bingu.](quick-start.md)
- [Jádro microsoftu .NET](https://www.microsoft.com/net/download/core)
- Libovolná edice [Visual Studia 2019 nebo novější](https://www.visualstudio.com/downloads/)
- Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).
- Balíček NuGet [pro vlastní vyhledávání Bingu.](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) 
    - V **Průzkumníku řešení** v sadě Visual Studio klikněte pravým tlačítkem myši na projekt a v nabídce vyberte **Spravovat balíčky NuGet.** Nainstalujte balíček `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Při instalaci balíčku NuGet pro vlastní vyhledávání se nainstalují také následující sestavení:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte novou konzolovou aplikaci C# v sadě Visual Studio. Pak přidejte do projektu následující balíčky.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Vytvořte následující třídy pro uložení výsledků hledání vrácených rozhraním API pro vlastní vyhledávání Bingu.

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

3. V hlavní metodě projektu vytvořte proměnné pro klíč předplatného rozhraní API pro vlastní vyhledávání Bingu, id vlastní konfigurace instance vyhledávání a hledaný výraz.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Vytvořte adresu URL požadavku připojením `q=` hledaného výrazu k parametru dotazu `customconfig=`a ID vlastní konfigurace instance hledání. oddělte parametry `&` znakem. `url`může být globální koncový bod níže nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslání a přijetí požadavku na vyhledávání 

1. Vytvořte klienta požadavku a přidejte `Ocp-Apim-Subscription-Key` klíč předplatného do záhlaví.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Proveďte požadavek hledání a získejte odpověď jako objekt JSON.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
   ## <a name="process-and-view-the-results"></a>Zpracování a zobrazení výsledků

3. Iterazovat přes objekt odpovědi zobrazit informace o každém výsledku hledání, včetně jeho název, url a datum, kdy byla naposledy procházena webová stránka.

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
> [Vytvoření webové aplikace pro vlastní vyhledávání](./tutorials/custom-search-web-page.md)
