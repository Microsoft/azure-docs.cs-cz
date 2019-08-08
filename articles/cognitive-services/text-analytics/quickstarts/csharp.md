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
ms.date: 07/30/2019
ms.author: assafi
ms.openlocfilehash: d12f6b400b270c6ef631d9f503980efef1ae8458
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840380"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Rychlý start: Použití sady .NET SDK a C# volání služby analýza textu
<a name="HOLTop"></a>

Tento rychlý Start vám pomůže začít používat sadu Azure SDK pro .NET C# a analyzovat jazyk. I když je REST API [Analýza textu](//go.microsoft.com/fwlink/?LinkID=759711) kompatibilní s většinou programovacích jazyků, poskytuje sada SDK snadný způsob, jak integrovat službu do vašich aplikací.

> [!NOTE]
> Ukázkový kód v tomto článku používá pro jednoduchost synchronní metody Analýza textu .NET SDK. U produkčních scénářů ale doporučujeme používat ve svých vlastních aplikacích dávkové asynchronní metody, které zajistí jejich škálovatelnost a reakce. Například můžete použít `SentimentBatchAsync` `Sentiment`místo.
>
> Dávkovaná asynchronní verze tohoto příkladu se dá najít na GitHubu [](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Technické informace najdete v tématu Referenční informace k sadě SDK pro .NET [Analýza textu](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet).

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady Visual Studio 2017 nebo novější
* [Sada SDK analýza textu pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

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
    // Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    // You can get the resource location from Azure Portal -> your TA resource -> Overview
    // There are two acceptable formats for the endpoint, both
    // require that you omit the `/text/analytics/<version>` suffix:
    // 1. A location based endpoint URL -
    //     "https://<your-location>.api.cognitive.microsoft.com";
    // 2. A resource name based endpoint URL -
    //     "https://<your-resource-name>.cognitiveservices.azure.com";
    private const string Endpoint = "enter-your-base-resource-endpoint-here";
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
        SentimentAnalysisExample(client);
        // DetectLanguageExample(client);
        // RecognizeEntitiesExample(client);
        // KeyPhraseExtractionExample(client);
        Console.ReadLine();
    }
```

Následující části popisují způsob volání jednotlivých funkcí služby.

## <a name="perform-sentiment-analysis"></a>Provést analýzu mínění

1. Vytvořte novou funkci `SentimentAnalysisExample()` , která převezme klienta, kterého jste vytvořili dříve.
2. Ve stejné funkci zavolejte `client.Sentiment()` a získejte výsledek. Výsledek bude obsahovat mínění `Score` v případě úspěchu, `errorMessage` a pokud ne. Skóre, které je blízko 0, označuje negativní mínění, zatímco skóre, které je blíže 1, značí kladné mínění.

    ```csharp
    var result = client.Sentiment("I had the best day of my life.", "en");

    // Printing sentiment results
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
    ```

### <a name="output"></a>Výstup

```console
Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Provést detekci jazyka

1. Vytvořte novou funkci `DetectLanguageExample()` , která převezme klienta, kterého jste vytvořili dříve.
2. Ve stejné funkci zavolejte `client.DetectLanguage()` a získejte výsledek. Výsledek bude obsahovat seznam zjištěných jazyků v případě úspěchu `DetectedLanguages` `errorMessage` a v případě potřeby. Potom vytiskněte první vrácený jazyk.

    ```csharp
    var result = client.DetectLanguage("This is a document written in English.");

    // Printing detected languages
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
    ```

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí `countryHint` parametru můžete zadat kód země se dvěma písmeny. Rozhraní API ve výchozím nastavení používá "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""` .

### <a name="output"></a>Výstup

```console
Language: English
```

## <a name="perform-entity-recognition"></a>Provést rozpoznávání entit

1. Vytvořte novou funkci `RecognizeEntitiesExample()` , která převezme klienta, kterého jste vytvořili dříve.
2. Ve stejné funkci zavolejte `client.Entities()` a získejte výsledek. Pak projdete výsledky. Výsledek bude obsahovat seznam zjištěných entit v případě úspěchu `Entities` `errorMessage` a v případě potřeby. Pro každou zjištěnou entitu vytiskněte svůj typ, dílčí typ, Wikipedii název (pokud existují) a také umístění v původním textu.

    ```csharp
    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");

    // Printing recognized entities
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
    ```

### <a name="output"></a>Výstup

```console
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
2. Ve stejné funkci zavolejte `client.KeyPhrases()` a získejte výsledek. Výsledek bude obsahovat seznam zjištěných klíčových frází v případě `KeyPhrases` úspěchu `errorMessage` a v případě potřeby. Potom vytiskněte všechny zjištěné klíčové fráze.

    ```csharp
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
    ```

### <a name="output"></a>Výstup

```console
Key phrases:
    cat
    veterinarian
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Nejčastější dotazy](../text-analytics-resource-faq.md)
