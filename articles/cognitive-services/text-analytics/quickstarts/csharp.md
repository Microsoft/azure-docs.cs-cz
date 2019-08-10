---
title: 'Rychlý start: Klientská knihovna Analýza textu pro .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít používat rozhraní API pro analýzu textu z Azure Cognitive Services.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: assafi
ms.openlocfilehash: 4373cd8da8d302722c5edbe3ee716eec96e6419f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881053"
---
# <a name="quickstart-text-analytics-client-library-for-net"></a>Rychlý start: Klientská knihovna pro analýzu textu pro .NET
<a name="HOLTop"></a>

Začněte s klientskou knihovnou Analýza textu pro .NET. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. 

Pomocí klientské knihovny Analýza textu pro Python proveďte následující:

* Analýza mínění
* Rozpoznávání jazyka
* Rozpoznávání entit
* Extrakce klíčových frází

[Ukázky dokumentace k](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | balíčku[zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | knihovny Referenční dokumentace[](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | 

> [!NOTE] 
> Ukázkový kód v tomto článku používá pro jednoduchost synchronní metody Analýza textu .NET SDK. U produkčních scénářů ale doporučujeme používat ve svých vlastních aplikacích dávkové asynchronní metody, které zajistí jejich škálovatelnost a reakce. Například volání [SentimentBatchAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) namísto [mínění ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-text-analytics-azure-resource"></a>Vytvoření prostředku Azure Analýza textu

Získejte klíč k ověřování vašich aplikací pomocí Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Analýza textu pomocí [Azure Portal](../../cognitive-services-apis-create-account.md) nebo rozhraní příkazového [řádku Azure](../../cognitive-services-apis-create-account-cli.md) na vašem místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředku na [Azure Portal](https://portal.azure.com/)

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte pro tento klíč [proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) s názvem `TEXT_ANALYTICS_SUBSCRIPTION_KEY`.

### <a name="create-a-new-c-application"></a>Vytvoření nové C# aplikace

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE). 

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem. `text-analytics quickstart` Tento příkaz vytvoří jednoduchý projekt "Hello World" C# s jedním zdrojovým souborem: *program.cs*. 

```console
dotnet new console -n text-analytics-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```console
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

V adresáři projektu otevřete soubor *program.cs* v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující `using` direktivy:

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

V `Main` metodě aplikace vytvořte proměnné pro koncový bod a klíč Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné. Metody budete definovat později.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```csharp
static void Main(string[] args)
{
    // replace this endpoint with the correct one for your Azure resource. 
    string endpoint = $"https://westus2.api.cognitive.microsoft.com";
    //This sample assumes you have created an environment variable for your key
    string key = Environment.GetEnvironmentVariable("TEXT_ANALYTICS_SUBSCRIPTION_KEY");
    ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials(key));
    Console.OutputEncoding = System.Text.Encoding.UTF8;
    SentimentAnalysisExample(client);
    // languageDetectionExample(client);
    // RecognizeEntitiesExample(client);
    // KeyPhraseExtractionExample(client);
    Console.ReadLine();
}
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte Analýza textu klientskou knihovnu pro .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.TextAnalytics --version 4.0.0
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Klient Analýza textu je objekt [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) , který se ověřuje v Azure pomocí vašeho klíče a poskytuje funkce pro příjem textu jako jednoho řetězce nebo jako dávky. Text do rozhraní API můžete synchronně nebo asynchronně posílat. Objekt odpovědi bude obsahovat informace o analýze pro každý dokument, který odešlete. 


## <a name="code-examples"></a>Příklady kódu

* [Ověření klienta](#authenticate-the-client)
* [Analýza subjektivního hodnocení](#sentiment-analysis)
* [Rozpoznávání jazyka](#language-detection)
* [Rozpoznávání entit](#entity-recognition)
* [Extrakce klíčových frází](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte novou `ApiKeyServiceClientCredentials` třídu pro uložení přihlašovacích údajů a přidejte ji do požadavků klienta. V rámci něj Vytvořte přepsání pro `ProcessHttpRequestAsync()` , které přidá klíč `Ocp-Apim-Subscription-Key` do záhlaví.

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

`main()` V metodě vytvořte instanci klienta.

```csharp
ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials(key));
```

## <a name="sentiment-analysis"></a>Analýza mínění

Vytvořte novou funkci s názvem `SentimentAnalysisExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho funkci [mínění ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) . Vrácený objekt [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) bude obsahovat mínění `Score` v případě `errorMessage` úspěchu a v případě potřeby. 

Skóre, které je blízko 0, označuje negativní mínění, zatímco skóre, které je blíže 1, značí kladné mínění.

```csharp
static void SentimentAnalysisExample(ITextAnalyticsClient client){
    var result = client.Sentiment("I had the best day of my life.", "en");
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
}
```

### <a name="output"></a>Výstup

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Rozpoznávání jazyka

Vytvořte novou funkci s názvem `languageDetectionExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho funkci [operaci DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Vrácený objekt [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) bude obsahovat seznam zjištěných jazyků v případě `DetectedLanguages` `errorMessage` úspěchu a v případě potřeby.  Tisk prvního vráceného jazyka.

> [!Tip]
> V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí `countryHint` parametru můžete zadat kód země se dvěma písmeny. Rozhraní API ve výchozím nastavení používá "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""` .

```csharp
static void languageDetectionExample(ITextAnalyticsClient client){
    var result = client.DetectLanguage("This is a document written in English.");
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
}
```
<!--
[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

### <a name="output"></a>Výstup

```console
Language: English
```

## <a name="entity-recognition"></a>Rozpoznávání entit

Vytvořte novou funkci s názvem `RecognizeEntitiesExample()` , která převezme klienta, který jste vytvořili dříve, a zavolejte jeho funkce [Entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Iterujte výsledky. Vrácený objekt [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) bude obsahovat seznam zjištěných entit v případě `Entities` `errorMessage` úspěchu a v případě potřeby. Pro každou zjištěnou entitu vytiskněte svůj typ, dílčí typ, Wikipedii název (pokud existují) a také umístění v původním textu.

```csharp
static void entityRecognitionExample(ITextAnalyticsClient client){

    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities){
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches){
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
}
```
<!--
[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

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

Vytvořte novou funkci s názvem `KeyPhraseExtractionExample()` , která převezme klienta, který jste vytvořili dříve, [](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) a zavolejte jeho funkci. Výsledek bude obsahovat seznam zjištěných klíčových frází v případě `KeyPhrases` úspěchu `errorMessage` a v případě potřeby. Vytiskněte všechny zjištěné klíčové fráze.

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků dojde také k odstranění všech dalších prostředků přidružených ke skupině prostředků.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání entit](../how-tos/text-analytics-how-to-entity-linking.md)
* [Zjistit jazyk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznávání jazyka](../how-tos/text-analytics-how-to-language-detection.md)
