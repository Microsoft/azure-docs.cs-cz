---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 786f9587ab223cf87a48cd791f366049b94af59b
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97866108"
---
Nejprve načtěte soubor modelu klíčových slov pomocí `FromFile()` statické funkce, která vrátí `KeywordRecognitionModel` . Použijte cestu k `.table` souboru, který jste stáhli ze sady Speech Studio. Kromě toho se vytvoří `AudioConfig` pomocí výchozího mikrofonu a pak se vytvoří instance nového `KeywordRecognizer` pomocí konfigurace zvuku.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

V dalším kroku je spuštění rozpoznávání klíčového slova provedeno s jedním voláním metody `RecognizeOnceAsync()` předáním objektu modelu. Tím spustíte relaci rozpoznávání klíčových slov, která trvá, dokud nebude klíčové slovo rozpoznáno. Tento vzor návrhu se proto obecně používá v aplikacích s více vlákny nebo v případech použití, kde můžete počkat na neomezenou dobu probuzení slova.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Příklad, který je zde znázorněn, používá místní rozpoznávání klíčového slova, protože nevyžaduje `SpeechConfig` objekt pro kontext ověřování a nekontaktuje back-end. Rozpoznávání klíčových slov a ověřování však můžete použít [k přímému připojení back-end](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).

### <a name="continuous-recognition"></a>Průběžné rozpoznávání

Jiné třídy v sadě Speech SDK podporují nepřetržité rozpoznávání (pro rozpoznávání řeči i záměr) pomocí rozpoznávání klíčových slov. To vám umožní použít stejný kód, který byste normálně používali pro průběžné rozpoznávání, s možností odkazovat na `.table` soubor pro váš klíč klíčových slov.

Pro převod řeči na text použijte stejný vzor návrhu zobrazený v [rychlém](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-speech-to-text?tabs=script%2Cbrowser%2Cwindowsinstall&pivots=programming-language-csharp#continuous-recognition) startu a nastavte průběžné rozpoznávání. Potom nahraďte volání `recognizer.StartContinuousRecognitionAsync()` pomocí `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` a předejte svůj `KeywordRecognitionModel` objekt. Chcete-li ukončit průběžné rozpoznávání pomocí klíčového slova hledání, použijte `recognizer.StopKeywordRecognitionAsync()` místo `recognizer.StopContinuousRecognitionAsync()` .

Rozpoznávání záměru používá identický vzor s [`StartKeywordRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) funkcemi a [`StopKeywordRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) .