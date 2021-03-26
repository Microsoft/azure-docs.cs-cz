---
title: 'Rychlý Start: Analýza textu klientskou knihovnu pro C# | Microsoft Docs'
description: 'Začínáme s klientskou knihovnou Analýza textu pro C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: d36efaf64edd51fd1c2b46b959af1a994f6ec956
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582222"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

[Referenční dokumentace](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview)  |  verze 3.1 [zdrojový kód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)  |  knihovny v 3.1 [balíček verze 3.1 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.5)  |  [ukázky v 3.1](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

[Referenční dokumentace V3](/dotnet/api/azure.ai.textanalytics)  |  [zdrojový kód](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics)  |  knihovny v3 [balíček V3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)  |  [ukázky V3](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/)
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" vytvořte prostředek analýza textu vytvoření prostředku "  target="_blank"> Analýza textu </a> v Azure Portal, abyste získali svůj klíč a koncový bod.  Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro analýzu textu budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Pokud chcete použít funkci analyzovat, budete potřebovat prostředek Analýza textu s cenovou úrovní Standard (y).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-net-core-application"></a>Vytvoření nové aplikace .NET Core

Pomocí integrovaného vývojového prostředí (IDE) sady Visual Studio vytvořte novou konzolovou aplikaci .NET Core. Tím se vytvoří projekt "Hello World" s jedním zdrojovým souborem C#: *program. cs*.

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. Ve Správci balíčků, který se otevře, vyberte **Vyhledat** a vyhledat `Azure.AI.TextAnalytics` . Zaškrtněte políčko **Zahrnout prerelase** , vyberte možnost verze `5.1.0-beta.5` a pak **nainstalujte**. Můžete také použít [konzolu Správce balíčků](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. Ve Správci balíčků, který se otevře, vyberte **Vyhledat** a vyhledat `Azure.AI.TextAnalytics` . Vyberte verzi `5.0.0` a pak **nainstalujte**. Můžete také použít [konzolu Správce balíčků](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).


> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), který obsahuje příklady kódu v tomto rychlém startu. 

---

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Otevřete soubor *program. cs* a přidejte následující `using` direktivy:

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

Nahraďte metodu aplikace `Main` . Zde budete definovat metody, které jsou zde volány později.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Otevřete soubor *program. cs* a přidejte následující `using` direktivy:

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

Nahraďte metodu aplikace `Main` . Zde budete definovat metody, které jsou zde volány později.

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

---

## <a name="object-model"></a>Objektový model

Klient Analýza textu je `TextAnalyticsClient` objekt, který se ověřuje v Azure pomocí vašeho klíče a poskytuje funkce pro příjem textu jako jednoho řetězce nebo jako dávky. Text do rozhraní API můžete synchronně nebo asynchronně posílat. Objekt odpovědi bude obsahovat informace o analýze pro každý dokument, který odešlete. 

Pokud používáte verzi `3.x` služby, můžete použít volitelnou `TextAnalyticsClientOptions` instanci pro inicializaci klienta s různými výchozími nastaveními (například výchozí jazyk nebo doporučení země/oblasti). Můžete se také ověřit pomocí Azure Active Directoryho tokenu. 

## <a name="code-examples"></a>Příklady kódu

* [Analýza mínění](#sentiment-analysis)
* [Dolování názoru](#opinion-mining)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner)
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Zajistěte, aby metoda Main z dřívějších verzí vytvořila nový objekt klienta s vaším koncovým bodem a přihlašovacími údaji.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Zajistěte, aby metoda Main z dřívějších verzí vytvořila nový objekt klienta s vaším koncovým bodem a přihlašovacími údaji.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

---

## <a name="sentiment-analysis"></a>Analýza mínění

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Vytvořte novou funkci s názvem `SentimentAnalysisExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `AnalyzeSentiment()` funkci. Vrácený `Response<DocumentSentiment>` objekt bude obsahovat popisek mínění a skóre celého vstupního dokumentu a také analýzu mínění pro každou větu, pokud je úspěšná. Pokud došlo k chybě, vyvolá se `RequestFailedException` .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
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

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

### <a name="opinion-mining"></a>Dolování názoru

Vytvořte novou funkci s názvem `SentimentAnalysisWithOpinionMiningExample()` , která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho `AnalyzeSentimentBatch()` funkci s `IncludeOpinionMining` možností v `AnalyzeSentimentOptions` kontejneru. Vrácený `AnalyzeSentimentResultCollection` objekt bude obsahovat kolekci, `AnalyzeSentimentResult` ve které představuje `Response<DocumentSentiment>` . Rozdíl mezi `SentimentAnalysis()` a `SentimentAnalysisWithOpinionMiningExample()` je v tom, že `SentenceOpinion` v každé větě bude v každé větě zobrazen analyzovaný cíl a související posouzení. Pokud došlo k chybě, vyvolá se `RequestFailedException` .

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (SentenceOpinion sentenceOpinion in sentence.Opinions)
            {
                Console.WriteLine($"\tTarget: {sentenceOpinion.Target.Text}, Value: {sentenceOpinion.Target.Sentiment}");
                Console.WriteLine($"\tTarget positive score: {sentenceOpinion.Target.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tTarget negative score: {sentenceOpinion.Target.ConfidenceScores.Negative:0.00}");
                foreach (AssessmentSentiment assessment in sentenceOpinion.Assessments)
                {
                    Console.WriteLine($"\t\tRelated Assessment: {assessment.Text}, Value: {assessment.Sentiment}");
                    Console.WriteLine($"\t\tRelated Assessment positive score: {assessment.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Assessment negative score: {assessment.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>Výstup

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Target: food, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: service, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: concierge, Value: Positive
        Target positive score: 1.00
        Target negative score: 0.00
                Related Assessment: nice, Value: Positive
                Related Assessment positive score: 1.00
                Related Assessment negative score: 0.00

Press any key to exit.
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte novou funkci s názvem `SentimentAnalysisExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `AnalyzeSentiment()` funkci. Vrácený `Response<DocumentSentiment>` objekt bude obsahovat popisek mínění a skóre celého vstupního dokumentu a také analýzu mínění pro každou větu, pokud je úspěšná. Pokud došlo k chybě, vyvolá se `RequestFailedException` .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
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

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

---

## <a name="language-detection"></a>Rozpoznávání jazyka

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)


Vytvořte novou funkci s názvem `LanguageDetectionExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho  `DetectLanguage()` funkci. Vrácený `Response<DetectedLanguage>` objekt bude obsahovat zjištěný jazyk spolu s jeho názvem a kódem ISO-6391. Pokud došlo k chybě, vyvolá se `RequestFailedException` .

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí `countryHint` parametru můžete zadat kód země nebo oblasti ve dvou písmenech. Rozhraní API ve výchozím nastavení používá "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""` . Chcete-li nastavit jinou výchozí hodnotu, nastavte `TextAnalyticsClientOptions.DefaultCountryHint` vlastnost a předejte ji během inicializace klienta.

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

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)


Vytvořte novou funkci s názvem `LanguageDetectionExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho  `DetectLanguage()` funkci. Vrácený `Response<DetectedLanguage>` objekt bude obsahovat zjištěný jazyk spolu s jeho názvem a kódem ISO-6391. Pokud došlo k chybě, vyvolá se `RequestFailedException` .

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí `countryHint` parametru můžete zadat kód země nebo oblasti ve dvou písmenech. Rozhraní API ve výchozím nastavení používá "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""` . Chcete-li nastavit jinou výchozí hodnotu, nastavte `TextAnalyticsClientOptions.DefaultCountryHint` vlastnost a předejte ji během inicializace klienta.

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


---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)


