---
title: Rychlý start klientské knihovny Bing Search C# pro vyhledávání videí
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: d50e1acd104916d68f7fbb84ff568cf4efc0b46b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289753"
---
Pomocí tohoto rychlého startu můžete začít hledat novinky v klientské knihovně Bing video search pro c#. Zatímco hledání videa Bing má rozhraní REST API kompatibilní s většinou programovacích jazyků, klientská knihovna poskytuje snadný způsob integrace služby do vašich aplikací. Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) s dalšími anotacemi a funkcemi.

## <a name="prerequisites"></a>Požadavky

* Libovolná edice [Visual Studia 2017 nebo novější](https://visualstudio.microsoft.com/downloads/).
* Json.NET framework, k dispozici [jako balíček NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Chcete-li přidat knihovnu klienta Bing Video Search do projektu, vyberte **Spravovat balíčky NuGet** z **Průzkumníka řešení** v sadě Visual Studio. Přidejte balíček `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

Instalace [balíčku [NuGet Video Search SDK]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) také nainstaluje následující závislosti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nové řešení konzoly C# v sadě Visual Studio. Pak přidejte následující do hlavního souboru kódu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Vytvořte instanci klienta `ApiKeyServiceClientCredentials` vytvořením nového objektu pomocí klíče předplatného a voláním konstruktoru.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Odeslání požadavku na hledání a zpracování výsledků

1. Pomocí klienta odešlete požadavek na vyhledávání. Pro vyhledávací dotaz použijte "SwiftKey".

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Pokud byly vráceny nějaké výsledky, `videoResults.Value[0]`získejte první s . Pak vytiskněte ID, název a adresu URL videa.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace pro jednu stránku](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Viz také 

* [Co je rozhraní API Bingu pro vyhledávání videí?](../../overview.md)
* [Ukázky sady Cognitive Services .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
