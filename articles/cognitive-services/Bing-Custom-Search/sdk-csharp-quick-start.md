---
title: 'Rychlý start: Volání pomocí koncových bodů vlastního vyhledávání Bingu C# SDK | Dokumentace Microsoftu'
titleSuffix: Azure Cognitive Services
description: Nastavení konzolové aplikace sady SDK pro vizuální vyhledávání v jazyce C#.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: f5ede8d8dc2950551655e7e0331a68b15ba13cf8
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555774"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Rychlý start: Volání pomocí koncových bodů vlastního vyhledávání Bingu C# SDK 

V tomto rychlém startu můžete začít si vyžádat výsledky hledání z vaší instance vlastního vyhledávání Bingu pomocí C# SDK. Zatímco vlastní vyhledávání Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, SDK vlastní vyhledávání Bingu poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

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

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte novou aplikaci konzoly C# v sadě Visual Studio. Pak přidejte do projektu následující balíčky.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. V hlavní metodě aplikace vytvořit instanci klienta vyhledávání s svůj klíč rozhraní API.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Poslat žádost o vyhledávání a přijetí odpovědi
    
1. Odeslat dotaz vyhledávání pomocí vašeho klienta `SearchAsync()` metoda a uložit odpověď. Nezapomeňte nahradit vaše `YOUR-CUSTOM-CONFIG-ID` s ID konfigurace vaší instance (najdete ID v [vlastní vyhledávání Bingu portál](https://www.customsearch.ai/)). Tento příklad vyhledá "Xbox".

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. Metoda `SearchAsync()` vrátí objekt `WebData`. Pomocí objektu k iteraci v rámci žádné `WebPages` , které nebyly nalezeny. Tento kód vyhledá první výsledek hledání webových stránek a vypíše název (`Name`) a adresu URL (`URL`) webové stránky.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)