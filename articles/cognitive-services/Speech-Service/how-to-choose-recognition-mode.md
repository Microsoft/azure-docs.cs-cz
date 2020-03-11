---
title: Výběr režimu rozpoznávání řeči pomocí sady Speech SDK
titleSuffix: Azure Cognitive Services
description: Naučte se, jak zvolit nejlepší režim rozpoznávání při použití sady Speech SDK.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: d997cb592d9d648998f2b44d9f61f465f05faeb0
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79079828"
---
# <a name="choose-a-speech-recognition-mode"></a>Zvolit režim rozpoznávání řeči

Při zvažování operací rozpoznávání řeči na text poskytuje [sada Speech SDK](speech-sdk.md) více režimů pro zpracování řeči. Konceptuální, někdy označovaný jako *režim rozpoznávání*. Tento článek porovnává různé režimy rozpoznávání.

## <a name="recognize-once"></a>Rozpoznat jednou

Pokud chcete zpracovat každou utterance jednu větu, použijte funkci rozpoznat jednou. Tato metoda detekuje rozpoznané utterance ze vstupu od začátku zjištěného hlasu až do dalšího pozastavení. Pozastavení obvykle označuje konec věty nebo řádku, který je myšlenkou.

Na konci jednoho rozpoznaného utterance služba zastaví zpracování zvuku z tohoto požadavku. Maximální omezení pro rozpoznávání je 20 sekund po dobu trvání věty.

::: zone pivot="programming-language-csharp"

Další informace o použití funkce `RecognizeOnceAsync` naleznete v [dokumentaci sady .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Další informace o použití funkce `RecognizeOnceAsync` naleznete v [ C++ dokumentaci sady Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Další informace o použití funkce `recognizeOnceAsync` naleznete v [dokumentaci Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Další informace o použití funkce `recognize_once` naleznete v [dokumentaci k sadě Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Další jazyky naleznete v [referenční dokumentaci sady Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Průběžný

Pokud potřebujete dlouhodobě vyhodnotit, pro průběžné rozpoznávání použijte počáteční a odpovídající funkce stop. Funkce Start spustí a bude pokračovat ve zpracování všech projevy, dokud nevyvoláte funkci stop nebo dokud neuplyne příliš mnoho času v tichém zpracování. Při použití průběžného režimu nezapomeňte zaregistrovat různé události, které se spustí při výskytu. Například "rozpoznaná" událost aktivuje, když dojde k rozpoznávání řeči. Musíte mít k dispozici obslužnou rutinu události pro zpracování rozpoznávání.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end
::: zone pivot="programming-language-more"

Další jazyky naleznete v [referenční dokumentaci sady Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Diktování

Při použití průběžného rozpoznávání můžete povolit zpracování diktování pomocí odpovídající funkce "Povolit diktování". V tomto režimu bude instance konfigurace řeči interpretovat popisy slov ve strukturách vět, jako je například interpunkční znaménko. Například utterance "provedete to živě ve městě otazník", který se interpretuje jako text "žijete ve městě?".

::: zone pivot="programming-language-csharp"

Další informace o použití funkce `EnableDictation` naleznete v [dokumentaci sady .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Další informace o použití funkce `EnableDictation` naleznete v [ C++ dokumentaci sady Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Další informace o použití funkce `enableDictation` naleznete v [dokumentaci Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Další informace o použití funkce `enable_dictation` naleznete v [dokumentaci k sadě Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Další jazyky naleznete v [referenční dokumentaci sady Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte další ukázky sady Speech SDK na GitHubu](https://aka.ms/csspeech/samples)
