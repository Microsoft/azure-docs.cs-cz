---
title: 'Úvodní příručka: Kontrola pravopisu pomocí sady SDK kontroly pravopisu Bingu pro C #'
titleSuffix: Azure Cognitive Services
description: Můžete začít používat rozhraní REST API kontroly pravopisu Bingu ke kontrole pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 1cda7032d5bfe58e9f8bcbdb8b18dd597a691441
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78273535"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Úvodní příručka: Kontrola pravopisu pomocí sady SDK kontroly pravopisu Bingu pro C #

Pomocí tohoto rychlého startu můžete zahájit kontrolu pravopisu pomocí sady SDK kontroly pravopisu Bingu pro c#. Zatímco kontrola pravopisu Bing má rozhraní REST API kompatibilní s většinou programovacích jazyků, sada SDK poskytuje snadný způsob integrace služby do vašich aplikací. Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Závislosti aplikace

* Libovolná edice [Visual Studia 2017 nebo novější](https://visualstudio.microsoft.com/downloads/).
* [Balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) pro kontrolu pravopisu Bing

Chcete-li přidat sada SDK kontroly pravopisu Bingu do projektu, vyberte **spravovat balíčky NuGet** z **Průzkumníka řešení** v sadě Visual Studio. Přidejte balíček `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. Balíček také nainstaluje následující závislosti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nové řešení konzoly C# v sadě Visual Studio. Pak přidejte `using` následující příkaz.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Vytvořte novou třídu. Potom vytvořte asynchronní `SpellCheckCorrection()` funkci s názvem, která přebírá klíč předplatného a odešle žádost o kontrolu pravopisu.

3. Vytvořte vytvoření nového `ApiKeyServiceClientCredentials` objektu klienta. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Odeslat žádost a přečíst odpověď

1. Ve výše vytvořené funkci proveďte následující kroky. Odešlete žádost o kontrolu pravopisu s klientem. Přidejte do `text` parametru text, který chcete zkontrolovat, a nastavte režim na `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Získejte první výsledek kontroly pravopisu, pokud existuje. Vytisknout první chybně napsané slovo (token) vrácené, typ tokenu a počet návrhů.

    ```csharp
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Získejte první navrhovanou opravu, pokud existuje. Vytiskněte skóre návrhu a navrhované slovo. 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>Spuštění aplikace

Sestavení a spuštění projektu. Pokud používáte Visual Studio, ladíte soubor stisknutím **klávesy F5.**

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorials/spellcheck.md)

- [Co je rozhraní API pro kontrolu pravopisu Bingu?](overview.md)
- [Referenční příručka sady #Kontrola pravopisu Bingu C# SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)
