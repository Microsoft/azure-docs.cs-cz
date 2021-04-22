---
title: 'Rychlý Start: volání koncového bodu Vlastní vyhledávání Bingu pomocí jazyka C# | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu v jazyce C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 1d3accdb20073bd1e9b29988b78d7eacd49976b8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862304"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Rychlý Start: volání koncového bodu Vlastní vyhledávání Bingu pomocí jazyka C # 

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

V tomto rychlém startu se dozvíte, jak vyžádat výsledky hledání z vaší instance Vlastní vyhledávání Bingu. I když je tato aplikace napsaná v jazyce C#, rozhraní API pro vlastní vyhledávání Bingu je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Požadavky

- Instance Vlastní vyhledávání Bingu. Další informace najdete v tématu [rychlý Start: Vytvoření první instance vlastní vyhledávání Bingu](quick-start.md).
- [Microsoft .NET Core](https://dotnet.microsoft.com/download).
- Libovolná edice sady [Visual Studio 2019 nebo novější](https://www.visualstudio.com/downloads/).
- Pokud používáte Linux/MacOS, můžete tuto aplikaci spustit pomocí [mono](https://www.mono-project.com/).
- Balíček NuGet [Vlastní vyhledávání Bingu](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) . 

   Instalace tohoto balíčku v aplikaci Visual Studio: 
     1. V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a pak vyberte **Spravovat balíčky NuGet**. 
     2. Vyhledejte a vyberte *Microsoft. Azure. cognitiveservices Account. Search. CustomSearch* a pak balíček nainstalujte.

   Při instalaci balíčku Vlastní vyhledávání Bingu NuGet nainstaluje Visual Studio také tyto balíčky:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft. REST. ClientRuntime. Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte novou konzolovou aplikaci v jazyce C# v aplikaci Visual Studio. Pak přidejte do projektu následující balíčky:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Vytvořte následující třídy pro uložení výsledků hledání vrácených rozhraní API pro vlastní vyhledávání Bingu:

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

3. V hlavní metodě projektu vytvořte následující proměnné pro klíč předplatného rozhraní API pro vlastní vyhledávání Bingu, ID vlastní konfigurace instance hledání a hledaný termín:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Vytvořte adresu URL žádosti připojením hledaného výrazu k `q=` parametru dotazu a ID vlastní konfigurace vaší instance hledání k `customconfig=` parametru. Parametry oddělte pomocí ampersandu ( `&` ). Pro `url` hodnotu proměnné můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslání a přijetí žádosti o vyhledávání 

1. Vytvořte klienta žádosti a přidejte do záhlaví klíč předplatného `Ocp-Apim-Subscription-Key` .

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

- Iterujte přes objekt odpovědi, aby se zobrazily informace o každém výsledku hledání, včetně jeho názvu, adresy URL a data posledního procházení webové stránky.

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