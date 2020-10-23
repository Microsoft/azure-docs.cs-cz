---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: f903c0ea3b7ca6fb692e216a5681bb89223a764c
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470747"
---
Jednou z klíčových funkcí služby Speech je možnost rozpoznávat a přepisovat lidské řeči (často se označuje jako převod řeči na text). V tomto rychlém startu se naučíte používat sadu Speech SDK ve vašich aplikacích a produktech k provádění vysoce kvalitních převodů řeči na text.

## <a name="skip-to-samples-on-github"></a>Přeskočit na ukázky na GitHubu

Pokud chcete přeskočit přímý na vzorový kód, přečtěte si [ukázky pro rychlý Start v C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet) na GitHubu.

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat sadu Speech SDK. V závislosti na vaší platformě postupujte podle následujících pokynů:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Jednot <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">PODPORUJÍ <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true) . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, jestli provádíte rozpoznávání řeči, syntézu řeči, překlad nebo rozpoznávání záměrů, vždy vytvoříte konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true) :

* S předplatným: předejte klíč a přidruženou oblast.
* S koncovým bodem: předejte koncový bod služby řeči. Klíč nebo autorizační token jsou volitelné.
* S hostitelem: předejte adresu hostitele. Klíč nebo autorizační token jsou volitelné.
* Pomocí autorizačního tokenu: předejte autorizační token a přidruženou oblast.

Pojďme se podívat, jak [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true) se vytvoří pomocí klíče a oblasti. Tyto přihlašovací údaje můžete získat podle kroků v [části Vyzkoušejte si službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicializovat Nástroj pro rozpoznávání

Po vytvoření [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true) je dalším krokem inicializace [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet&preserve-view=true) . Když inicializujete [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet&preserve-view=true) , předáte ho `speechConfig` . To poskytuje přihlašovací údaje, které služba Speech vyžaduje k ověření vaší žádosti.

```csharp
using var recognizer = new SpeechRecognizer(speechConfig);
```

## <a name="recognize-from-microphone-or-file"></a>Rozpoznávání z mikrofonu nebo souboru

Chcete-li zadat vstupní zvukové zařízení, je nutné vytvořit [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet&preserve-view=true) a předat ho jako parametr při inicializaci [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet&preserve-view=true) .

Pokud chcete rozpoznávat řeč pomocí mikrofonu zařízení, vytvořte `AudioConfig` pomocí `FromDefaultMicrophoneInput()` a pak při vytváření objektu předejte konfiguraci zvuku `SpeechRecognizer` .

```csharp
using Microsoft.CognitiveServices.Speech.Audio;

using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

> [!TIP]
> Přečtěte si, [Jak získat ID zařízení pro vstupní zvukové zařízení](../../../how-to-select-audio-input-devices.md).

Pokud chcete rozpoznávat řeč ze zvukového souboru místo mikrofonu, je stále potřeba vytvořit `AudioConfig` . Pokud však vytvoříte [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet&preserve-view=true) namísto volání volání, `FromDefaultMicrophoneInput()` zavoláte `FromWavFileInput()` a předáte `filename` parametr.

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Rozpoznávání řeči

[Třída pro rozpoznávání](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet&preserve-view=true) pro sadu Speech SDK pro jazyk C# zpřístupňuje několik metod, které lze použít pro rozpoznávání řeči.

* Rozpoznávání bez přípony (Async) – provádí rozpoznávání v neblokujícím (asynchronním) režimu. Tím se rozpozná jeden utterance. Konec jednoho utterance se určuje tak, že naslouchá tichému ukončení na konci nebo dokud se nezpracovává po dobu 15 sekund zvuku.
* Průběžné rozpoznávání (asynchronní) – asynchronně iniciuje operaci průběžného rozpoznávání. Uživatel zaregistruje události do událostí a zpracuje různé stavy aplikace. Chcete-li zastavit asynchronní průběžné rozpoznávání, zavolejte [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet&preserve-view=true) .

> [!NOTE]
> Přečtěte si další informace o tom, jak [zvolit režim rozpoznávání řeči](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Rozpoznávání s jedním záběrem

Tady je příklad asynchronního samostatného rozpoznávání pomocí [`RecognizeOnceAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet&preserve-view=true) :

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

Pro zpracování výsledku budete muset napsat nějaký kód. Tato ukázka vyhodnocuje [`result.Reason`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason?view=azure-dotnet&preserve-view=true) :

