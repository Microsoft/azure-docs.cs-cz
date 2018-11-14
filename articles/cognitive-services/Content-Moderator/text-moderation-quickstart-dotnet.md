---
title: 'Rychlý start: Analýza problematického materiálu v obsahu textu v jazyce C#'
titlesuffix: Azure Cognitive Services
description: Postup analýzy různého problematického materiálu v obsahu textu pomocí sady Content Moderator SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: sajagtap
ms.openlocfilehash: 0540a81db93570928dd33b66a69b6883b2df0cd9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007684"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Rychlý start: Analýza problematického materiálu v obsahu textu v jazyce C# 

Tento článek obsahuje informace a vzorové kódy, které vám pomůžou začít používat [sadu Content Moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Dozvíte se, jak provádět filtrování založené na termínech a klasifikaci obsahu textu za účelem moderování potenciálně problematického materiálu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky
- Klíč předplatného Content Moderatoru. Podle pokynů v tématu [Vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) se přihlaste k odběru Content Moderatoru a získejte svůj klíč.
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> V této příručce se používá předplatné Content Moderatoru úrovně Free. Informace o tom, co je součástí jednotlivých úrovní předplatného, najdete na stránce [Ceny a omezení](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. V sadě Visual Studio vytvořte nový projekt **Konzolová aplikace (.NET Framework)** a pojmenujte ho **TextModeration**. 
1. Pokud vaše řešení obsahuje i jiné projekty, vyberte tento projekt jako jediný spouštěný projekt.
1. Získejte požadované balíčky NuGet. Klikněte pravým tlačítkem na svůj projekt v Průzkumníku řešení a vyberte **Spravovat balíčky NuGet**. Potom vyhledejte a nainstalujte následující balíčky:
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-text-moderation-code"></a>Přidání kódu pro moderování textu

Dále zkopírováním kódu z této příručky a jeho vložením do svého projektu implementujete základní scénář moderování obsahu.

### <a name="include-namespaces"></a>Zahrnutí oborů názvů

Na začátek souboru *Program.cs* přidejte následující příkazy `using`.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Vytvoření klienta Content Moderatoru

Do souboru *Program.cs* přidejte následující kód, který pro vaše předplatné vytvoří zprostředkovatele klienta Content Moderatoru. Přidejte kód společně s třídou **Program** do stejného oboru názvů. Budete muset aktualizovat pole **AzureRegion** a **CMSubscriptionKey** hodnotami identifikátoru oblasti a klíče předplatného.

```csharp
// Wraps the creation and configuration of a Content Moderator client.
public static class Clients
{
    // The region/location for your Content Moderator account, 
    // for example, westus.
    private static readonly string AzureRegion = "YOUR API REGION";

    // The base URL fragment for Content Moderator calls.
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    // Your Content Moderator subscription key.
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    // Returns a new Content Moderator client for your subscription.
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

### <a name="set-up-input-and-output-targets"></a>Nastavení vstupních a výstupních cílů

Do třídy **Program** v souboru _Program.cs_ přidejte následující statická pole. Tato pole určují soubory pro vstupní textový obsah a výstupní obsah JSON.

```csharp
// The name of the file that contains the text to evaluate.
private static string TextFile = "TextFile.txt";

// The name of the file to contain the output from the evaluation.
private static string OutputFile = "TextModerationOutput.txt";
```

Budete muset vytvořit vstupní soubor *TextFile.txt* a odpovídajícím způsobem aktualizovat jeho cestu (relativní cesty jsou relativní vzhledem k adresáři provádění). Otevřete soubor _TextFile.txt_ a přidejte text, který se má moderovat. V tomto rychlém startu se používá následující ukázkový text:

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>Načtení vstupního textu

Do metody **Main** přidejte následující kód. Zásadní operací je metoda **ScreenText**. Její parametry určují, které operace moderování obsahu se provedou. V tomto příkladu je metoda nakonfigurovaná tak, aby prováděla následující:
- Rozpoznávání potenciálně vulgárních výrazů v textu
- Normalizace textu a automatické opravy překlepů
- Rozpoznávání identifikovatelných osobních údajů (PII), jako jsou telefonní čísla USA a Velké Británie, e-mailové adresy a poštovní adresy v USA
- Klasifikace textu do tří kategorií s využitím modelů založených na strojovém učení

Další informace o tom, co tyto operace dělají, najdete na odkazu v části [Další kroky](#next-steps).

```csharp
// Load the input text.
string text = File.ReadAllText(TextFile);
Console.WriteLine("Screening {0}", TextFile);

text = text.Replace(System.Environment.NewLine, " ");
byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(text);
MemoryStream stream = new MemoryStream(byteArray);

// Save the moderation results to a file.
using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
{
    // Create a Content Moderator client and evaluate the text.
    using (var client = Clients.NewClient())
    {
        // Screen the input text: check for profanity,
        // autocorrect text, check for personally identifying
        // information (PII), and classify the text into three categories
        outputWriter.WriteLine("Autocorrect typos, check for matching terms, PII, and classify.");
        var screenResult =
        client.TextModeration.ScreenText("text/plain", stream, "eng", true, true, null, true);
        outputWriter.WriteLine(
                JsonConvert.SerializeObject(screenResult, Formatting.Indented));
    }
    outputWriter.Flush();
    outputWriter.Close();
}
```

## <a name="run-the-program"></a>Spuštění programu

Program zapíše řetězcová data JSON do souboru _TextModerationOutput.txt_. Pro ukázkový text použitý v tomto rychlém startu bude výstup následující:

```json
Autocorrect typos, check for matching terms, PII, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili jednoduchou aplikaci v .NET, která s využitím služby Content Moderator vrací relevantní informace o poskytnutém ukázkovém textu. Dále si můžete přečíst další informace o tom, co znamenají různé příznaky a klasifikace, abyste se mohli rozhodnout, jaká data potřebujete a jak by je vaše aplikace měla zpracovávat.

> [!div class="nextstepaction"]
> [Příručka moderování textu](text-moderation-api.md)
