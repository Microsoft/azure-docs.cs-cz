---
title: 'Rychlý Start: Kontrola pravopisu pomocí sady Kontrola pravopisu Bingu SDK pro jazyk C #'
titleSuffix: Azure Cognitive Services
description: Začněte používat REST API Kontrola pravopisu Bingu pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: e1e05ca9b63bda3373afb8a090118953d89ad8f9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88934221"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Rychlý Start: Kontrola pravopisu pomocí sady Kontrola pravopisu Bingu SDK pro jazyk C #

Pomocí tohoto rychlého startu můžete spustit kontrolu pravopisu v sadě Kontrola pravopisu Bingu SDK pro jazyk C#. I když Kontrola pravopisu Bingu má REST API kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací. Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Závislosti aplikace

* Libovolná edice sady [Visual Studio 2017 nebo novější](https://visualstudio.microsoft.com/downloads/).
* [Balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) kontrola pravopisu Bingu

Chcete-li přidat sadu Kontrola pravopisu Bingu SDK do projektu, vyberte možnost **Spravovat balíčky NuGet** z **Průzkumník řešení** v sadě Visual Studio. Přidejte balíček `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. Balíček také nainstaluje následující závislosti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nové řešení konzoly C# v aplikaci Visual Studio. Pak přidejte následující `using` příkaz.
    
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

1. Ve funkci vytvořené výše proveďte následující kroky. Odešlete žádost o kontrolu pravopisu u klienta. Přidejte text, který se má zkontrolovat do `text` parametru, a nastavte režim na `proof` .  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Získejte první výsledek kontroly pravopisu, pokud je nějaký. Vytiskněte vrácené slovo (token), typ tokenu a počet návrhů.

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

3. Získejte první navrhovanou opravu, pokud je nějaká. Vytiskněte skóre návrhu a navrhované slovo. 

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

Sestavte a spusťte projekt. Pokud používáte Visual Studio, můžete soubor ladit stisknutím klávesy **F5** .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorials/spellcheck.md)

- [Co je rozhraní API pro kontrolu pravopisu Bingu?](overview.md)
- [Referenční příručka k sadě Kontrola pravopisu Bingu C# SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)
