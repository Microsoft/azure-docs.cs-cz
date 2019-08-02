---
title: 'Rychlý start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu SDK proC#'
titleSuffix: Azure Cognitive Services
description: Začněte používat REST API Kontrola pravopisu Bingu pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d98d00275cbd89702e4bae0c93aa262805617e59
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500786"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Rychlý start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu SDK proC#

Pomocí tohoto rychlého startu můžete spustit kontrolu pravopisu v sadě Kontrola pravopisu Bingu C#SDK pro. I když Kontrola pravopisu Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na GitHubu. [](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck)

## <a name="application-dependencies"></a>Závislosti aplikace

* Libovolná edice sady [Visual Studio 2017 nebo novější](https://visualstudio.microsoft.com/downloads/).
* [Balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) kontrola pravopisu Bingu

Chcete-li přidat sadu Kontrola pravopisu Bingu SDK do projektu, vyberte možnost **Spravovat balíčky NuGet** z **Průzkumník řešení** v sadě Visual Studio. Přidejte balíček `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. Balíček také nainstaluje následující závislosti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nové C# řešení konzoly v aplikaci Visual Studio. Pak přidejte následující `using` příkaz.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Vytvořte novou třídu. Pak vytvořte asynchronní funkci s názvem `SpellCheckCorrection()` , která převezme klíč předplatného, a odešle požadavek na kontrolu pravopisu.

3. Vytvořte instanci klienta vytvořením nového `ApiKeyServiceClientCredentials` objektu. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Odeslat požadavek a přečíst odpověď

1. Ve funkci vytvořené výše proveďte následující kroky. Odešlete žádost o kontrolu pravopisu u klienta. Přidejte text, který se má zkontrolovat do `text` parametru, a nastavte režim na. `proof`  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Získejte první výsledek kontroly pravopisu, pokud je nějaký. Vytiskněte vrácené slovo (token), typ tokenu a počet návrhů.

    ```csharp
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Získejte první navrhovanou opravu, pokud je nějaká. Vytiskněte skóre návrhu a navrhované slovo. 

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