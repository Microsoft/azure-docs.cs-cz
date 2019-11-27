---
title: 'Rychlý Start: hledání videí pomocí sady SDK pro C# Vvyhledávání videí Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete odesílat žádosti o vyhledávání videí pomocí sady SDK C#Vvyhledávání videí Bingu pro.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: d29aef614b8308dfeba8da7925bd2880c25fe72d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383761"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Rychlý Start: provedení videa s Vvyhledávání videí Bingu SDK proC#

V tomto rychlém startu můžete začít hledat zprávy s Vvyhledávání videí Bingu SDK pro C#. I když Vvyhledávání videí Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) s dalšími poznámkami a funkcemi.

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017 nebo novější](https://visualstudio.microsoft.com/downloads/).
* Rozhraní Json.NET, které je k dispozici [jako balíček NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Chcete-li přidat sadu Vvyhledávání videí Bingu SDK do projektu, vyberte možnost **Spravovat balíčky NuGet** z **Průzkumník řešení** v sadě Visual Studio. Přidejte balíček `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

Instalace [balíčku [NuGet vyhledávání videí SDK]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) nainstaluje také následující závislosti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nové C# řešení konzoly v aplikaci Visual Studio. Pak přidejte následující do hlavního souboru kódu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Vytvořte instanci klienta vytvořením nového objektu `ApiKeyServiceClientCredentials` pomocí klíče předplatného a zavoláním konstruktoru.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Odeslání žádosti o vyhledávání a zpracování výsledků

1. Odešlete požadavek hledání pomocí klienta. Pro vyhledávací dotaz použijte "SwiftKey".

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Pokud byly vráceny nějaké výsledky, získejte první z nich s `videoResults.Value[0]`. Potom vytiskněte ID, název a adresu URL videa.

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
> [Vytvoření webové aplikace s jednou stránkou](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Viz také 

* [Co je rozhraní API Bingu pro vyhledávání videí?](../overview.md)
* [Ukázky kognitivních služeb sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
