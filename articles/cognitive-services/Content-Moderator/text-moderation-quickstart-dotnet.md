---
title: 'Rychlý Start: analýza textového obsahu C# v Content moderator'
titleSuffix: Azure Cognitive Services
description: Jak analyzovat textový obsah pro různé nevhodný materiál pomocí sady Content Moderator SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: 69c190b9ce9e5a99d39a46c147f7159ace47e4f7
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001441"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Rychlý Start: analýza textového obsahu pro nevhodný materiál vC#

V tomto článku najdete informace a ukázky kódu, které vám pomůžou začít používat [sadu Content moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Naučíte se, jak provádět filtrování založené na termínech a klasifikaci textových obsahu s cílem moderování potenciálně nežádoucího materiálu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky
- Content Moderator klíč předplatného. Podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) se přihlaste k odběru Content moderator a Získejte svůj klíč.
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> Tato příručka používá Content Moderator předplatné na bezplatné úrovni. Informace o tom, co je k dispozici u jednotlivých úrovní předplatného, najdete na stránce [ceny a omezení](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/) .

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. V aplikaci Visual Studio vytvořte nový projekt **konzolové aplikace (.NET Framework)** a pojmenujte ho **TextModeration**. 
1. Pokud máte ve svém řešení jiné projekty, vyberte je jako jeden spouštěný projekt.
1. Získejte požadovaný balíček NuGet. V Průzkumník řešení klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Pak vyhledejte a nainstalujte **balíček `Microsoft.Azure.CognitiveServices.ContentModerator`** . Alternativně můžete spustit následující příkaz z adresáře řešení:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator
```

## <a name="add-text-moderation-code"></a>Přidat kód pro moderování textu

V dalším kroku zkopírujete a vložíte kód z tohoto průvodce do projektu, abyste implementovali základní scénář Moderování obsahu.

### <a name="include-namespaces"></a>Zahrnout obory názvů

Do horní části souboru *program.cs* přidejte následující příkazy `using`.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Vytvoření klienta Content Moderator

Přidejte do souboru *program.cs* následující kód pro vytvoření poskytovatele klienta Content moderator pro vaše předplatné. Přidejte kód spolu se třídou **program** ve stejném oboru názvů. Pole **a** a **CMSubscriptionKey** budete muset aktualizovat hodnotami identifikátoru vaší oblasti a klíče předplatného.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>Nastavit cíle vstupu a výstupu

Do třídy **program** v _program.cs_přidejte následující statická pole. Tato pole určují soubory pro vstupní textový obsah a výstupní obsah JSON.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

Budete muset vytvořit vstupní soubor *textfile. txt* a aktualizovat jeho cestu (cesty jsou relativní vzhledem k adresáři spuštění). Otevřete _textfile. txt_ a přidejte text do středníku. V tomto rychlém startu se používá následující vzorový text:

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>Načíst vstupní text

Do metody **Main** přidejte následující kód. Metoda **ScreenText** je základní operace. Jeho parametry určují, které operace Moderování obsahu budou provedeny. V tomto příkladu je metoda nakonfigurovaná na:
- Detekuje potenciální vulgární text.
- Normalizuje text a překlepy v automatických opravách.
- Zjišťují se osobní údaje, jako jsou telefonní čísla USA a UK, e-mailové adresy a poštovní adresy USA.
- Použijte modely založené na strojovém učení pro klasifikaci textu do tří kategorií.

Pokud se chcete dozvědět více o tom, co tyto operace provede, postupujte podle odkazu v části [Další kroky](#next-steps) .

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>Spustit program

Program zapíše data řetězce JSON do souboru _TextModerationOutput. txt_ . Vzorový text použitý v tomto rychlém startu nabízí následující výstup:

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

V tomto rychlém startu jste vytvořili jednoduchou aplikaci .NET, která používá službu Content Moderator k vrácení relevantních informací o dané ukázce textu. Dále si přečtěte další informace o tom, co jednotlivé příznaky a klasifikace znamenají, abyste se mohli rozhodnout, jaká data potřebujete a jak ji by měla vaše aplikace zpracovat.

> [!div class="nextstepaction"]
> [Průvodce moderováním textu](text-moderation-api.md)
