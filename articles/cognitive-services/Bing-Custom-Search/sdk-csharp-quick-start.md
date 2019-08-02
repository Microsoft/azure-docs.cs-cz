---
title: 'Rychlý start: Volání koncového bodu Vlastní vyhledávání Bingu pomocí C# sady SDK | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu pomocí C# sady SDK.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: scottwhi
ms.openlocfilehash: c7ac6d051c8333a6329a3c2ed238d78fb9da4a30
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565722"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Rychlý start: Volání koncového bodu Vlastní vyhledávání Bingu pomocí C# sady SDK 

Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu pomocí C# sady SDK. I když Vlastní vyhledávání Bingu má REST API kompatibilní s většinou programovacích jazyků, Vlastní vyhledávání Bingu SDK poskytuje snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na GitHubu. [](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch)

## <a name="prerequisites"></a>Požadavky

- Instance Vlastní vyhledávání Bingu. Další [informace najdete v tématu rychlý Start: Pro další informace vytvořte první](quick-start.md) instanci vlastní vyhledávání Bingu.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Libovolná edice sady [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/)
- Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).
- Balíček NuGet [Vlastní vyhledávání Bingu](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) . 
    - Z **Průzkumník řešení** v aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt a v nabídce vyberte možnost **Spravovat balíčky NuGet** . Nainstalujte balíček `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Při instalaci balíčku NuGet pro vlastní vyhledávání se nainstalují také následující sestavení:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte novou C# konzolovou aplikaci v aplikaci Visual Studio. Pak přidejte do projektu následující balíčky.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. V metodě Main aplikace vytvořte instanci vyhledávacího klienta s klíčem rozhraní API.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Odeslat žádost o vyhledávání a přijmout odpověď
    
1. Odešlete vyhledávací dotaz pomocí `SearchAsync()` metody klienta a uložte odpověď. Nezapomeňte nahradit `YOUR-CUSTOM-CONFIG-ID` ID konfigurace vaší instance (ID můžete najít na [portálu vlastní vyhledávání Bingu](https://www.customsearch.ai/)). Tento příklad vyhledá "Xbox".

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. Metoda `SearchAsync()` vrátí objekt `WebData`. Použijte objekt k iterování všech `WebPages` nalezených. Tento kód vyhledá první výsledek hledání webových stránek a vypíše název (`Name`) a adresu URL (`URL`) webové stránky.

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
