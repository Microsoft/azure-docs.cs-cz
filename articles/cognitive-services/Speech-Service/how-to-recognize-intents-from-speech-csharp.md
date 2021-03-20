---
title: 'Jak rozpoznávat záměry z rozpoznávání řeči pomocí sady Speech SDK C #'
titleSuffix: Azure Cognitive Services
description: V této příručce se dozvíte, jak rozpoznávat záměry pomocí sady Speech SDK pro jazyk C#.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 93a3adf00203e317be912e3e72de7a3f7ca666c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001084"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Jak rozpoznávat záměry z rozpoznávání řeči pomocí sady Speech SDK pro jazyk C #

Sada Cognitive Services [Speech SDK](speech-sdk.md) se integruje se [službou Language Understanding (Luis)](https://www.luis.ai/home) , která poskytuje **rozpoznávání záměrů**. Záměr je něco, co chce uživatel udělat: rezervovat si let, ověřit počasí nebo si zavolat. Uživatel může použít jakákoli slova, která mu připadají přirozená. Pomocí strojového učení LUIS mapuje požadavky uživatelů na vámi definované záměry.

> [!NOTE]
> Aplikace LUIS definuje záměry a entity, které chcete rozpoznat. Je oddělená od aplikace C#, která používá službu Speech. V tomto článku budeme aplikaci služby LUIS označovat jako aplikaci LUIS a kód C# jako aplikaci.

V této příručce použijete sadu Speech SDK k vývoji konzolové aplikace v jazyce C#, která odvozuje záměry od uživatelů projevy prostřednictvím mikrofonu vašeho zařízení. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
>
> - Vytvořit projekt sady Visual Studio odkazující na balíček NuGet sady Speech SDK
> - Vytvoření konfigurace řeči a získání nástroje pro rozpoznávání záměrů
> - Získat model pro vaši aplikaci LUIS a přidat potřebné záměry
> - Specifikovat jazyk pro rozpoznávání řeči
> - Rozpoznávat řeč ze souboru
> - Používat asynchronní událostmi řízené průběžné rozpoznávání

## <a name="prerequisites"></a>Předpoklady

Než začnete s touto příručkou, ujistěte se, že máte následující položky:

- Účet služby LUIS. Můžete ho získat zdarma prostřednictvím [portálu LUIS](https://www.luis.ai/home).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (libovolná edice).

## <a name="luis-and-speech"></a>Služba LUIS a řeč

Služba LUIS se integruje se službou Speech za účelem rozpoznávání záměrů z řeči. Nepotřebujete předplatné služby Speech, stačí vám služba LUIS.

LUIS používá tři typy klíčů:

| Typ klíče  | Účel                                               |
| --------- | ----------------------------------------------------- |
| Vytváření obsahu | Umožňuje vytvářet a upravovat aplikace LUIS programově. |
| Starter   | Umožňuje testovat aplikace LUIS jenom pomocí textu.   |
| Koncový bod  | Autorizuje přístup ke konkrétní aplikaci LUIS.            |

V této příručce budete potřebovat typ klíče koncového bodu. Tato příručka používá ukázkovou aplikaci LUIS pro automatizaci, kterou můžete vytvořit pomocí [předpřipraveného rychlého startu aplikace pro domácí automatizaci](../luis/luis-get-started-create-app.md) . Pokud jste vytvořili vlastní aplikaci LUIS, můžete ji místo toho použít.

Když vytvoříte aplikaci LUIS, LUIS automaticky vygeneruje počáteční klíč, abyste mohli otestovat aplikaci pomocí textových dotazů. Tento klíč nepovoluje integraci služby Speech Service a nebude s tímto průvodcem fungovat. Vytvořte prostředek LUIS na řídicím panelu Azure a přiřaďte ho do aplikace LUIS. Pro tuto příručku můžete použít bezplatnou úroveň předplatného.

Po vytvoření prostředku LUIS na řídicím panelu Azure se přihlaste k [portálu Luis](https://www.luis.ai/home), vyberte svou aplikaci na stránce **Moje aplikace** a pak přepněte na stránku **správy** aplikace. Nakonec na bočním panelu vyberte **klíče a koncové body** .

![Nastavení klíčů a koncových bodů portálu LUIS](media/sdk/luis-keys-endpoints-page.png)

Na stránce **klíče a nastavení koncového bodu** :

1. Přejděte dolů k části **prostředky a klíče** a vyberte **přiřadit prostředek**.
1. V dialogovém okně **přiřadit klíč k aplikaci** proveďte následující změny:

   - V části **tenant** klikněte na **Microsoft**.
   - V části **název předplatného** vyberte předplatné Azure, které obsahuje prostředek Luis, který chcete použít.
   - V části **klíč** vyberte prostředek Luis, který chcete s aplikací používat.

   Nové předplatné se za chvíli zobrazí v tabulce v dolní části stránky.

1. Vyberte ikonu vedle klíče a zkopírujte ji do schránky. (Můžete použít kterýkoli z klíčů.)

![Klíče předplatného aplikace LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Vytvoření projektu řeči v sadě Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Přidání kódu

Dále přidáte kód do projektu.

1. Z **Průzkumník řešení** otevřete soubor **program. cs**.

1. Nahraďte blok `using` příkazů na začátku souboru následujícími deklaracemi:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Nahraďte poskytnutou `Main()` metodu následujícím asynchronním ekvivalentem:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Vytvořte prázdnou asynchronní metodu `RecognizeIntentAsync()` , jak je znázorněno zde:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. V těle této nové metody přidejte tento kód:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Zástupné symboly v této metodě nahraďte klíčem předplatného služby LUIS, oblastí a ID aplikace následujícím způsobem.

   | Zástupný symbol | Nahradit hodnotou |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | Klíčem koncového bodu služby LUIS. Znovu musíte získat tuto položku z řídicího panelu Azure, ne pomocí spouštěcího klíče. Můžete ji najít na stránce **s klíči a koncovými body** vaší aplikace (v části **Spravovat**) na [portálu Luis](https://www.luis.ai/home). |
   | `YourLanguageUnderstandingServiceRegion` | Krátkým identifikátorem pro oblast předplatného služby LUIS, například `westus` pro oblast USA – západ. Viz [Oblasti](regions.md). |
   | `YourLanguageUnderstandingAppId` | ID aplikace LUIS. Můžete ji najít na stránce **Nastavení** vaší aplikace na [portálu Luis](https://www.luis.ai/home). |

Pomocí těchto změn můžete sestavit (**Control + Shift + B**) a spustit aplikaci (**F5**). Po zobrazení výzvy se pokuste "vypnout světla" na mikrofon počítače. Aplikace zobrazí výsledek v okně konzoly.

Následující části obsahují informace o kódu.

## <a name="create-an-intent-recognizer"></a>Vytvoření nástroje pro rozpoznávání záměru

Nejdřív je potřeba vytvořit konfiguraci řeči z klíče a oblasti koncového bodu LUIS. Pomocí konfigurací pro rozpoznávání řeči můžete vytvářet nástroje pro rozpoznávání různých funkcí sady Speech SDK. Konfigurace řeči má několik způsobů, jak zadat předplatné, které chcete použít; Tady používáme `FromSubscription` , který přebírá klíč a oblast předplatného.

> [!NOTE]
> Použijte klíč a oblast předplatného LUIS, nikoli předplatné služby Speech.

Potom pomocí `new IntentRecognizer(config)` vytvořte nástroj pro rozpoznávání záměru. Vzhledem k tomu, že konfigurace už zná předplatné, které se má použít, nemusíte při vytváření nástroje pro rozpoznávání zadat klíč a koncový bod předplatného.

## <a name="import-a-luis-model-and-add-intents"></a>Import modelu LUIS a přidání záměrů

Teď model z aplikace LUIS naimportujte pomocí `LanguageUnderstandingModel.FromAppId()` a přidejte záměry služby LUIS, které si přejete pomocí metody `AddIntent()` nástroje pro rozpoznávání rozpoznat. Tyto dva kroky vylepší přesnost rozpoznávání řeči indikováním slov, která uživatel ve svých žádostech pravděpodobně použije. Není nutné přidávat všechny záměry aplikace, pokud je nepotřebujete v aplikaci rozpoznat vše.

Chcete-li přidat záměry, je nutné zadat tři argumenty: LUIS model (který byl vytvořen a má název `model` ), název záměru a ID záměru. Rozdíl mezi ID a názvem je následující.

| `AddIntent()`&nbsp;Argument | Účel |
| --------------------------- | ------- |
| `intentName` | Název záměru, jak je definovaný v aplikaci LUIS. Tato hodnota musí přesně odpovídat názvu záměru LUIS. |
| `intentID` | ID přiřazené rozpoznanému záměru sadou Speech SDK. Tato hodnota může být libovolná? nemusí odpovídat názvu záměru definovanému v aplikaci LUIS. Pokud třeba stejný kód zpracovává více záměrů, můžete pro ně použít stejné ID. |

Aplikace LUIS pro domácí automatizaci má dva záměry: jeden pro zapnutí zařízení a druhý pro vypnutí zařízení. Níže uvedené řádky přidají tyto záměry do nástroje pro rozpoznávání. Tyto tři řádky `AddIntent` v metodě `RecognizeIntentAsync()` nahraďte tímto kódem.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Místo přidání individuálních záměrů můžete také použít `AddAllIntents` metodu k přidání všech záměrů do modelu do nástroje pro rozpoznávání.

## <a name="start-recognition"></a>Zahájení rozpoznávání

Když máte vytvořený nástroj pro rozpoznávání a přidali jste záměry, můžete zahájit rozpoznávání. Sada Speech SDK podporuje jednorázové i průběžné rozpoznávání.

| Režim rozpoznávání | Volané metody | Výsledek |
| ---------------- | --------------- | ------ |
| Jednorázový | `RecognizeOnceAsync()` | Vrací rozpoznaný záměr, pokud existuje, po jedné promluvě. |
| Průběžný | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Rozpoznává více projevy; generuje události (například `IntermediateResultReceived` ), pokud jsou k dispozici výsledky. |

Aplikace používá režim s jednou sestřelou, takže volání `RecognizeOnceAsync()` zahájí rozpoznávání. Výsledkem je objekt `IntentRecognitionResult`, který obsahuje informace o rozpoznaném záměru. LUIS odpověď JSON můžete extrahovat pomocí následujícího výrazu:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Aplikace neanalyzuje výsledek JSON. V okně konzoly se zobrazí pouze text JSON.

![Výsledky rozpoznávání jednoduchých LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Specifikace jazyka rozpoznávání

Ve výchozím nastavení rozpoznává služba LUIS záměry v americké angličtině (`en-us`). Když přiřadíte kód národního prostředí vlastnosti `SpeechRecognitionLanguage` konfigurace řeči, můžete záměry rozpoznávat v jiných jazycích. Například přidejte `config.SpeechRecognitionLanguage = "de-de";` v naší aplikaci před vytvořením nástroje pro rozpoznávání, aby bylo možné rozpoznat záměry v němčině. Další informace najdete v tématu [Podpora jazyků Luis](../LUIS/luis-language-support.md#languages-supported).

## <a name="continuous-recognition-from-a-file"></a>Průběžné rozpoznávání ze souboru

Následující kód představuje další dvě možnosti rozpoznávání záměru pomocí sady Speech SDK. První z nich už jsme zmínili, je to průběžné rozpoznávání, u kterého nástroj pro rozpoznávání vysílá události, když jsou výsledky k dispozici. Tyto události pak mohou být zpracovány vámi zadanými obslužnými rutinami událostí. Při průběžném rozpoznávání zavoláte metodu nástroje pro rozpoznávání, `StartContinuousRecognitionAsync()` aby bylo možné spustit rozpoznávání místo `RecognizeOnceAsync()` .

Druhou možností je čtení zvuku obsahujícího řeč, která se má zpracovat, ze souboru WAV. Implementace zahrnuje vytvoření konfigurace zvuku, která se dá použít při vytváření nástroje pro rozpoznávání záměrů. Soubor musí být jednokanálový (mono) a musí mít vzorkovací frekvenci 16 kHz.

Chcete-li tyto funkce vyzkoušet, odstraňte nebo Odkomentujte tělo `RecognizeIntentAsync()` metody a přidejte následující kód na místo.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Ověřte, že kód obsahuje klíč koncového bodu služby LUIS, oblast a ID aplikace a přidává záměry aplikace Home Automation. Změňte `whatstheweatherlike.wav` název zaznamenaného zvukového souboru. Pak Sestavte, zkopírujte zvukový soubor do adresáře sestavení a spusťte aplikaci.

Například pokud jste řekli "vypnout světla", pozastavit a pak vyslovit "zapnout světla" v nahraném zvukovém souboru, může se zobrazit výstup konzoly podobný následujícímu:

![Výsledky rozpoznávání LUIS zvukového souboru](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte kód z tohoto článku ve složce **Samples/CSharp/sharedcontent/Console** .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý Start: rozpoznávání řeči z mikrofonu](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)