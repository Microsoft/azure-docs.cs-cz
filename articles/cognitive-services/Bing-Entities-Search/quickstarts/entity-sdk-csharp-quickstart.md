---
title: 'Rychlý start: U entit s SDK vyhledávání entit Bingu pro vyhledáváníC#'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete u entit s SDK vyhledávání entit Bingu pro vyhledávání C#.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 6a365ef5421de3ceb31c5cc78a424f786f174ab3
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861914"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>Odeslat požadavek s SDK vyhledávání entit Bingu pro vyhledáváníC#

Můžete začít u entit s SDK vyhledávání entit Bingu pro vyhledávání v tomto rychlém startu C#. Při vyhledávání entit Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet.
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](http://www.mono-project.com/).
* [Balíček NuGet sady SDK pro vyhledávání zpráv Bingu](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Instaluje se tento balíček nainstaluje taky následující:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Chcete-li přidat do projektu sady Visual Studio SDK vyhledávání entit Bingu, použijte `Manage NuGet Packages` z Průzkumníka řešení a přidejte `Microsoft.Azure.CognitiveServices.Search.EntitySearch` balíčku.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Vytváření a inicializace aplikace

1. Vytvořte nový C# konzole řešení v sadě Visual Studio. Pak přidejte následující kód do souboru hlavní kód.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Vytvoření klienta a odeslat žádost o vyhledávání

2. Vytvořte nového klienta vyhledávání. Přidat klíč předplatného. tím, že vytvoříte nový `ApiKeyServiceClientCredentials`.

    ```csharp
    var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

3. Použití klienta `Entities.Search()` funkce vyhledávání pro váš dotaz:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Získání a vytisknout popis entity

1. Pokud rozhraní API vrátí výsledky hledání, získání hlavního entity z `entityData`.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Vytisknout popis hlavních entity 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorial-bing-entities-search-single-page-app.md)

* [Co je API pro vyhledávání entit Bingu?](../overview.md )