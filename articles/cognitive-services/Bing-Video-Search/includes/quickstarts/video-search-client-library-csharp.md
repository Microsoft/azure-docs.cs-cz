---
title: Rychlý Start klientské knihovny Vvyhledávání videí Bingu C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d50a8e2c9d0263616b25e25958be6a6f0fb7fe1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88929261"
---
V tomto rychlém startu můžete začít vyhledávat zprávy pomocí Vvyhledávání videí Bingu klientské knihovny pro C#. I když Vvyhledávání videí Bingu má REST API kompatibilní s většinou programovacích jazyků, Klientská knihovna poskytuje snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) s dalšími poznámkami a funkcemi.

## <a name="prerequisites"></a>Předpoklady

* Libovolná edice sady [Visual Studio 2017 nebo novější](https://visualstudio.microsoft.com/downloads/).
* Rozhraní Json.NET, které je k dispozici [jako balíček NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Chcete-li přidat knihovnu klienta Vvyhledávání videí Bingu do projektu, vyberte možnost **Spravovat balíčky NuGet** z **Průzkumník řešení** v aplikaci Visual Studio. Přidejte balíček `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

Instalace [balíčku [NuGet vyhledávání videí SDK]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) nainstaluje také následující závislosti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nové řešení konzoly C# v aplikaci Visual Studio. Pak přidejte následující do hlavního souboru kódu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Vytvořte instanci klienta vytvořením nového `ApiKeyServiceClientCredentials` objektu pomocí klíče předplatného a voláním konstruktoru.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Odeslání žádosti o vyhledávání a zpracování výsledků

1. Odešlete požadavek hledání pomocí klienta. Pro vyhledávací dotaz použijte "SwiftKey".

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Pokud byly vráceny nějaké výsledky, získejte první z nich `videoResults.Value[0]` . Potom vytiskněte ID, název a adresu URL videa.

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
> [Vytvoření webové aplikace s jednou stránkou](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Viz také 

* [Co je rozhraní API Bingu pro vyhledávání videí?](../../overview.md)
* [Ukázky sady .NET SDK pro rozpoznávání služeb](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
