---
title: 'Rychlý Start: Analýza textu klientskou knihovnu pro C# | Microsoft Docs'
description: 'Začínáme s klientskou knihovnou Analýza textu pro C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: e27719f7a639a97a0cde04848a51ba6135c3199e
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84346236"
---
<a name="HOLTop"></a>

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

[Referenční dokumentace V3](https://aka.ms/azsdk-net-textanalytics-ref-docs)  |  [zdrojový kód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics)  |  knihovny v3 [balíček V3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)  |  [ukázky V3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

[Referenční dokumentace v2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview)  |  [zdrojový kód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics)  |  knihovny v2 [balíček v2 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/)  |  [ukázky v2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/)
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" vytvořte prostředek analýza textu vytvoření prostředku "  target="_blank"> Analýza textu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod.  Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro analýzu textu budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-net-core-application"></a>Vytvoření nové aplikace .NET Core

Pomocí integrovaného vývojového prostředí (IDE) sady Visual Studio vytvořte novou konzolovou aplikaci .NET Core. Tím se vytvoří projekt "Hello World" s jedním zdrojovým souborem C#: *program.cs*.

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. Ve Správci balíčků, který se otevře, vyberte **Procházet**, zaškrtněte políčko **Zahrnout předprodejní**a vyhledejte `Azure.AI.TextAnalytics` . Vyberte verzi `1.0.0-preview.5` a pak **nainstalujte**. Můžete také použít [konzolu Správce balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), který obsahuje příklady kódu v tomto rychlém startu. 

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. V okně Správce balíčků, které se otevře, vyberte **Vyhledat** a vyhledat `Microsoft.Azure.CognitiveServices.Language.TextAnalytics` . Klikněte na něj a pak na **nainstalovat**. Můžete také použít [konzolu Správce balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), který obsahuje příklady kódu v tomto rychlém startu. 

---

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Otevřete soubor *program.cs* a přidejte následující `using` direktivy:

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

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Otevřete soubor *program.cs* a přidejte následující `using` direktivy:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Ve `Program` třídě aplikace vytvořte proměnné pro klíč a koncový bod prostředku. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Nahraďte metodu aplikace `Main` . Zde budete definovat metody, které jsou zde volány později.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objektový model

Klient Analýza textu je `TextAnalyticsClient` objekt, který se ověřuje v Azure pomocí vašeho klíče a poskytuje funkce pro příjem textu jako jednoho řetězce nebo jako dávky. Text do rozhraní API můžete synchronně nebo asynchronně posílat. Objekt odpovědi bude obsahovat informace o analýze pro každý dokument, který odešlete. 

Pokud používáte verzi `3.0` služby, můžete použít volitelnou `TextAnalyticsClientOptions` instanci pro inicializaci klienta s různými výchozími nastaveními (například výchozí jazyk nebo doporučení země/oblasti). Můžete se také ověřit pomocí Azure Active Directoryho tokenu. 

## <a name="code-examples"></a>Příklady kódu

* [Analýza mínění](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-ner)
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Zajistěte, aby metoda Main z dřívějších verzí vytvořila nový objekt klienta s vaším koncovým bodem a přihlašovacími údaji.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvořte novou `ApiKeyServiceClientCredentials` třídu pro uložení přihlašovacích údajů a přidejte ji do požadavků klienta. V rámci něj Vytvořte přepsání pro `ProcessHttpRequestAsync()` , které přidá klíč do `Ocp-Apim-Subscription-Key` záhlaví.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Vytvořte metodu pro vytvoření instance objektu [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) s vaším koncovým bodem a `ApiKeyServiceClientCredentials` objektem, který obsahuje váš klíč.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analýza mínění

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

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

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvořte novou funkci s názvem `SentimentAnalysisExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho funkci [mínění ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) . Vrácený objekt [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) bude obsahovat mínění v `Score` případě úspěchu a v `errorMessage` případě potřeby. 

Skóre, které je blízko 0, označuje negativní mínění, zatímco skóre, které je blíže 1, značí kladné mínění.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Rozpoznávání jazyka

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)


Vytvořte novou funkci s názvem `LanguageDetectionExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho `DetectLanguage()` funkci. Vrácený `Response<DetectedLanguage>` objekt bude obsahovat zjištěný jazyk spolu s jeho názvem a kódem ISO-6391. Pokud došlo k chybě, vyvolá se `RequestFailedException` .

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

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvořte novou funkci s názvem `languageDetectionExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho funkci [operaci DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Vrácený objekt [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) bude obsahovat seznam zjištěných jazyků v `DetectedLanguages` případě úspěchu a v `errorMessage` případě potřeby. Tisk prvního vráceného jazyka.

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí `countryHint` parametru můžete zadat kód země nebo oblasti ve dvou písmenech. Rozhraní API ve výchozím nastavení používá "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""` .

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Výstup

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)


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

## <a name="entity-linking"></a>Propojení entit

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

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

> [!NOTE]
> Ve verzi 2,1 je odkaz na entitu součástí odpovědi NER.

Vytvořte novou funkci s názvem `RecognizeEntitiesExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho funkce [Entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Iterujte výsledky. Vrácený objekt [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) bude obsahovat seznam zjištěných entit v `Entities` případě úspěchu a v `errorMessage` případě potřeby. Pro každou zjištěnou entitu vytiskněte svůj typ, dílčí typ, Wikipedii název (pokud existují) a také umístění v původním textu.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

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

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

Vytvořte novou funkci s názvem `KeyPhraseExtractionExample()` , která převezme klienta, který jste vytvořili dříve, [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) a zavolejte jeho funkci. Výsledek bude obsahovat seznam zjištěných klíčových frází v `KeyPhrases` případě úspěchu a v `errorMessage` případě potřeby. Vytiskněte všechny zjištěné klíčové fráze.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Výstup

```console
Key phrases:
    cat
    veterinarian
```

---
