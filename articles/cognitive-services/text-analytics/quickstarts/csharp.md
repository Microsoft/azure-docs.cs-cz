---
title: 'Rychlý start: Volání služby Analýza textu pomocí sady Azure SDK pro .NET aC#'
titleSuffix: Azure Cognitive Services
description: Informace a ukázky kódu, které vám pomůžou začít používat službu Analýza textu C#a.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: assafi
ms.openlocfilehash: 09713528f51675f6e9d7f3073b6c81b095d23631
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356976"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Rychlý start: Použití sady .NET SDK a C# volání služby analýza textu
<a name="HOLTop"></a>

Tento rychlý Start vám pomůže začít používat sadu Azure SDK pro .NET C# a analyzovat jazyk. I když je REST API [Analýza textu](//go.microsoft.com/fwlink/?LinkID=759711) kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací.

> [!NOTE]
> Zdrojový kód k této ukázce je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Technické informace najdete v tématu Referenční informace k sadě SDK pro .NET [Analýza textu](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet).

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017 nebo novější]
* [Sada SDK analýza textu pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Také potřebujete [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) , který jste vygenerovali během registrace.

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Vytvoření řešení sady Visual Studio a instalace sady SDK

1. Vytvořte nový projekt Konzolová aplikace (.NET Core). [Přístup k Visual Studiu](https://visualstudio.microsoft.com/vs/).
1. Klikněte pravým tlačítkem na řešení a vyberte **Spravovat balíčky NuGet pro řešení**.
1. Vyberte kartu **Procházet**. Vyhledejte **Microsoft. Azure. cognitiveservices Account. Language. TextAnalytics**.

## <a name="authenticate-your-credentials"></a>Ověření přihlašovacích údajů

1. Do souboru hlavní `using` třídy přidejte následující příkazy (ve výchozím nastavení je to program.cs).

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

2. Vytvořte novou `ApiKeyServiceClientCredentials` třídu pro uložení přihlašovacích údajů a přidejte ji pro každý požadavek.

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Aktualizujte `Program` třídu. Přidejte konstantního člena pro svůj rozhraní API pro analýzu textu klíč a druhý pro koncový bod služby. Nezapomeňte použít pro prostředek Analýza textu správné umístění Azure.

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```
> [!Tip]
> Pro zvýšení zabezpečení tajných kódů v produkčních systémech doporučujeme použít [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Vytvoření klienta Analýza textu

`Main` Ve funkci projektu zavolejte ukázkovou metodu, kterou chcete vyvolat. Předejte parametry `ApiKey`a, které jste definovali. `Endpoint`

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

Následující části popisují způsob volání jednotlivých funkcí služby.

## <a name="perform-sentiment-analysis"></a>Provést analýzu mínění

1. Vytvořte novou funkci `SentimentAnalysisExample()` , která převezme klienta, kterého jste vytvořili dříve.
2. Vygenerujte seznam `MultiLanguageInput` objektů, které obsahují dokumenty, které chcete analyzovat.

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life.")
            });
        //...
    }
    ```

3. Ve stejné funkci zavolejte `client.SentimentAsync()` a získejte výsledek. Pak projdete výsledky. Vytiskněte ID každého dokumentu a mínění skóre. Skóre, které je blízko 0, označuje negativní mínění, zatímco skóre, které je blíže 1, značí kladné mínění.

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
```

## <a name="perform-language-detection"></a>Provést detekci jazyka

1. Vytvořte novou funkci `DetectLanguageExample()` , která převezme klienta, kterého jste vytvořili dříve.
2. Vygeneruje seznam `LanguageInput` objektů, které obsahují vaše dokumenty.

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English.")
                    });
        //...
    }
    ```

3. Ve stejné funkci zavolejte `client.DetectLanguageAsync()` a získejte výsledek. Pak projdete výsledky. Vytiskněte ID každého dokumentu a první vrácený jazyk.

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
```

## <a name="perform-entity-recognition"></a>Provést rozpoznávání entit

1. Vytvořte novou funkci `RecognizeEntitiesExample()` , která převezme klienta, kterého jste vytvořili dříve.
2. Vygeneruje seznam `MultiLanguageBatchInput` objektů, které obsahují vaše dokumenty.

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {
        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.")
            });
        //...
    }
    ```

3. Ve stejné funkci zavolejte `client.EntitiesAsync()` a získejte výsledek. Pak projdete výsledky. Vytiskněte ID každého dokumentu. Pro každou zjištěnou entitu vytiskněte název Wikipedii a typ a podtypy (pokud existují) a také umístění v původním textu.

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
```

## <a name="perform-key-phrase-extraction"></a>Provést extrakci klíčových frází

1. Vytvořte novou funkci `KeyPhraseExtractionExample()` , která převezme klienta, kterého jste vytvořili dříve.
2. Vygeneruje seznam `MultiLanguageBatchInput` objektů, které obsahují vaše dokumenty.

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "My cat might need to see a veterinarian.")
                    });
        //...
    }
    ```

3. Ve stejné funkci zavolejte `client.KeyPhrasesAsync()` a získejte výsledek. Pak projdete výsledky. Vytiskněte ID každého dokumentu a všechny zjištěné klíčové fráze.

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
                cat
                veterinarian
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Nejčastější dotazy](../text-analytics-resource-faq.md)
