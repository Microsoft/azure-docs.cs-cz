---
title: 'Rychlý start: Klientská knihovna Analýzy textu pro jazyk C# | Microsoft Docs'
description: Začínáme s klientskou knihovnou Analýzy textu pro jazyk C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 6adce0ed6b5b5768bd9a489fced25ce439a33e0a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203394"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

[Referenční dokumentace k v3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Zdrojový kód knihovny v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [Balíček v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Ukázky v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

[Referenční dokumentace k v2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Zdrojový kód knihovny v2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Balíček v2 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Ukázky v2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Můžete si ho [vytvořit zdarma](https://azure.microsoft.com/free/).
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/)
* Jakmile máte předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Vytvoření prostředku Analýzy textu"  target="_blank">vytvořte si prostředek Analýza textu<span class="docon docon-navigate-external x-hidden-focus"></span></a> na webu Azure Portal. Získáte klíč a koncový bod. 
    * Tento klíč a koncový bod z vytvářeného prostředku budete potřebovat pro připojení vaší aplikace k rozhraní API pro analýzu textu. Uděláte to v další fázi tohoto rychlého zprovoznění.
    * Můžete si službu vyzkoušet s bezplatnou cenovou úrovní a pro produkční prostředí později upgradovat na placenou úroveň.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-net-core-application"></a>Vytvoření nové aplikace .NET Core

Pomocí integrovaného vývojového prostředí (IDE) sady Visual Studio vytvořte novou aplikaci konzoly .NET Core. Vytvoří se projekt Hello World s jedním zdrojovým souborem jazyka C#: *program.cs*.

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Klikněte pravým tlačítkem na řešení v **Průzkumníku řešení**, vyberte **Spravovat balíčky NuGet** a nainstalujte klientskou knihovnu. Ve správci balíčků, který se otevře, vyberte **Procházet**, zaškrtněte políčko **Zahrnout předběžné verze** a vyhledejte `Azure.AI.TextAnalytics`. Vyberte verzi `1.0.0-preview.2` a pak vyberte **Nainstalovat**. Můžete použít také [konzolu správce balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Chcete zobrazit celý soubor s kódem rychlého startu najednou? Soubor s příklady kódu v tomto rychlém startu najdete [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs). 

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Klikněte pravým tlačítkem na řešení v **Průzkumníku řešení**, vyberte **Spravovat balíčky NuGet** a nainstalujte klientskou knihovnu. Ve správci balíčků, který se otevře, vyberte **Procházet** a vyhledejte `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Klikněte na tuto položku a pak na **Nainstalovat**. Můžete použít také [konzolu správce balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Chcete zobrazit celý soubor s kódem rychlého startu najednou? Soubor s příklady kódu v tomto rychlém startu najdete [na GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs). 

---

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Otevřete soubor *program.cs* a přidejte následující direktivy `using`:

```csharp
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

V třídě `Program` aplikace vytvořte proměnné pro klíč a koncový bod vašeho prostředku.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Nahraďte metodu `Main` aplikace. Metody, které se tady volají, nadefinujete později.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
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

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Otevřete soubor *program.cs* a přidejte následující direktivy `using`:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

V třídě `Program` aplikace vytvořte proměnné pro klíč a koncový bod vašeho prostředku. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Nahraďte metodu `Main` aplikace. Metody, které se tady volají, nadefinujete později.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objektový model

Klient Analýzy textu je objekt `TextAnalyticsClient`, který se ověřuje pro Azure pomocí vašeho klíče a nabízí funkce umožňující příjem textu jako jednotlivých řetězců nebo dávky. Text můžete do rozhraní API odesílat synchronně nebo asynchronně. Objekt odpovědi bude obsahovat analytické informace o jednotlivých odeslaných dokumentech. 

Pokud používáte verzi `3.0-preview`, můžete pomocí volitelné instance `TextAnalyticsClientOptions` inicializovat klienta s různými výchozími nastaveními (například výchozí jazyk nebo nápověda k zemi). K ověřování můžete použít také token Azure Active Directory. 

## <a name="code-examples"></a>Příklady kódu

* [Analýza mínění](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner)
* [Rozpoznávání osobních údajů](#detect-personal-information)
* [Spojování entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Ujistěte se, že vaše metoda main z dřívější části vytváří nový objekt klienta s vaším koncovým bodem a vašimi přihlašovacími údaji.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

Vytvořte novou třídu `ApiKeyServiceClientCredentials`, která uloží přihlašovací údaje a přidá je do požadavků klienta. V této třídě vytvořte přepsání funkce `ProcessHttpRequestAsync()`, která přidá váš klíč do hlavičky `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Vytvořte metodu, který vytvoří instanci objektu [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) s vaším koncovým bodem a objektem `ApiKeyServiceClientCredentials` obsahujícím váš klíč.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analýza mínění

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Vytvořte novou funkci `SentimentAnalysisExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci `AnalyzeSentiment()`. Pokud vše proběhne úspěšně, vrácený objekt `Response<DocumentSentiment>` bude obsahovat popisek mínění a skóre celého vstupního dokumentu a také analýzu mínění pro jednotlivé věty. Pokud dojde k chybě, vyvolá se výjimka `RequestFailedException`.

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

Vytvořte novou funkci `SentimentAnalysisExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet). Pokud vše proběhne úspěšně, vrácený objekt [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) bude obsahovat skóre mínění v objektu `Score`, jinak bude obsahovat objekt `errorMessage`. 

Skóre blížící se 0 značí negativní mínění, zatímco skóre blížící se 1 značí pozitivní mínění.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Rozpoznávání jazyka

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)


Vytvořte novou funkci `LanguageDetectionExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci `DetectLanguage()`. Vrácený objekt `Response<DetectedLanguage>` bude obsahovat zjištěný jazyk, jeho název a kód ISO-6391. Pokud dojde k chybě, vyvolá se výjimka `RequestFailedException`.

> [!Tip]
> V některých případech může být obtížné jednoznačně rozpoznat jazyk na základě vstupu. Pomocí parametru `countryHint` můžete zadat dvoupísmenný kód země. Rozhraní API ve výchozím nastavení jako hodnotu countryHint používá US. Pokud chcete toto chování zrušit, můžete tento parametr resetovat nastavením této hodnoty na prázdný řetězec – `countryHint = ""`. Pokud chcete nastavit jinou výchozí hodnotu, nastavte vlastnost `TextAnalyticsClientOptions.DefaultCountryHint` a předejte ji během inicializace klienta.

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

Vytvořte novou funkci `languageDetectionExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Pokud vše proběhne úspěšně, vrácený objekt [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) bude obsahovat seznam zjištěných jazyků v objektu `DetectedLanguages`, jinak bude obsahovat objekt `errorMessage`. Vytiskněte první vrácený jazyk.

> [!Tip]
> V některých případech může být obtížné jednoznačně rozpoznat jazyk na základě vstupu. Pomocí parametru `countryHint` můžete zadat dvoupísmenný kód země. Rozhraní API ve výchozím nastavení jako hodnotu countryHint používá US. Pokud chcete toto chování zrušit, můžete tento parametr resetovat nastavením této hodnoty na prázdný řetězec – `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Výstup

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER, Named Entity Recognition)

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)


> [!NOTE]
> Novinky ve verzi `3.0-preview`:
> * Rozpoznávání entit teď zahrnuje možnost rozpoznávat v textu osobní údaje.
> * Spojování entit je teď oddělené od rozpoznávání entit.


Vytvořte novou funkci `EntityRecognitionExample()`, která přebírá objekt klienta, který jste vytvořili dříve, volá jeho funkci `RecognizeEntities()` a iteruje přes seznam výsledků. Vrácený objekt `Response<IReadOnlyCollection<CategorizedEntity>>` bude obsahovat seznam zjištěných entit. Pokud dojde k chybě, vyvolá se výjimka `RequestFailedException`.

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

## <a name="detect-personal-information"></a>Rozpoznávání osobních údajů

Vytvořte novou funkci `EntityPIIExample()`, která přebírá objekt klienta, který jste vytvořili dříve, volá jeho funkci `RecognizePiiEntities()` a iteruje přes seznam výsledků. Podobně jako u předchozí funkce bude vrácený objekt `Response<IReadOnlyCollection<CategorizedEntity>>` obsahovat seznam zjištěných entit. Pokud dojde k chybě, vyvolá se výjimka `RequestFailedException`.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    string inputText = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    var response = client.RecognizePiiEntities(inputText);
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Výstup

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Category: U.S. Social Security Number (SSN),    Sub-Category:
                Length: 11,     Score: 0.85
```


## <a name="entity-linking"></a>Spojování entit

Vytvořte novou funkci `EntityLinkingExample()`, která přebírá objekt klienta, který jste vytvořili dříve, volá jeho funkci `RecognizeLinkedEntities()` a iteruje přes seznam výsledků. Vrácený objekt `Response<IReadOnlyCollection<LinkedEntity>>` představuje seznam zjištěných entit. Pokud dojde k chybě, vyvolá se výjimka `RequestFailedException`. Vzhledem k tomu, že jsou propojené entity jednoznačně identifikované, výskyty stejné entity se seskupují v rámci objektu `LinkedEntity` jako seznam objektů `LinkedEntityMatch`.

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
> Ve verzi 2.1 je spojování entit součástí odpovědi NER.

Vytvořte novou funkci `RecognizeEntitiesExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Iterujte přes seznam výsledků. Pokud vše proběhne úspěšně, vrácený objekt [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) bude obsahovat seznam zjištěných entit v objektu `Entities`, jinak bude obsahovat objekt `errorMessage`. Vytiskněte typ, podtyp a název na Wikipedii (pokud existují) jednotlivých zjištěných entit a také jejich umístění v původním textu.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

#### <a name="version-30-preview"></a>[Verze 3.0 – Preview](#tab/version-3)

Vytvořte novou funkci `KeyPhraseExtractionExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci `ExtractKeyPhrases()`. Vrácený objekt `<Response<IReadOnlyCollection<string>>` bude obsahovat seznam zjištěných klíčových frází. Pokud dojde k chybě, vyvolá se výjimka `RequestFailedException`.

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

Vytvořte novou funkci `KeyPhraseExtractionExample()`, která přebírá objekt klienta, který jste vytvořili dříve, a volá jeho funkci [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Pokud vše proběhne úspěšně, výsledek bude obsahovat seznam zjištěných klíčových frází v objektu `KeyPhrases`, jinak bude obsahovat objekt `errorMessage`. Vytiskněte všechny zjištěné klíčové fráze.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Výstup

```console
Key phrases:
    cat
    veterinarian
```

---
