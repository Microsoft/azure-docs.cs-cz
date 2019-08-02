---
title: 'Rychlý start: Hledat entity s Vyhledávání entit Bingu SDK proC#'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete vyhledat entity pomocí sady Vyhledávání entit Bingu SDK C#pro.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: aahi
ms.openlocfilehash: 20f76c0adfcbd756c71769979214ea975cb5d6d9
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360595"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>Odeslat žádost o vyhledávání pomocí sady Vyhledávání entit Bingu SDK proC#

V tomto rychlém startu můžete začít vyhledávat entity pomocí sady Vyhledávání entit Bingu SDK C#pro. I když Vyhledávání entit Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na GitHubu. [](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch)


## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/).
* Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet.
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).
* [Balíček NuGet sady vyhledávání zpráv Bingu SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Instalace tohoto balíčku také nainstaluje následující:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Chcete-li přidat sadu vyhledávání entit Bingu SDK do projektu aplikace Visual Studio, použijte možnost **Spravovat balíčky NuGet** z **Průzkumník řešení** `Microsoft.Azure.CognitiveServices.Search.EntitySearch` a přidejte balíček.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nové C# řešení konzoly v aplikaci Visual Studio. Pak přidejte následující do hlavního souboru kódu.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Vytvoření klienta a odeslání žádosti o vyhledávání

1. Vytvořit nového klienta vyhledávání. Přidejte svůj klíč předplatného tak, `ApiKeyServiceClientCredentials`že vytvoříte nový.

    ```csharp
    var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. K vyhledání dotazu použijte `Entities.Search()` funkci klienta:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Získání a tisk popisu entity

1. Pokud rozhraní API vrátilo výsledky hledání, Získejte hlavní entitu `entityData`z.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Tisk popisu hlavní entity 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorial-bing-entities-search-single-page-app.md)

* [Co je rozhraní API Bingu pro vyhledávání entit?](../overview.md )