* Vytiskne výsledek rozpoznávání: `ResultReason.RecognizedSpeech`
* Pokud se neshodují žádné rozpoznávání, informujte uživatele: `ResultReason.NoMatch`
* Pokud dojde k chybě, vytiskněte chybovou zprávu: `ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        Console.WriteLine($"    Intent not recognized.");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

### <a name="continuous-recognition"></a>Průběžné rozpoznávání

Průběžné rozpoznávání je trochu větší než rozpoznávání s jedním prostřelou. Pro získání výsledků rozpoznávání se vyžaduje přihlášení k odběru `Recognizing` `Recognized` událostí, a `Canceled` . Chcete-li zastavit rozpoznávání, je nutné zavolat [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet&preserve-view=true) . Tady je příklad toho, jak se provádí nepřetržité rozpoznávání na vstupním souboru zvuku.

Pojďme začít definováním vstupu a inicializací [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet&preserve-view=true) :

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```
Nyní vytvoříme proměnnou, která bude spravovat stav rozpoznávání řeči. Pokud chcete začít, deklarujeme `TaskCompletionSource<int>` za předchozí deklaracemi.

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

Přihlásíme se k odběru událostí odeslaných z [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet&preserve-view=true) .

* [`Recognizing`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-dotnet&preserve-view=true): Signál pro události obsahující mezilehlé výsledky rozpoznávání.
* [`Recognized`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-dotnet&preserve-view=true): Signál pro události obsahující konečné výsledky rozpoznávání (indikující úspěšný pokus o uznání).
* [`SessionStopped`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-dotnet&preserve-view=true): Signál pro události indikující konec relace rozpoznávání (operace).
* [`Canceled`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-dotnet&preserve-view=true): Signál pro události obsahující zrušené výsledky rozpoznávání (indikující pokus o deaktivaci, který byl zrušen jako výsledek nebo přímý požadavek na zrušení nebo případně i přenos nebo selhání protokolu).

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

S nastavením všeho můžeme zavolat [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet&preserve-view=true) .

```csharp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// Stops recognition.
await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Režim diktování

Při použití průběžného rozpoznávání můžete povolit zpracování diktování pomocí odpovídající funkce "Povolit diktování". V tomto režimu bude instance konfigurace řeči interpretovat popisy slov ve strukturách vět, jako je například interpunkční znaménko. Například utterance "provedete to živě ve městě otazník", který se interpretuje jako text "žijete ve městě?".

Chcete-li povolit režim diktování, použijte [`EnableDictation`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet&preserve-view=true) metodu na [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true) .

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>Změnit jazyk zdroje

Běžným úkolem pro rozpoznávání řeči je zadání vstupu (nebo zdrojového) jazyka. Pojďme se podívat, jak byste změnili vstupní jazyk na italštinu. V kódu Najděte [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true) a pak přidejte tento řádek přímo pod ním.

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

[`SpeechRecognitionLanguage`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet&preserve-view=true)Vlastnost očekává řetězec formátu národního prostředí jazyka. Můžete zadat libovolnou hodnotu ve sloupci **locale (národní prostředí** ) v seznamu podporovaných [národních prostředí a jazyků](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Zlepšení přesnosti rozpoznávání

Existuje několik způsobů, jak vylepšit přesnost rozpoznávání pomocí sady Speech SDK. Pojďme se podívat na seznamy frází. Seznamy frází slouží k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění. Do seznamu frází lze přidat jednotlivá slova nebo kompletní fráze. Při rozpoznávání se používá záznam v seznamu frází, pokud je do zvukového umístění uvedena přesná shoda celé fráze. Pokud se nenalezne přesná shoda se slovem, rozpoznávání vám nepomáhá.

> [!IMPORTANT]
> Funkce seznamu frází je k dispozici pouze v angličtině.

Chcete-li použít seznam frází, nejprve vytvořte [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet&preserve-view=true) objekt a pak přidejte konkrétní slova a fráze pomocí [`AddPhrase`](https://docs.microsoft.com//dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-dotnet&preserve-view=true) .

Všechny změny se projeví [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet&preserve-view=true) při příštím rozpoznávání nebo po opětovném připojení ke službě Speech.

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

Pokud potřebujete vymazat seznam frází: 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Další možnosti pro zlepšení přesnosti rozpoznávání

Seznamy frází jsou pouze jedním z možností, jak zlepšit přesnost rozpoznávání. Můžete také: 

* [Zlepšení přesnosti s využitím služby Custom Speech](../../../how-to-custom-speech.md)
* [Zlepšení přesnosti s využitím modelů tenantů](../../../tutorial-tenant-model.md)