---
title: 'Úvodní příručka: Klientská knihovna Analýzy textových analýz pro c# | Dokumenty společnosti Microsoft'
description: 'Začínáme s klientskou knihovnou Text Analytics pro C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2fa2e40ba2a7fe84b6df57bfb711d01332b8f523
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275034"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

[v3 Referenční dokumentace](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 Zdrojový kód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | knihovny[v3 Balíček (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 Ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

[v2 Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 Zdrojový kód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | knihovny[v2 Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 Ukázky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* IDE [visual studia](https://visualstudio.microsoft.com/vs/)
* Jakmile budete mít předplatné <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure, vytvořte"  target="_blank">prostředek analýzy <span class="docon docon-navigate-external x-hidden-focus"></span> </a> textu vytvořit prostředek analýzy textu na webu Azure Portal, abyste získali klíč a koncový bod.  Po nasazení klikněte na **Přejít na prostředek**.
    * Budete potřebovat klíč a koncový bod z prostředku, který vytvoříte pro připojení aplikace k rozhraní API analýzy textu. Klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * Můžete použít bezplatnou cenovou úroveň (`F0`) vyzkoušet službu a upgradovat později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-net-core-application"></a>Vytvoření nové aplikace .NET Core

Pomocí rozhraní IDE sady Visual Studio vytvořte novou konzolovou aplikaci .NET Core. Tím se vytvoří projekt "Hello World" s jedním zdrojovým souborem C#: *program.cs*.

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Nainstalujte klientskou knihovnu kliknutím pravým tlačítkem myši na řešení v **Průzkumníku řešení** a výběrem **možnosti Spravovat balíčky NuGet**. Ve správci balíčků, který se otevře, vyberte `Azure.AI.TextAnalytics` **Procházet**, **zaškrtněte políčko Zahrnout předběžnou verzi**a vyhledejte . Vyberte `1.0.0-preview.4`verzi a **nainstalujte**program Install . Můžete také použít [konzolu Správce balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Chcete zobrazit celý soubor kódu rychlého startu najednou? Najdete ji [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), který obsahuje příklady kódu v tomto rychlém startu. 

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Nainstalujte klientskou knihovnu kliknutím pravým tlačítkem myši na řešení v **Průzkumníku řešení** a výběrem **možnosti Spravovat balíčky NuGet**. Ve správci balíčků, který se `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`otevře, vyberte **Procházet** a vyhledejte . Klikněte na něj, a pak **nainstalovat**. Můžete také použít [konzolu Správce balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Chcete zobrazit celý soubor kódu rychlého startu najednou? Najdete ji [na GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), který obsahuje příklady kódu v tomto rychlém startu. 

---

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Otevřete soubor *program.cs* a `using` přidejte následující direktivy:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Ve `Program` třídě aplikace vytvořte proměnné pro klíč a koncový bod prostředku.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Nahraďte metodu `Main` aplikace. Zde volané metody budete později.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Otevřete soubor *program.cs* a `using` přidejte následující direktivy:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Ve `Program` třídě aplikace vytvořte proměnné pro klíč a koncový bod prostředku. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Nahraďte metodu `Main` aplikace. Zde volané metody budete později.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objektový model

Klient Text Analytics `TextAnalyticsClient` je objekt, který se ověřuje v Azure pomocí vašeho klíče a poskytuje funkce pro přijímání textu jako jednotlivé řetězce nebo jako dávka. Text můžete odeslat do rozhraní API synchronně nebo asynchronně. Objekt odpovědi bude obsahovat informace o analýze pro každý odeslané dokument. 

Pokud používáte verzi `3.0-preview` služby, můžete použít `TextAnalyticsClientOptions` volitelnou instanci k inicializaci klienta s různými výchozími nastaveními (například výchozí jazyk nebo nápověda země). Můžete se také ověřit pomocí tokenu služby Azure Active Directory. 

## <a name="code-examples"></a>Příklady kódu

* [Analýza mínění](#sentiment-analysis)
* [Detekce jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner)
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Ujistěte se, že hlavní metoda z předchozích vytvoří nový objekt klienta s koncovým bodem a pověření.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte `ApiKeyServiceClientCredentials` novou třídu pro uložení pověření a jejich přidání do požadavků klienta. V něm vytvořte `ProcessHttpRequestAsync()` přepsání, pro `Ocp-Apim-Subscription-Key` které přidá klíč do záhlaví.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Vytvořte metodu k vytvoření instance objektu [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) `ApiKeyServiceClientCredentials` s koncovým bodem a objektem obsahujícím klíč.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analýza mínění

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Vytvořte novou `SentimentAnalysisExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho `AnalyzeSentiment()` funkce. Vrácený `Response<DocumentSentiment>` objekt bude obsahovat popisek mínění a skóre celého vstupního dokumentu a také analýzu mínění pro každou větu, pokud je úspěšná. Pokud došlo k chybě, bude `RequestFailedException`hodit .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [length {sentence.GraphemeLength}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.GraphemeOffset, sentence.GraphemeLength)}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Výstup

```console
Document sentiment: Positive

        Sentence [length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte novou `SentimentAnalysisExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho funkce [Sentiment().](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) Vrácený [Objekt SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) bude `Score` obsahovat sentiment, pokud je úspěšný, a `errorMessage` pokud ne. 

Skóre, které se blíží 0 označuje negativní sentiment, zatímco skóre, které je blíže k 1 označuje pozitivní sentiment.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Rozpoznávání jazyka

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)


Vytvořte novou `LanguageDetectionExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho `DetectLanguage()` funkce. Vrácený `Response<DetectedLanguage>` objekt bude obsahovat zjištěný jazyk spolu s jeho názvem a kódem ISO-6391. Pokud došlo k chybě, bude `RequestFailedException`hodit .

> [!Tip]
> V některých případech může být obtížné rozptýlit jazyky na základě vstupu. `countryHint` Parametr můžete použít k určení dvoupísmenný kód země. Ve výchozím nastavení rozhraní API používá "US" jako výchozí zeměHint, chcete-li odebrat toto chování, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""`. Chcete-li nastavit jiné `TextAnalyticsClientOptions.DefaultCountryHint` výchozí nastavení, nastavte vlastnost a předat ji během inicializace klienta.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Výstup

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte novou `languageDetectionExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho funkce [DetectLanguage().](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) Vrácený objekt [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) bude obsahovat `DetectedLanguages` seznam zjištěných `errorMessage` jazyků v případě úspěchu a pokud ne. Vytiskněte první vrácený jazyk.

> [!Tip]
> V některých případech může být obtížné rozptýlit jazyky na základě vstupu. `countryHint` Parametr můžete použít k určení dvoupísmenný kód země. Ve výchozím nastavení rozhraní API používá "US" jako výchozí zeměHint, chcete-li odebrat toto chování, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""` .

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Výstup

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Uznání pojmenované entity (NER)

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)


> [!NOTE]
> Novinka ve `3.0-preview`verzi :
> * Propojení entit je nyní odděleno od rozpoznávání entit.


Vytvořte novou `EntityRecognitionExample()` funkci s názvem, která přebírá `RecognizeEntities()` klienta, který jste vytvořili dříve, volat jeho funkci a iterát prostřednictvím výsledků. Vrácený `Response<IReadOnlyCollection<CategorizedEntity>>` objekt bude obsahovat seznam zjištěných entit. Pokud došlo k chybě, bude `RequestFailedException`hodit .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Výstup

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Length: 7,      Score: 0.92

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Length: 9,      Score: 0.80
```

## <a name="entity-linking"></a>Propojení entit

Vytvořte novou `EntityLinkingExample()` funkci s názvem, která přebírá `RecognizeLinkedEntities()` klienta, který jste vytvořili dříve, volat jeho funkci a iterát prostřednictvím výsledků. Vrácený `Response<IReadOnlyCollection<LinkedEntity>>` představuje seznam zjištěných entit. Pokud došlo k chybě, bude `RequestFailedException`hodit . Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, výskyty stejné entity jsou seskupeny pod objektem `LinkedEntity` jako seznam `LinkedEntityMatch` objektů.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tLength: {match.GraphemeLength},\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Výstup

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Length: 11,     Score: 0.78

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Length: 10,     Score: 0.55

                Text: Gates
                Length: 5,      Score: 0.55

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Length: 10,     Score: 0.53

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Length: 9,      Score: 0.47

                Text: Microsoft
                Length: 9,      Score: 0.47

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Length: 7,      Score: 0.25

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Length: 5,      Score: 0.28
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

> [!NOTE]
> Ve verzi 2.1 je propojení entit zahrnuto v odpovědi ner.

Vytvořte novou `RecognizeEntitiesExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho [entity().](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) Iterate přes výsledky. Vrácený objekt [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) bude obsahovat seznam `Entities` zjištěných entit v případě úspěchu `errorMessage` a pokud ne. Pro každou zjištěnou entitu vytiskněte její typ, podtyp, název Wikipedie (pokud existují) a také umístění v původním textu.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

Vytvořte novou `KeyPhraseExtractionExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve, a volání jeho `ExtractKeyPhrases()` funkce. Vrácený `<Response<IReadOnlyCollection<string>>` objekt bude obsahovat seznam zjištěných klíčových frází. Pokud došlo k chybě, bude `RequestFailedException`hodit .

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Výstup

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte novou `KeyPhraseExtractionExample()` funkci s názvem, která přebírá klienta, který jste vytvořili dříve a volání jeho [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) funkce. Výsledek bude obsahovat seznam zjištěných `KeyPhrases` klíčových frází `errorMessage` v případě úspěchu a pokud ne. Vytisknout všechny zjištěné klíčové fráze.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Výstup

```console
Key phrases:
    cat
    veterinarian
```

---
