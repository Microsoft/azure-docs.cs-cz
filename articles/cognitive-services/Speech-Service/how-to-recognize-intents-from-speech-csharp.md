---
title: 'Jak rozpoznat záměry z řeči pomocí sady Speech SDK C #'
titleSuffix: Azure Cognitive Services
description: V této příručce se dozvíte, jak rozpoznat záměry z řeči pomocí sady Speech SDK pro C#.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.openlocfilehash: 41ebcb7b44ea88af06a30a611960fd8bb0ceddee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402225"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Jak rozpoznat záměry z řeči pomocí sady Speech SDK pro C #

Sada [SDK služby Cognitive](speech-sdk.md) Services pro rozpoznávání řeči se integruje se [službou Language Understanding (LUIS)](https://www.luis.ai/home) a poskytuje **rozpoznávání záměru**. Záměr je něco, co chce uživatel udělat: rezervovat si let, ověřit počasí nebo si zavolat. Uživatel může použít jakákoli slova, která mu připadají přirozená. Pomocí strojového učení služba LUIS mapuje požadavky uživatelů na záměry, které jste definovali.

> [!NOTE]
> Aplikace LUIS definuje záměry a entity, které chcete rozpoznat. Je oddělená od aplikace C#, která používá službu Speech. V tomto článku budeme aplikaci služby LUIS označovat jako aplikaci LUIS a kód C# jako aplikaci.

V této příručce pomocí sady Speech SDK vyvinout konzolu c# aplikace, která odvozuje záměry z projevy uživatelů prostřednictvím mikrofonu vašeho zařízení. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
>
> - Vytvořit projekt sady Visual Studio odkazující na balíček NuGet sady Speech SDK
> - Vytvoření konfigurace řeči a získání nástroje pro rozpoznávání záměru
> - Získat model pro vaši aplikaci LUIS a přidat potřebné záměry
> - Specifikovat jazyk pro rozpoznávání řeči
> - Rozpoznávat řeč ze souboru
> - Používat asynchronní událostmi řízené průběžné rozpoznávání

## <a name="prerequisites"></a>Požadavky

Než začnete s touto příručkou, ujistěte se, že máte následující položky:

- Účet služby LUIS. Můžete ho získat zdarma prostřednictvím [portálu LUIS](https://www.luis.ai/home).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (libovolné vydání).

## <a name="luis-and-speech"></a>Služba LUIS a řeč

Služba LUIS se integruje se službou Speech za účelem rozpoznávání záměrů z řeči. Nepotřebujete předplatné služby Speech, stačí vám služba LUIS.

Služba LUIS používá tři druhy klíčů:

| Typ klíče  | Účel                                               |
| --------- | ----------------------------------------------------- |
| Vytváření obsahu | Umožňuje vytvářet a upravovat aplikace LUIS programově. |
| Starter   | Umožňuje otestovat aplikaci LUIS pouze pomocí textu.   |
| Koncový bod  | Autorizuje přístup k určité aplikaci LUIS.            |

Pro tuto příručku potřebujete typ klíče koncového bodu. Tato příručka používá příklad domácí automatizace LUIS aplikace, kterou můžete vytvořit podle [použití předem domácí automatizace aplikace](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) rychlý start. Pokud jste vytvořili vlastní aplikaci LUIS, můžete ji místo toho použít.

Když vytvoříte aplikaci LUIS, LUIS automaticky vygeneruje počáteční klíč, takže můžete aplikaci otestovat pomocí textových dotazů. Tento klíč neumožňuje integraci služby Rozpoznávání řeči a nebude fungovat s touto příručkou. Vytvořte prostředek LUIS na řídicím panelu Azure a přiřaďte ho k aplikaci LUIS. Pro tuto příručku můžete použít bezplatnou úroveň předplatného.

Po vytvoření prostředku LUIS na řídicím panelu Azure se přihlaste k [portálu LUIS](https://www.luis.ai/home), zvolte aplikaci na stránce **Moje aplikace a** přepněte na stránku **Správa** aplikace. Nakonec vyberte **klíče a koncové body** v postranním panelu.

![Nastavení klíčů a koncových bodů portálu LUIS](media/sdk/luis-keys-endpoints-page.png)

Na stránce **Nastavení kláves a koncových bodů:**

1. Posuňte se dolů do části **Zdroje a klíče a** vyberte Přiřadit **prostředek**.
1. V **dialogovém okně Přiřadit klíč k aplikaci proveďte** následující změny:

   - V části **Tenant**zvolte **Microsoft**.
   - V **části Název předplatného**zvolte předplatné Azure, které obsahuje prostředek LUIS, který chcete použít.
   - V části **Klíč**vyberte prostředek LUIS, který chcete s aplikací použít.

   Nové předplatné se za chvíli zobrazí v tabulce v dolní části stránky.

1. Vyberte ikonu vedle klávesy, chcete-li ji zkopírovat do schránky. (Můžete použít kterýkoli z klíčů.)

![Klíče předplatného aplikace LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Vytvoření projektu řeči v sadě Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Přidání kódu

Dále přidáte kód do projektu.

1. V **Průzkumníku řešení**otevřete soubor **Program.cs**.

1. Nahraďte `using` blok příkazů na začátku souboru následujícími prohlášeními:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Nahraďte `Main()` poskytnutou metodu následujícím asynchronním ekvivalentem:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Vytvořte prázdnou asynchronní metodu `RecognizeIntentAsync()`, jak je znázorněno zde:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. V textu této nové metody přidejte tento kód:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Zástupné symboly v této metodě nahraďte klíčem předplatného služby LUIS, oblastí a ID aplikace následujícím způsobem.

   | Zástupný symbol | Nahradit hodnotou |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | Klíčem koncového bodu služby LUIS. Znovu musíte získat tuto položku z řídicího panelu Azure, nikoli "počáteční klíč." Najdete ji na stránce **Klíče a koncové body** aplikace (v části **Správa)** na [portálu LUIS](https://www.luis.ai/home). |
   | `YourLanguageUnderstandingServiceRegion` | Krátkým identifikátorem pro oblast předplatného služby LUIS, například `westus` pro oblast USA – západ. Viz [Oblasti](regions.md). |
   | `YourLanguageUnderstandingAppId` | ID aplikace LUIS. Najdete ji na stránce **Nastavení** aplikace na [portálu LUIS](https://www.luis.ai/home). |

S těmito provedenými změnami můžete vytvořit **(Ctrl+Shift+B)** a spustit **(F5)** aplikaci. Až se zobrazí výzva, zkuste do mikrofonu počítače vyslovit "Zhasnout světla". Aplikace zobrazí výsledek v okně konzoly.

Následující části obsahují informace o kódu.

## <a name="create-an-intent-recognizer"></a>Vytvoření nástroje pro rozpoznávání záměru

Nejprve je třeba vytvořit konfiguraci řeči z klíče koncového bodu LUIS a oblasti. Konfigurace řeči můžete použít k vytvoření rozhodců pro různé možnosti sady Speech SDK. Konfigurace řeči má několik způsobů, jak určit odběr, který chcete použít; zde používáme `FromSubscription`, který má klíč předplatného a oblast.

> [!NOTE]
> Použijte klíč a oblast vašeho předplatného LUIS, ne předplatné služby Rozpoznávání řeči.

Potom pomocí `new IntentRecognizer(config)` vytvořte nástroj pro rozpoznávání záměru. Vzhledem k tomu, že konfigurace již ví, které předplatné použít, není nutné zadat klíč předplatného a koncový bod znovu při vytváření nástroje pro rozpoznávání.

## <a name="import-a-luis-model-and-add-intents"></a>Import modelu LUIS a přidání záměrů

Teď model z aplikace LUIS naimportujte pomocí `LanguageUnderstandingModel.FromAppId()` a přidejte záměry služby LUIS, které si přejete pomocí metody `AddIntent()` nástroje pro rozpoznávání rozpoznat. Tyto dva kroky vylepší přesnost rozpoznávání řeči indikováním slov, která uživatel ve svých žádostech pravděpodobně použije. Není nutné přidat všechny záměry aplikace, pokud nepotřebujete rozpoznat všechny ve vaší aplikaci.

Chcete-li přidat záměry, musíte zadat tři argumenty: model LUIS `model`(který byl vytvořen a je pojmenován ), název záměru a ID záměru. Rozdíl mezi ID a názvem je následující.

| `AddIntent()`&nbsp;Argument | Účel |
| --------------------------- | ------- |
| `intentName` | Název záměru, jak je definovaný v aplikaci LUIS. Tato hodnota musí přesně odpovídat názvu záměru LUIS. |
| `intentID` | ID přiřazené rozpoznanému záměru sadou Speech SDK. Tato hodnota může být, co se vám líbí; nemusí odpovídat názvu záměru definovanému v aplikaci LUIS. Pokud třeba stejný kód zpracovává více záměrů, můžete pro ně použít stejné ID. |

Aplikace DOMÁCÍ automatizace LUIS má dva záměry: jeden pro zapnutí zařízení a druhý pro vypnutí zařízení. Níže uvedené řádky přidají tyto záměry do nástroje pro rozpoznávání. Tyto tři řádky `AddIntent` v metodě `RecognizeIntentAsync()` nahraďte tímto kódem.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Namísto přidání jednotlivých záměrů můžete `AddAllIntents` také použít metodu k přidání všech záměrů v modelu do nástroje pro rozpoznávání.

## <a name="start-recognition"></a>Zahájení rozpoznávání

Když máte vytvořený nástroj pro rozpoznávání a přidali jste záměry, můžete zahájit rozpoznávání. Sada Speech SDK podporuje jednorázové i průběžné rozpoznávání.

| Režim rozpoznávání | Volané metody | Výsledek |
| ---------------- | --------------- | ------ |
| Jednorázový | `RecognizeOnceAsync()` | Vrací rozpoznaný záměr, pokud existuje, po jedné promluvě. |
| Průběžný | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Rozpozná více projevy; vydává události `IntermediateResultReceived`(například), jsou-li k dispozici výsledky. |

Aplikace používá režim jednoho výstřelu, a tak volá `RecognizeOnceAsync()` k zahájení rozpoznávání. Výsledkem je objekt `IntentRecognitionResult`, který obsahuje informace o rozpoznaném záměru. Odpověď LUIS JSON extrahujete pomocí následujícího výrazu:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Aplikace není analyzovat výsledek JSON. Zobrazí pouze text JSON v okně konzoly.

![Výsledky rozpoznávání jednotné služby LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Specifikace jazyka rozpoznávání

Ve výchozím nastavení rozpoznává služba LUIS záměry v americké angličtině (`en-us`). Když přiřadíte kód národního prostředí vlastnosti `SpeechRecognitionLanguage` konfigurace řeči, můžete záměry rozpoznávat v jiných jazycích. Například přidejte `config.SpeechRecognitionLanguage = "de-de";` do naší aplikace před vytvořením nástroje pro rozpoznávání rozpoznat záměry v němčině. Další informace naleznete v tématu [podpora jazyka LUIS](../LUIS/luis-language-support.md#languages-supported).

## <a name="continuous-recognition-from-a-file"></a>Průběžné rozpoznávání ze souboru

Následující kód představuje další dvě možnosti rozpoznávání záměru pomocí sady Speech SDK. První z nich už jsme zmínili, je to průběžné rozpoznávání, u kterého nástroj pro rozpoznávání vysílá události, když jsou výsledky k dispozici. Tyto události pak mohou být zpracovány vámi zadanými obslužnými rutinami událostí. Při nepřetržitém rozpoznávání zavoláte `StartContinuousRecognitionAsync()` metodu nástroje pro `RecognizeOnceAsync()`rozpoznávání tak, aby začala rozpoznávání místo .

Druhou možností je čtení zvuku obsahujícího řeč, která se má zpracovat, ze souboru WAV. Implementace zahrnuje vytvoření konfigurace zvuku, který lze použít při vytváření nástroje pro rozpoznávání záměru. Soubor musí být jednokanálový (mono) a musí mít vzorkovací frekvenci 16 kHz.

Chcete-li vyzkoušet tyto funkce, odstraňte `RecognizeIntentAsync()` nebo zakomentujte tělo metody a přidejte na její místo následující kód.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Ověřte, že kód obsahuje klíč koncového bodu služby LUIS, oblast a ID aplikace a přidává záměry aplikace Home Automation. Změňte `whatstheweatherlike.wav` název nahraného zvukového souboru. Potom sestavte, zkopírujte zvukový soubor do adresáře sestavení a spusťte aplikaci.

Pokud například v nahraném zvukovém souboru řeknete "Zhasněte světla", pozastavíte a pak řeknete "Zapněte světla", může se zobrazit výstup konzoly podobný následujícímu:

![Výsledky rozpoznávání služby LUIS zvukového souboru](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte kód z tohoto článku ve složce **samples/csharp/sharedcontent/console.**

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Úvodní příručka: Rozpoznání řeči z mikrofonu](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)
