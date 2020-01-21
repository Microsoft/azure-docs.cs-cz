---
title: 'Rychlý Start: Klientská knihovna Analýza textu C# v3 pro | Microsoft Docs'
description: Začínáme s klientskou knihovnou V3 Analýza textu proC#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2cf9a006e1f6f1edb996aa9beaf8934a14af29df
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281135"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Ukázka](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples) | [ukázka zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [balíčků (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)

> [!NOTE]
> * V tomto rychlém startu se používá `3.0-preview` verze Analýza textu klientské knihovny, která zahrnuje verzi Public Preview pro vylepšené [Analýza mínění](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) a [rozpoznávání pojmenovaných entit (ner)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Pro produkční scénáře doporučujeme použití dávkových asynchronních metod pro výkon a škálovatelnost. Například volání `AnalyzeSentimentAsync()` namísto `AnalyzeSentiment()`.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-text-analytics-azure-resource"></a>Vytvoření prostředku Azure Analýza textu

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>Vytvoření nové aplikace .NET Core

Pomocí integrovaného vývojového prostředí (IDE) sady Visual Studio vytvořte novou konzolovou aplikaci .NET Core. Tím se vytvoří projekt "Hello World" s jedním C# zdrojovým souborem: *program.cs*.

Nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. Ve Správci balíčků, který se otevře, vyberte **Procházet**, zaškrtněte políčko **Zahrnout předprodejní**a vyhledejte `Azure.AI.TextAnalytics`. Klikněte na něj a pak na **nainstalovat**. Můžete také použít [konzolu Správce balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Otevřete soubor *program.cs* a přidejte následující direktivy `using`:

```csharp
using System;
using Azure.AI.TextAnalytics;
```

V `Program` třídě aplikace vytvořte proměnné pro klíč a koncový bod prostředku.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Nahraďte metodu `Main` aplikace. Zde budete definovat metody, které jsou zde volány později.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);

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

## <a name="object-model"></a>Objektový model

Klient Analýza textu je `TextAnalyticsClient` objekt, který se ověřuje v Azure pomocí vašeho klíče a poskytuje funkce pro příjem textu jako jednoho řetězce nebo jako dávky. Text do rozhraní API můžete synchronně nebo asynchronně posílat. Objekt odpovědi bude obsahovat informace o analýze pro každý dokument, který odešlete. Volitelná `TextAnalyticsClientOptions` instance může být použita k inicializaci klienta s různými výchozími nastaveními (například výchozí jazyk nebo doporučení země).

Další příklady, včetně ověřování AAD a použití výchozího nastavení klienta, najdete [tady](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples).

## <a name="code-examples"></a>Příklady kódu

* [Analýza mínění](#sentiment-analysis) (Public Preview)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání pojmenovaných entit](#named-entity-recognition-public-preview) (Public Preview)
* [Rozpoznávání pojmenovaných entit – osobní informace](#named-entity-recognition---personal-information-public-preview) (Public Preview)
* [Propojení entit](#entity-linking)
* [Extrakce klíčových frází](#key-phrase-extraction)

V metodě `main()` programu zavolejte metodu ověřování pro vytvoření instance klienta.

## <a name="sentiment-analysis-public-preview"></a>Analýza mínění (Public Preview)

> [!NOTE]
> Níže uvedený kód je pro Analýza mínění v3, který je ve verzi Public Preview.

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

## <a name="language-detection"></a>Detekce jazyka

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

## <a name="named-entity-recognition-public-preview"></a>Rozpoznávání pojmenovaných entit (Public Preview)

> [!NOTE]
> Níže je uvedený kód pro rozpoznávání pojmenovaných entit v3, který je ve verzi Public Preview.

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

## <a name="named-entity-recognition---personal-information-public-preview"></a>Rozpoznávání pojmenovaných entit – osobní informace (Public Preview)

> [!NOTE]
> Níže uvedený kód slouží ke zjišťování osobních údajů pomocí rozpoznávání pojmenovaných entit v3, který je ve verzi Public Preview.

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

## <a name="entity-linking"></a>Propojování entit

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

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

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