Vytvořte novou funkci s názvem `EntityRecognitionExample()` , která převezme klienta, který jste vytvořili dříve, zavolejte jeho `RecognizeEntities()` funkci a Iterujte pomocí výsledků. Vrácený `Response<CategorizedEntityCollection>` objekt bude obsahovat kolekci zjištěných entit `CategorizedEntity` . Pokud došlo k chybě, vyvolá se `RequestFailedException` .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>Výstup

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>Propojení entit

Vytvořte novou funkci s názvem `EntityLinkingExample()` , která převezme klienta, který jste vytvořili dříve, zavolejte jeho `RecognizeLinkedEntities()` funkci a Iterujte pomocí výsledků. Vrácený `Response<LinkedEntityCollection>` objekt bude obsahovat kolekci zjištěných entit `LinkedEntity` . Pokud došlo k chybě, vyvolá se `RequestFailedException` . Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, jsou výskyty stejné entity seskupeny do `LinkedEntity` objektu jako seznam `LinkedEntityMatch` objektů.

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
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>Výstup

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>Rozpoznávání identifikovatelných osobních údajů

Vytvořte novou funkci s názvem `RecognizePIIExample()` , která převezme klienta, který jste vytvořili dříve, zavolejte jeho `RecognizePiiEntities()` funkci a Iterujte pomocí výsledků. Vrácený `PiiEntityCollection` představuje seznam zjištěných entit PII. Pokud došlo k chybě, vyvolá se `RequestFailedException` .

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>Výstup

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)


> [!NOTE]
> Novinka ve verzi `3.0` :
> * Propojení entit je teď oddělené od rozpoznávání entit.


Vytvořte novou funkci s názvem `EntityRecognitionExample()` , která převezme klienta, který jste vytvořili dříve, zavolejte jeho `RecognizeEntities()` funkci a Iterujte pomocí výsledků. Vrácený `Response<IReadOnlyCollection<CategorizedEntity>>` objekt bude obsahovat seznam zjištěných entit. Pokud došlo k chybě, vyvolá se `RequestFailedException` .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Výstup

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

### <a name="entity-linking"></a>Propojení entit

