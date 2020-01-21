---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: de6601a60bbb65787673d9d3bf5b1cbe9f46804a
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281380"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Ukázka](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) | [ukázka zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [balíčků (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/)

> [!NOTE]
> Kód v tomto článku používá pro jednoduchost synchronní metody Analýza textu .NET SDK. Pro produkční scénáře doporučujeme použití dávkových asynchronních metod pro výkon a škálovatelnost. Například volání [SentimentBatchAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) namísto [mínění ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-text-analytics-azure-resource"></a>Vytvoření prostředku Azure Analýza textu

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>Vytvoření nové aplikace .NET Core

Pomocí integrovaného vývojového prostředí (IDE) sady Visual Studio vytvořte novou konzolovou aplikaci .NET Core. Tím se vytvoří jednoduchý projekt "Hello World" s jedním C# zdrojovým souborem: *program.cs*.

Nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. V okně Správce balíčků, které se otevře, vyberte **Procházet** a vyhledejte `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Klikněte na něj a pak na **nainstalovat**. Můžete také použít [konzolu Správce balíčků](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Otevřete soubor *program.cs* a přidejte následující direktivy `using`:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

V `Program` třídě aplikace vytvořte proměnné pro klíč a koncový bod prostředku. 

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info-v2.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Nahraďte metodu `Main` aplikace. Zde budete definovat metody, které jsou zde volány později.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

## <a name="object-model"></a>Objektový model

Klient Analýza textu je objekt [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) , který se ověřuje v Azure pomocí vašeho klíče a poskytuje funkce pro příjem textu jako jednoho řetězce nebo jako dávky. Text do rozhraní API můžete synchronně nebo asynchronně posílat. Objekt odpovědi bude obsahovat informace o analýze pro každý dokument, který odešlete. 

## <a name="code-examples"></a>Příklady kódu

* [Ověření klienta](#authenticate-the-client)
* [Analýza subjektivního hodnocení](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání entit](#entity-recognition)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte novou třídu `ApiKeyServiceClientCredentials` pro uložení přihlašovacích údajů a přidejte je do požadavků klienta. V rámci něj Vytvořte přepsání pro `ProcessHttpRequestAsync()`, které přidá klíč do hlavičky `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Vytvořte metodu pro vytvoření instance objektu [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) s vaším koncovým bodem a objektem `ApiKeyServiceClientCredentials`, který obsahuje váš klíč.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

V metodě `main()` programu zavolejte metodu ověřování pro vytvoření instance klienta.

## <a name="sentiment-analysis"></a>Analýza subjektivního hodnocení

Vytvořte novou funkci nazvanou `SentimentAnalysisExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci [mínění ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) . Vrácený objekt [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) bude obsahovat mínění `Score` v případě úspěchu a `errorMessage`, pokud ne. 

Skóre, které je blízko 0, označuje negativní mínění, zatímco skóre, které je blíže 1, značí kladné mínění.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

### <a name="output"></a>Výstup

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Detekce jazyka

Vytvořte novou funkci nazvanou `languageDetectionExample()`, která převezme klienta, kterého jste vytvořili dříve, a zavolejte jeho funkci [operaci DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Vrácený objekt [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) bude obsahovat seznam zjištěných jazyků v `DetectedLanguages` v případě úspěchu a `errorMessage`, pokud ne.  Tisk prvního vráceného jazyka.

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí parametru `countryHint` můžete zadat kód země se dvěma písmeny. Ve výchozím nastavení používá rozhraní API "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Výstup

```console
Language: English
```

## <a name="entity-recognition"></a>Rozpoznávání entit

Vytvořte novou funkci nazvanou `RecognizeEntitiesExample()`, která převezme klienta, který jste vytvořili dříve, a zavolejte jeho funkce [Entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Iterujte výsledky. Vrácený objekt [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) bude obsahovat seznam zjištěných entit v `Entities` v případě úspěchu a `errorMessage`, pokud ne. Pro každou zjištěnou entitu vytiskněte svůj typ, dílčí typ, Wikipedii název (pokud existují) a také umístění v původním textu.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]


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

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Vytvořte novou funkci nazvanou `KeyPhraseExtractionExample()`, která převezme klienta, kterého jste vytvořili dříve a zavolejte [jeho funkci.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) Výsledek bude obsahovat seznam zjištěných klíčových frází v `KeyPhrases` v případě úspěchu a `errorMessage`, pokud ne. Vytiskněte všechny zjištěné klíčové fráze.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Výstup

```console
Key phrases:
    cat
    veterinarian
```
