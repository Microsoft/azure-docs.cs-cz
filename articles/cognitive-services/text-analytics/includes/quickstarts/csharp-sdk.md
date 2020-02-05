---
title: 'Rychlý Start: Klientská knihovna C# analýza textu pro | Microsoft Docs'
description: Začínáme s klientskou knihovnou Analýza textu proC#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: a907fb5347e6559e066f678c53bb16c7727a74b5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987880"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

[Referenční dokumentace v3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [zdrojový kód knihovny v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3 Package (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [V3 Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

[Referenční dokumentace v2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [zdrojový kód knihovny v2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2 Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 Samples](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-net-core-application"></a>Vytvoření nové aplikace .NET Core

Pomocí integrovaného vývojového prostředí (IDE) sady Visual Studio vytvořte novou konzolovou aplikaci .NET Core. Tím se vytvoří projekt "Hello World" s jedním C# zdrojovým souborem: *program.cs*.

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

Nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. Ve Správci balíčků, který se otevře, vyberte **Procházet**, zaškrtněte políčko **Zahrnout předprodejní**a vyhledejte `Azure.AI.TextAnalytics`. Klikněte na něj a pak na **nainstalovat**. Můžete také použít [konzolu Správce balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Otevřete soubor *program.cs* a přidejte následující direktivy `using`:

```csharp
using System;
using Azure.AI.TextAnalytics;
```

V `Program` třídě aplikace vytvořte proměnné pro klíč a koncový bod prostředku.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Nahraďte metodu `Main` aplikace. Zde budete definovat metody, které jsou zde volány později.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

Nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. V okně Správce balíčků, které se otevře, vyberte **Procházet** a vyhledejte `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Klikněte na něj a pak na **nainstalovat**. Můžete také použít [konzolu Správce balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Otevřete soubor *program.cs* a přidejte následující direktivy `using`:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

V `Program` třídě aplikace vytvořte proměnné pro klíč a koncový bod prostředku. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Nahraďte metodu `Main` aplikace. Zde budete definovat metody, které jsou zde volány později.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objektový model

Klient Analýza textu je `TextAnalyticsClient` objekt, který se ověřuje v Azure pomocí vašeho klíče a poskytuje funkce pro příjem textu jako jednoho řetězce nebo jako dávky. Text do rozhraní API můžete synchronně nebo asynchronně posílat. Objekt odpovědi bude obsahovat informace o analýze pro každý dokument, který odešlete. 

Pokud používáte verzi `3.0-preview`, můžete použít volitelnou `TextAnalyticsClientOptions` instanci k inicializaci klienta s různými výchozími nastaveními (například výchozí jazyk nebo doporučení země). Můžete se také ověřit pomocí Azure Active Directoryho tokenu. 

## <a name="code-examples"></a>Příklady kódu

* [Analýza mínění](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner)
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

Zajistěte, aby metoda Main z dřívějších verzí vytvořila nový objekt klienta s vaším koncovým bodem a klíčem. 

```csharp
var client = new TextAnalyticsClient(endpoint, key);
```

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

Vytvořte novou třídu `ApiKeyServiceClientCredentials` pro uložení přihlašovacích údajů a přidejte je do požadavků klienta. V rámci něj Vytvořte přepsání pro `ProcessHttpRequestAsync()`, které přidá klíč do hlavičky `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Vytvořte metodu pro vytvoření instance objektu [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) s vaším koncovým bodem a objektem `ApiKeyServiceClientCredentials`, který obsahuje váš klíč.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analýza subjektivního hodnocení

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

Vytvořte novou funkci nazvanou `SentimentAnalysisExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci `AnalyzeSentiment()`. Vrácený objekt `Response<AnalyzeSentimentResult>` bude obsahovat popisek mínění a skóre celého vstupního dokumentu a také analýzu mínění pro každou větu, pokud je úspěšná, a `Value.ErrorMessage` Pokud ne.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tSentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"\tPositive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### <a name="output"></a>Výstup

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

Vytvořte novou funkci nazvanou `SentimentAnalysisExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci [mínění ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) . Vrácený objekt [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) bude obsahovat mínění `Score` v případě úspěchu a `errorMessage`, pokud ne. 

Skóre, které je blízko 0, označuje negativní mínění, zatímco skóre, které je blíže 1, značí kladné mínění.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Detekce jazyka

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)


Vytvořte novou funkci nazvanou `LanguageDetectionExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci `DetectLanguage()`. Vrácený objekt `Response<DetectLanguageResult>` bude v případě úspěchu obsahovat nalezený jazyk v `Value.PrimaryLanguage` a `Value.ErrorMessage`, pokud ne.

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí parametru `countryHint` můžete zadat kód země se dvěma písmeny. Ve výchozím nastavení používá rozhraní API "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""`. Chcete-li nastavit jinou výchozí hodnotu, nastavte vlastnost `TextAnalyticsClientOptions.DefaultCountryHint` a předejte ji během inicializace klienta.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    var detectedLanguage = response.Value.PrimaryLanguage;
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Výstup

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

Vytvořte novou funkci nazvanou `languageDetectionExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci [operaci DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Vrácený objekt [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) bude obsahovat seznam zjištěných jazyků v `DetectedLanguages` v případě úspěchu a `errorMessage`, pokud ne.  Tisk prvního vráceného jazyka.

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí parametru `countryHint` můžete zadat kód země se dvěma písmeny. Ve výchozím nastavení používá rozhraní API "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Výstup

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)


> [!NOTE]
> Ve verzi `3.0-preview`:
> * NER obsahuje samostatné metody zjišťování osobních údajů. 
> * Odkaz na entitu je samostatný požadavek než NER.

Níže uvedený kód slouží ke zjišťování osobních údajů pomocí NER v3, který je ve verzi Public Preview.


Vytvořte novou funkci nazvanou `EntityRecognitionExample()`, která převezme klienta, který jste vytvořili dříve, zavolejte jeho funkci `RecognizeEntities()` a Iterujte výsledky. Vrácený objekt `Response<RecognizeEntitiesResult>` bude obsahovat seznam zjištěných entit v `Value.NamedEntities` v případě úspěchu a `Value.ErrorMessage`, pokud ne. Pro každou zjištěnou entitu, pokud existuje, vytiskněte její typ a dílčí typ.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Výstup

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="using-ner-to-detect-personal-information"></a>Použití NER k detekci osobních údajů

Vytvořte novou funkci nazvanou `EntityPIIExample()`, která převezme klienta, který jste vytvořili dříve, zavolejte jeho funkci `RecognizePiiEntities()` a Iterujte výsledky. Podobně jako u předchozí funkce bude vrácený objekt `Response<RecognizeEntitiesResult>` obsahovat seznam zjištěných entit v `Value.NamedEntities` v případě úspěchu a `Value.ErrorMessage`, pokud ne.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Výstup

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```


## <a name="entity-linking"></a>Propojení entit

Vytvořte novou funkci nazvanou `EntityLinkingExample()`, která převezme klienta, který jste vytvořili dříve, zavolejte jeho funkci `RecognizeLinkedEntities()` a Iterujte výsledky. Vrácený objekt `Response<RecognizeLinkedEntitiesResult>` bude obsahovat seznam zjištěných entit v `Value.LinkedEntities` v případě úspěchu a `Value.ErrorMessage`, pokud ne. Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, jsou výskyty stejné entity seskupeny pod objektem `LinkedEntity` jako seznam objektů `LinkedEntityMatch`.

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
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
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
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

> [!NOTE]
> Ve verzi 2,1 je odkaz na entitu součástí odpovědi NER.

Vytvořte novou funkci nazvanou `RecognizeEntitiesExample()`, která převezme klienta, který jste vytvořili dříve, a zavolejte jeho funkce [Entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Iterujte výsledky. Vrácený objekt [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) bude obsahovat seznam zjištěných entit v `Entities` v případě úspěchu a `errorMessage`, pokud ne. Pro každou zjištěnou entitu vytiskněte svůj typ, dílčí typ, Wikipedii název (pokud existují) a také umístění v původním textu.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

#### <a name="version-30-previewtabversion-3"></a>[Verze 3,0-Preview](#tab/version-3)

Vytvořte novou funkci nazvanou `KeyPhraseExtractionExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci `ExtractKeyPhrases()`. Výsledek bude obsahovat seznam zjištěných klíčových frází v `Value.KeyPhrases` v případě úspěchu a `Value.ErrorMessage`, pokud ne. Vytiskněte všechny zjištěné klíčové fráze.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
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

#### <a name="version-21tabversion-2"></a>[Verze 2,1](#tab/version-2)

Vytvořte novou funkci nazvanou `KeyPhraseExtractionExample()`, která převezme klienta, kterého jste vytvořili dříve a zavolejte [jeho funkci.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) Výsledek bude obsahovat seznam zjištěných klíčových frází v `KeyPhrases` v případě úspěchu a `errorMessage`, pokud ne. Vytiskněte všechny zjištěné klíčové fráze.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Výstup

```console
Key phrases:
    cat
    veterinarian
```

---