Vytvořte novou funkci s názvem `EntityLinkingExample()` , která převezme klienta, který jste vytvořili dříve, zavolejte jeho `RecognizeLinkedEntities()` funkci a Iterujte pomocí výsledků. Vrácený `Response<IReadOnlyCollection<LinkedEntity>>` představuje seznam zjištěných entit. Pokud došlo k chybě, vyvolá se `RequestFailedException` . Vzhledem k tomu, že propojené entity jsou jednoznačně identifikovány, jsou výskyty stejné entity seskupeny do `LinkedEntity` objektu jako seznam `LinkedEntityMatch` objektů.

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
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
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
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

--- 


### <a name="key-phrase-extraction"></a>Extrakce klíčových frází

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

Vytvořte novou funkci s názvem `KeyPhraseExtractionExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `ExtractKeyPhrases()` funkci. Vrácený `<Response<KeyPhraseCollection>` objekt bude obsahovat seznam zjištěných klíčových frází. Pokud došlo k chybě, vyvolá se `RequestFailedException` .

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

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Vytvořte novou funkci s názvem `KeyPhraseExtractionExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `ExtractKeyPhrases()` funkci. Vrácený `<Response<IReadOnlyCollection<string>>` objekt bude obsahovat seznam zjištěných klíčových frází. Pokud došlo k chybě, vyvolá se `RequestFailedException` .

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

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Asynchronní použití rozhraní API s operací analyzovat

# <a name="version-31-preview"></a>[Verze 3,1 Preview](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Vytvořte novou funkci s názvem `AnalyzeOperationExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `StartAnalyzeBatchActionsAsync()` funkci. Vrácený `AnalyzeBatchActionsOperation` objekt bude obsahovat `Operation` objekt rozhraní. Vzhledem k tomu, že se jedná o dlouhodobou provozní operaci, `await` `operation.WaitForCompletionAsync()` pro hodnotu, která se má aktualizovat. Jakmile `WaitForCompletionAsync()` je dokončeno, kolekce by měla být aktualizována v `operation.Value` . Pokud došlo k chybě, vyvolá se `RequestFailedException` .


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };


    TextAnalyticsActions actions = new TextAnalyticsActions()
    {
        RecognizeEntitiesOptions = new List<RecognizeEntitiesOptions>() { new RecognizeEntitiesOptions() },
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeBatchActionsOperation operation = await client.StartAnalyzeBatchActionsAsync(batchDocuments, actions);

    await operation.WaitForCompletionAsync();

    Console.WriteLine($"Status: {operation.Status}");
    Console.WriteLine($"Created On: {operation.CreatedOn}");
    Console.WriteLine($"Expires On: {operation.ExpiresOn}");
    Console.WriteLine($"Last modified: {operation.LastModified}");
    if (!string.IsNullOrEmpty(operation.DisplayName))
        Console.WriteLine($"Display name: {operation.DisplayName}");
    Console.WriteLine($"Total actions: {operation.TotalActions}");
    Console.WriteLine($"  Succeeded actions: {operation.ActionsSucceeded}");
    Console.WriteLine($"  Failed actions: {operation.ActionsFailed}");
    Console.WriteLine($"  In progress actions: {operation.ActionsInProgress}");

    await foreach (AnalyzeBatchActionsResult documentsInPage in operation.Value)
    {
        RecognizeEntitiesResultCollection entitiesResult = documentsInPage.RecognizeEntitiesActionsResults.FirstOrDefault().Result;

        Console.WriteLine("Recognized Entities");

        foreach (RecognizeEntitiesResult result in entitiesResult)
        {
            Console.WriteLine($"  Recognized the following {result.Entities.Count} entities:");

            foreach (CategorizedEntity entity in result.Entities)
            {
                Console.WriteLine($"  Entity: {entity.Text}");
                Console.WriteLine($"  Category: {entity.Category}");
                Console.WriteLine($"  Offset: {entity.Offset}");
                Console.WriteLine($"  Length: {entity.Length}");
                Console.WriteLine($"  ConfidenceScore: {entity.ConfidenceScore}");
                Console.WriteLine($"  SubCategory: {entity.SubCategory}");
            }
            Console.WriteLine("");
        }
    }
}
```

Po přidání tohoto příkladu do aplikace zavolejte `main()` metodu pomocí `await` .

```csharp
await AnalyzeOperationExample(client).ConfigureAwait(false);
```
### <a name="output"></a>Výstup

```console
Status: succeeded
Created On: 3/10/2021 2:25:01 AM +00:00
Expires On: 3/11/2021 2:25:01 AM +00:00
Last modified: 3/10/2021 2:25:05 AM +00:00
Display name: Analyze Operation Quick Start Example
Total actions: 1
  Succeeded actions: 1
  Failed actions: 0
  In progress actions: 0
Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

Můžete také použít operaci analyzovat ke zjištění kódu PII a extrakce klíčových frází. Podívejte se na téma [Analýza ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples) na GitHubu.

# <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Tato funkce není k dispozici ve verzi 3,0.

---
