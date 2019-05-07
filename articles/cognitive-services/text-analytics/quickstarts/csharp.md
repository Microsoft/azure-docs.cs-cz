---
title: 'Rychlý start: Pomocí C# k volání rozhraní Text Analytics API'
titleSuffix: Azure Cognitive Services
description: Získejte informace a vzorové kódy, které vám pomůžou rychle začít používat rozhraní API pro analýzu textu.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/29/2019
ms.author: assafi
ms.openlocfilehash: e7b07472623cc459c31906aeaa6ccfb4388b4b50
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146089"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Rychlý start: Pomocí C# zavolat Text Analytics služby Cognitive Services
<a name="HOLTop"></a>

V tomto rychlém startu můžete začít analýzou jazyce s využitím Text Analytics SDK pro C#. Zatímco [rozhraní Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) rozhraní REST API je kompatibilní s Většina programovacích jazyků, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Technickou dokumentaci pro tato rozhraní API najdete v [definicích rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Dále musíte mít [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) vygenerovaný během registrace.

> [!Tip]
>  [Koncové body HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) můžete volat přímo z kódu jazyka C#, ale sada SDK Microsoft.Azure.CognitiveServices.Language volání služby výrazně usnadňuje díky tomu, že se nemusíte starat o serializaci a deserializaci formátu JSON.
>
> Několik užitečných odkazů:
> - [Balíček NuGet sady SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Kód SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Vytvoření řešení sady Visual Studio a instalace sady SDK

1. Vytvořte nový projekt Konzolová aplikace (.NET Core) [sady Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Klikněte na řešení pravým tlačítkem a pak klikněte na **Spravovat balíčky NuGet pro řešení**.
1. Vyberte kartu **Procházet** a vyhledejte **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**.

## <a name="authenticate-your-credentials"></a>Ověření vašich pověření

1. Přidejte následující `using` příkazy do souboru třídy hlavního (`Program.cs` ve výchozím nastavení).

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. Vytvořte nový `ApiKeyServiceClientCredentials` třídy pro uložení přihlašovacích údajů a přidat je pro každý požadavek.

    ```csharp
    /// <summary>
    /// Allows authentication to the API using a basic apiKey mechanism
    /// </summary>
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string subscriptionKey;

        /// <summary>
        /// Creates a new instance of the ApiKeyServiceClientCredentails class
        /// </summary>
        /// <param name="subscriptionKey">The subscription key to authenticate and authorize as</param>
        public ApiKeyServiceClientCredentials(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
        }

        /// <summary>
        /// Add the Basic Authentication Header to each outgoing request
        /// </summary>
        /// <param name="request">The outgoing request</param>
        /// <param name="cancellationToken">A token to cancel the operation</param>
        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }

            request.Headers.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Aktualizace `Program` třídu a přidejte konstanty člena pro váš klíč předplatného pro analýzu textu a druhý pro koncový bod služby. Nezapomeňte použít správný oblast Azure pro vaše předplatné pro analýzu textu.

    ```csharp
    private const string SubscriptionKey = "enter-your-key-here";

    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://westus.api.cognitive.microsoft.com";
    ```
> [!Tip]
> Pro zabezpečené nasazení tajných kódů v produkční systémy doporučujeme použít [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)
>

## <a name="create-a-text-analytics-client"></a>Vytvoření klienta pro analýzu textu

V `Main` funkce projektu, zavolejte metodu ukázka byste chtěli vyvolat a předat `Endpoint` a `SubscriptionKey` parametry, které jste definovali.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(SubscriptionKey);
        var client = new TextAnalyticsClient(credentials)
        {
            //Replace 'westus' with the correct region for your Text Analytics subscription
            Endpoint = "https://westus.api.cognitive.microsoft.com"
        };

        // Change console encoding to display non-ASCII characters
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

Další části popisuje, jak volat jednotlivé funkce rozhraní API.

## <a name="sentiment-analysis"></a>Analýza mínění

1. Vytvořit novou funkci s názvem `SentimentAnalysisExample()` , která přijímá klienta vytvořili dříve.
2. Vytvořte nový `TextAnalyticsClient` objekt s `ApiKeyServiceClientCredentials` jako parametr a pak vygenerovat seznam `MultiLanguageInput` objekty obsahující dokumenty, které chcete analyzovat.

    ```csharp
    public static async Task SentimentAnalysisExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life."),
                new MultiLanguageInput("en", "2", "This was a waste of my time. The speaker put me to sleep."),
                new MultiLanguageInput("es", "3", "No tengo dinero ni nada que dar..."),
                new MultiLanguageInput("it", "4", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
            });
        //...
    }
    ```

3. Ve stejné funkci volat `client.SentimentAsync()` a získat výsledek. Potom iterování přes výsledky a tisknout ID každé dokumentu a skóre mínění. Skóre blíže 0 znamená negativní zabarvení, zatímco skóre blíže 1 označuje pozitivní mínění.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Výstup

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Detekce jazyka

1. Vytvořit novou funkci s názvem `DetectLanguageExample()` , která přijímá klienta vytvořili dříve.
2. Vytvořte nový `TextAnalyticsClient` objekt s `ApiKeyServiceClientCredentials` jako parametr a pak vygenerovat seznam `LanguageInput` objekty obsahující vaše dokumenty.

    ```csharp
    public static async Task DetectLanguageExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English."),
                        new LanguageInput(id: "2", text: "Este es un document escrito en Español."),
                        new LanguageInput(id: "3", text: "这是一个用中文写的文件")
                    });
        //...
    }
    ```

3. Ve stejné funkci volat `client.DetectLanguageAsync()` a získat výsledek. Pak iterování přes výsledky a tisknout ID každé dokumentu a první vrácené jazyk.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Výstup

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznávání entit

1. Vytvořit novou funkci s názvem `RecognizeEntitiesExample()` , která přijímá klienta vytvořili dříve.
2. Vytvořte nový `TextAnalyticsClient` objekt s `ApiKeyServiceClientCredentials` jako parametr a pak vygenerovat seznam `MultiLanguageBatchInput` objekty obsahující vaše dokumenty.

    ```csharp
    public static async Task RecognizeEntitiesExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
                new MultiLanguageInput("es", "2", "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.")
            });
        //...
    }
    ```

3. Ve stejné funkci volat `client.EntitiesAsync()` a získat výsledek. Potom iterování přes výsledky a tisknout ID každé dokumentu. Pro každou zjištěnou entitu, jeho wikipedia název, typ a vytisknout podtypů (pokud existuje) a také umístění původního textu.

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>Výstup

```console
Document ID: 1
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 0,      Length: 9,      Score: 1.000
                Name: Bill Gates,       Type: Person,   Sub-Type: N/A
                        Offset: 25,     Length: 10,     Score: 1.000
                Name: Paul Allen,       Type: Person,   Sub-Type: N/A
                        Offset: 40,     Length: 10,     Score: 0.999
                Name: April 4,  Type: Other,    Sub-Type: N/A
                        Offset: 54,     Length: 7,      Score: 0.800
                Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
                        Offset: 54,     Length: 13,     Score: 0.800
                Name: BASIC,    Type: Other,    Sub-Type: N/A
                        Offset: 89,     Length: 5,      Score: 0.800
                Name: Altair 8800,      Type: Other,    Sub-Type: N/A
                        Offset: 116,    Length: 11,     Score: 0.800
Document ID: 2
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 21,     Length: 9,      Score: 1.000
                Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
                        Offset: 60,     Length: 7,      Score: 0.991
                Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
                        Offset: 71,     Length: 13,     Score: 0.800
                Name: Seattle,  Type: Location, Sub-Type: N/A
                        Offset: 88,     Length: 7,      Score: 1.000
```

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

1. Vytvořit novou funkci s názvem `KeyPhraseExtractionExample()` , která přijímá klienta vytvořili dříve.
2. Vytvořte nový `TextAnalyticsClient` objekt s `ApiKeyServiceClientCredentials` jako parametr a pak vygenerovat seznam `MultiLanguageBatchInput` objekty obsahující vaše dokumenty.

    ```csharp
    public static async Task KeyPhraseExtractionExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("ja", "1", "猫は幸せ"),
                        new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                        new MultiLanguageInput("en", "3", "My cat might need to see a veterinarian."),
                        new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                    });
        //...
    }
    ```

3. Ve stejné funkci volat `client.KeyPhrasesAsync()` a získat výsledek. Pak iterování přes výsledky a tisknout ID každé dokumentu a všechny zjištěné klíčové fráze.

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>Výstup

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Nejčastější dotazy](../text-analytics-resource-faq.md)

