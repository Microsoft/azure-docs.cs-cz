---
title: 'Rychlý start: Pomocí sady SDK kontrola pravopisu Bingu pro kontrolu pravopisuC#'
titlesuffix: Azure Cognitive Services
description: Začněte používat rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: v-gedod
ms.openlocfilehash: 3487656f72f315aa15ce003a8dfd279c45d1f992
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766019"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Rychlý start: Pomocí sady SDK kontrola pravopisu Bingu pro kontrolu pravopisuC#

V tomto rychlém startu můžete začít pomocí sady SDK kontrola pravopisu Bingu pro kontrolu pravopisu C#. Zatímco kontrolu pravopisu Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Závislosti aplikace

* Libovolná edice sady [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
* Kontrola pravopisu Bingu [balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck)

Chcete-li do svého projektu přidat SDK kontrola pravopisu Bingu, klikněte na tlačítko `Manage NuGet Packages` z Průzkumníku řešení v sadě Visual Studio. Přidejte balíček `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. Tento balíček nainstaluje taky následující závislosti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový C# konzole řešení v sadě Visual Studio. Pak přidejte následující `using` příkazu.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Vytvořte novou třídu. Potom vytvořte asynchronní funkci volat `SpellCheckCorrection()` , který má klíč předplatného a odešle žádost o kontrolu pravopisu.

3. Vytvořit instanci klienta tak, že vytvoříte nový `ApiKeyServiceClientCredentials` objektu. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Odeslat požadavek a čtení odpovědi

1. Ve funkci vytvořené výše proveďte následující kroky. Odesíláte požadavek kontroly pravopisu s klientem. Přidejte text, který má být zkontroluje, aby se `text` parametr a nastavte režim na `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Získáte první výsledek kontroly pravopisu, pokud existuje. Tisk vrácen první chybně napsaná slova (token), typ tokenu a počet návrhů.

    ```csharp
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Získáte první navržená oprava, pokud existuje. Tisk skóre návrh a navrhované aplikace word. 

    ```csharp
            var suggestions = firstspellCheckResult.Suggestions;

            if (suggestions?.Count > 0)
            {
                var firstSuggestion = suggestions.FirstOrDefault();
                Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
            }
   }

## Next steps

> [!div class="nextstepaction"]
> [Create a single page web-app](tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK reference guide](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)