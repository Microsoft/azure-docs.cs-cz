---
title: 'Rychlý Start: analýza textového obsahu C# v Content moderator'
titleSuffix: Azure Cognitive Services
description: Jak analyzovat textový obsah pro různé nevhodný materiál pomocí sady Content Moderator SDK pro .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 51202597aaee2940a764d0385d4d93c7409f4905
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935928"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Rychlý start: Analýza problematického materiálu v obsahu textu v jazyce C#

Tento článek obsahuje informace a vzorové kódy, které vám pomůžou začít používat [sadu Content Moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Naučíte se, jak provádět filtrování založené na termínech a klasifikaci textových obsahu s cílem moderování potenciálně nežádoucího materiálu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Předpoklady
- Klíč předplatného Content Moderatoru. Postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru Content moderator. Pak [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč a adresu URL koncového bodu s názvem `CONTENT_MODERATOR_SUBSCRIPTION_KEY` a `CONTENT_MODERATOR_ENDPOINT`, v uvedeném pořadí.
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> V této příručce se používá předplatné Content Moderatoru úrovně Free. Informace o tom, co je součástí jednotlivých úrovní předplatného, najdete na stránce [Ceny a omezení](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. V sadě Visual Studio vytvořte nový projekt **Konzolová aplikace (.NET Framework)** a pojmenujte ho **TextModeration**. 
1. Pokud vaše řešení obsahuje i jiné projekty, vyberte tento projekt jako jediný spouštěný projekt.
1. Získejte požadovaný balíček NuGet. V Průzkumník řešení klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Pak vyhledejte a nainstalujte **balíček `Microsoft.Azure.CognitiveServices.ContentModerator`** . Alternativně můžete spustit následující příkaz z adresáře řešení:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator
```

## <a name="add-text-moderation-code"></a>Přidání kódu pro moderování textu

Dále zkopírováním kódu z této příručky a jeho vložením do svého projektu implementujete základní scénář moderování obsahu.

### <a name="include-namespaces"></a>Zahrnutí oborů názvů

Na začátek souboru *Program.cs* přidejte následující příkazy `using`.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_using)]

### <a name="create-the-content-moderator-client"></a>Vytvoření klienta Content Moderatoru

Do souboru *Program.cs* přidejte následující kód, který pro vaše předplatné vytvoří zprostředkovatele klienta Content Moderatoru. Přidejte třídu spolu se třídou **program** ve stejném oboru názvů. Pole **AzureBaseURL** a **CMSubscriptionKey** budete muset aktualizovat hodnotami adresy URL koncového bodu a klíče předplatného. Můžete je najít na kartě **rychlý Start** prostředku v Azure Portal.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_client)]

### <a name="set-up-input-and-output-targets"></a>Nastavení vstupních a výstupních cílů

Do třídy **Program** v souboru _Program.cs_ přidejte následující statická pole. Tato pole určují soubory pro vstupní textový obsah a výstupní obsah JSON.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_fields)]

Budete muset vytvořit vstupní soubor *textfile. txt* a aktualizovat jeho cestu (cesty jsou relativní vzhledem k adresáři spuštění). Otevřete soubor _TextFile.txt_ a přidejte text, který se má moderovat. V tomto rychlém startu se používá následující ukázkový text:

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>Načtení vstupního textu

Do metody **Main** přidejte následující kód. Zásadní operací je metoda **ScreenText**. Její parametry určují, které operace moderování obsahu se provedou. V tomto příkladu je metoda nakonfigurovaná tak, aby prováděla následující:
- Rozpoznávání potenciálně vulgárních výrazů v textu
- Normalizace textu a automatické opravy překlepů
- Zjišťují se osobní údaje, jako jsou telefonní čísla USA a UK, e-mailové adresy a poštovní adresy USA.
- Klasifikace textu do tří kategorií s využitím modelů založených na strojovém učení

Další informace o tom, co tyto operace dělají, najdete na odkazu v části [Další kroky](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_main)]

## <a name="run-the-program"></a>Spuštění programu

Program zapíše řetězcová data JSON do souboru _TextModerationOutput.txt_. Pro ukázkový text použitý v tomto rychlém startu bude výstup následující:

```json
Autocorrect typos, check for matching terms, check for personal data, and classify.
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
