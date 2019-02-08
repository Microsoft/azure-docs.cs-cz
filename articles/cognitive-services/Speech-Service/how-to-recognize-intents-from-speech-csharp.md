---
title: 'Kurz: Rozpoznat záměry z řeči pomocí sadou SDK pro řeč pro jazyk C#'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se naučíte rozpoznávat záměry z řeči pomocí sady Speech SDK pro C#.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 9166ac5bf630b66ae46f356e17a327162f06af2a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867167"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Kurz: Rozpoznat záměry z řeči pomocí sadou SDK pro řeč proC#

[!INCLUDE [Article selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

Sada [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) služeb Cognitive Services se integruje se službou [Language Understanding Service (LUIS)](https://www.luis.ai/home) a poskytuje **rozpoznávání záměrů**. Záměr je něco, co chce uživatel udělat: rezervovat si let, ověřit počasí nebo si zavolat. Uživatel může použít jakákoli slova, která mu připadají přirozená. Služba LUIS mapuje pomocí strojového učení žádosti uživatelů na vámi definované záměry.

> [!NOTE]
> Aplikace LUIS definuje záměry a entity, které chcete rozpoznat. Je oddělená od aplikace C#, která používá službu Speech. V tomto článku budeme aplikaci služby LUIS označovat jako aplikaci LUIS a kód C# jako aplikaci.

V tomto kurzu použijete sadu Speech SDK k vývoji konzolové aplikace C#, která odvozuje záměry z promluv uživatele prostřednictvím mikrofonu zařízení. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvořit projekt sady Visual Studio odkazující na balíček NuGet sady Speech SDK
> * Vytvořit konfiguraci řeči a získat nástroj pro rozpoznávání záměru
> * Získat model pro vaši aplikaci LUIS a přidat potřebné záměry
> * Specifikovat jazyk pro rozpoznávání řeči
> * Rozpoznávat řeč ze souboru
> * Používat asynchronní událostmi řízené průběžné rozpoznávání

## <a name="prerequisites"></a>Požadavky

Než zahájíte tento kurz, musíte mít následující položky.

* Účet služby LUIS. Můžete ho získat zdarma prostřednictvím [portálu LUIS](https://www.luis.ai/home).
* Visual Studio 2017 (libovolná edice).

## <a name="luis-and-speech"></a>Služba LUIS a řeč

Služba LUIS se integruje se službou Speech za účelem rozpoznávání záměrů z řeči. Nepotřebujete předplatné služby Speech, stačí vám služba LUIS.

Služba LUIS používá dva typy klíčů: 

|Typ klíče|Účel|
|--------|-------|
|vytváření obsahu|umožňuje vytvářet a upravovat aplikace LUIS prostřednictvím kódu programu|
|endpoint |autorizuje přístup ke konkrétní aplikaci LUIS|

Klíč koncového bodu je klíč služby LUIS, který potřebujete pro účely tohoto kurzu. Tento kurz používá ukázkovou aplikaci Home Automation služby LUIS, kterou můžete vytvořit pomocí postupu v článku o [použití předem připravené aplikace Home Automation](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app). Pokud jste si vytvořili vlastní aplikaci LUIS, můžete použít ji.

Při vytváření aplikace LUIS se automaticky vygeneruje počáteční klíč, abyste aplikaci mohli otestovat pomocí textových dotazů. Tento klíč neumožňuje integraci služby Speech a v tomto kurzu nebude fungovat. Musíte vytvořit prostředek služby LUIS na řídicím panelu Azure a přiřadit ho aplikaci LUIS. Pro účely tohoto kurzu můžete použít na bezplatnou úroveň předplatného. 

Jakmile prostředek služby LUIS na řídicím panelu Azure vytvoříte, přihlaste se k [portálu LUIS](https://www.luis.ai/home), vyberte svoji aplikaci na stránce My Apps (Moje aplikace), potom přepněte na stránku pro správu této aplikace. Nakonec na bočním panelu klikněte na **Keys and Endpoints** (Klíče a koncové body).

![Nastavení klíčů a koncových bodů portálu LUIS](media/sdk/luis-keys-endpoints-page.png)

Na stránce nastavení klíčů a koncových bodů:

1. Přejděte dolů do části Resources and Keys (Prostředky a klíče) a klikněte na **Assign resource** (Přiřadit prostředek).
1. V dialogovém okně **Assign a key to your app** (Přiřadit klíč aplikaci) vyberte následující položky:

    * Jako Tenanta zvolte Microsoft.
    * V části Subscription Name (Název předplatného) vyberte předplatné Azure, které obsahuje prostředek služby LUIS, který chcete použít.
    * V části Key (Klíč) vyberte prostředek služby LUIS, který chcete s aplikací používat.

Nové předplatné se za chvíli zobrazí v tabulce v dolní části stránky. Kliknutím na ikonu vedle klíče ho zkopírujte do schránky. (Můžete použít kterýkoli z klíčů.)

![Klíče předplatného aplikace LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Vytvoření projektu řeči v sadě Visual Studio

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Přidání kódu

Soubor `Program.cs` otevřete v projektu sady Visual Studio a blok příkazů `using` na začátku souboru nahraďte následujícími deklaracemi.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

Do zadané metody `Main()` přidejte následující kód.

```csharp
RecognizeIntentAsync().Wait();
Console.WriteLine("Please press Enter to continue.");
Console.ReadLine();
```

Vytvořte prázdnou asynchronní metodu `RecognizeIntentAsync()`, jak je znázorněno zde.

```csharp
static async Task RecognizeIntentAsync()
{
}
```

Do textu této nové metody přidejte tento kód.

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

Zástupné symboly v této metodě nahraďte klíčem předplatného služby LUIS, oblastí a ID aplikace následujícím způsobem.

|Zástupný symbol|Nahradit hodnotou|
|-----------|------------|
|`YourLanguageUnderstandingSubscriptionKey`|Klíčem koncového bodu služby LUIS. Jak už bylo zmíněno, musí se jednat o klíč získaný na řídicím panelu Azure, nikoli o počáteční klíč. Najdete ho na stránce Keys and Endpoints (Klíče a koncové body) vaší aplikace (v části Manage (Spravovat)) na [portálu LUIS](https://www.luis.ai/home).|
|`YourLanguageUnderstandingServiceRegion`|Krátkým identifikátorem pro oblast předplatného služby LUIS, například `westus` pro oblast Západní USA. Viz [Oblasti](regions.md).|
|`YourLanguageUnderstandingAppId`|ID aplikace LUIS. Najdete ho na stránce nastavení vaší aplikace na [portálu LUIS](https://www.luis.ai/home).|

Po provedení těchto změn můžete sestavit (Control + Shift + B) a spustit (F5) aplikaci tohoto kurzu. Po zobrazení výzvy zkuste do mikrofonu říct „turn off the lights“ (vypnout světla). Výsledek se zobrazí v okně konzoly.

Následující části obsahují informace o kódu.


## <a name="create-an-intent-recognizer"></a>Vytvoření nástroje pro rozpoznávání záměru

Prvním krokem při rozpoznávání záměrů v řeči je vytvoření konfigurace řeči z klíče koncového bodu a oblasti služby LUIS. Konfigurace řeči můžete použít k vytvoření nástrojů pro rozpoznávání s různými možnostmi sady Speech SDK. Konfigurace řeči zahrnuje několik způsobů, jak specifikovat předplatné, které chcete použít. My použijeme `FromSubscription`, který využije klíč a oblast předplatného.

> [!NOTE]
> Použijte klíč a oblast předplatného služby LUIS, nikoli předplatného služby Speech.

Potom pomocí `new IntentRecognizer(config)` vytvořte nástroj pro rozpoznávání záměru. Vzhledem k tomu, že konfigurace již ví, jaké předplatné použít, není při vytváření nástroje pro rozpoznávání nutné znovu zadávat klíč a koncový bod předplatného.

## <a name="import-a-luis-model-and-add-intents"></a>Import modelu LUIS a přidání záměrů

Teď model z aplikace LUIS naimportujte pomocí `LanguageUnderstandingModel.FromAppId()` a přidejte záměry služby LUIS, které si přejete pomocí metody `AddIntent()` nástroje pro rozpoznávání rozpoznat. Tyto dva kroky vylepší přesnost rozpoznávání řeči indikováním slov, která uživatel ve svých žádostech pravděpodobně použije. Není nutné přidávat všechny záměry aplikace, pokud je ve své aplikaci nepotřebujete rozpoznávat.

Pro přidání záměrů jsou potřeba tři argumenty: model LUIS (který byl právě vytvořen s názvem `model`), název záměru a ID záměru. Rozdíl mezi ID a názvem je následující.

|Argument `AddIntent()`|Účel|
|--------|-------|
|intentName |Název záměru, jak je definovaný v aplikaci LUIS. Musí přesně odpovídat názvu záměru služby LUIS.|
|intentID    |ID přiřazené rozpoznanému záměru sadou Speech SDK. Může být jakékoli. Nemusí odpovídat názvu záměru definovanému v aplikaci LUIS. Pokud třeba stejný kód zpracovává více záměrů, můžete pro ně použít stejné ID.|

Aplikace Home Automation služby LUIS má dva záměry: jeden pro zapnutí zařízení a druhý pro jeho vypnutí. Níže uvedené řádky přidají tyto záměry do nástroje pro rozpoznávání. Tyto tři řádky `AddIntent` v metodě `RecognizeIntentAsync()` nahraďte tímto kódem.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

## <a name="start-recognition"></a>Zahájení rozpoznávání

Když máte vytvořený nástroj pro rozpoznávání a přidali jste záměry, můžete zahájit rozpoznávání. Sada Speech SDK podporuje jednorázové i průběžné rozpoznávání.

|Režim rozpoznávání|Volané metody|Výsledek|
|----------------|-----------------|---------|
|Jednorázový|`RecognizeOnceAsync()`|Vrací rozpoznaný záměr, pokud existuje, po jedné promluvě.|
|Průběžný|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Rozpoznává více promluv. Když jsou výsledky k dispozici, vysílá události (například `IntermediateResultReceived`).|

Aplikace kurzu používá jednorázový režim, a proto pro zahájení rozpoznávání volá metodu `RecognizeOnceAsync()`. Výsledkem je objekt `IntentRecognitionResult`, který obsahuje informace o rozpoznaném záměru. Odpověď služby LUIS ve formátu JSON je extrahována následujícím výrazem:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Aplikace kurzu neanalyzuje výsledek JSON, pouze ho zobrazuje v okně konzoly.

![Výsledky rozpoznávání služby LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Specifikace jazyka rozpoznávání

Ve výchozím nastavení rozpoznává služba LUIS záměry v americké angličtině (`en-us`). Když přiřadíte kód národního prostředí vlastnosti `SpeechRecognitionLanguage` konfigurace řeči, můžete záměry rozpoznávat v jiných jazycích. Přidejte například `config.SpeechRecognitionLanguage = "de-de";` do aplikace našeho kurzu ještě před vytvořením nástroje pro rozpoznávání, abyste záměry mohli rozpoznávat v němčině. Viz [Podporované jazyky](language-support.md#speech-to-text).

## <a name="continuous-recognition-from-a-file"></a>Průběžné rozpoznávání ze souboru

Následující kód představuje další dvě možnosti rozpoznávání záměru pomocí sady Speech SDK. První z nich už jsme zmínili, je to průběžné rozpoznávání, u kterého nástroj pro rozpoznávání vysílá události, když jsou výsledky k dispozici. Tyto události pak mohou být zpracovány vámi zadanými obslužnými rutinami událostí. U průběžného rozpoznávání voláte pro zahájení rozpoznávání metodu `StartContinuousRecognitionAsync()` nástroje pro rozpoznávání, nikoli metodu `RecognizeOnceAsync()`.

Druhou možností je čtení zvuku obsahujícího řeč, která se má zpracovat, ze souboru WAV. Zahrnuje vytvoření zvukové konfigurace, kterou lze použít při vytváření nástroje pro rozpoznávání záměru. Soubor musí být jednokanálový (mono) a musí mít vzorkovací frekvenci 16 kHz.

Pokud si chcete tyto funkce vyzkoušet, nahraďte text metody `RecognizeIntentAsync()` následujícím kódem. 

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Ověřte, že kód obsahuje klíč koncového bodu služby LUIS, oblast a ID aplikace a přidává záměry aplikace Home Automation. `whatstheweatherlike.wav` změňte na název zvukového souboru. Potom proveďte sestavení a spuštění.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Kód z tohoto článku najdete ve složce samples/csharp/sharedcontent/console.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozpoznávání řeči](how-to-recognize-speech-csharp.md)
