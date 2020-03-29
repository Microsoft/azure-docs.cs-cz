---
title: Volba režimu rozpoznávání řeči pomocí sady Speech SDK
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak zvolit nejlepší režim rozpoznávání při použití sady Speech SDK.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79079828"
---
# <a name="choose-a-speech-recognition-mode"></a>Volba režimu rozpoznávání řeči

Při zvažování operace rozpoznávání řeči na text, [sada Speech SDK](speech-sdk.md) poskytuje více režimů pro zpracování řeči. Koncepčně se někdy nazývá *režim rozpoznávání*. Tento článek porovnává různé režimy rozpoznávání.

## <a name="recognize-once"></a>Rozpoznat jednou

Pokud chcete zpracovat každý utterance jednu "větu" najednou, použijte funkci "rozpoznat jednou". Tato metoda detekuje rozpoznaný utterance od vstupu počínaje začátkem zjištěné řeči až do další pauzy. Obvykle pauza označuje konec věty nebo linie myšlení.

Na konci jednoho rozpoznaného utterance služba zastaví zpracování zvuku z tohoto požadavku. Maximální limit pro rozpoznávání je doba trvání trestu 20 sekund.

::: zone pivot="programming-language-csharp"

Další informace o `RecognizeOnceAsync` použití funkce naleznete v [dokumentech sady .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Další informace o `RecognizeOnceAsync` použití funkce naleznete v [dokumentech sady C++ Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Další informace o `recognizeOnceAsync` používání funkce naleznete v [dokumentech sady Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Další informace o `recognize_once` používání funkce naleznete v [dokumentech sady Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Další jazyky naleznete v [referenčních dokumentech sady Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Průběžný

Pokud potřebujete dlouhotrvající rozpoznávání, použijte funkce start a odpovídající stop pro průběžné rozpoznávání. Počáteční funkce se spustí a pokračovat ve zpracování všech projevy, dokud vyvolat stop funkce nebo dokud příliš mnoho času v tichu uplynulo. Při použití nepřetržitého režimu se nezapomeňte zaregistrovat k různým událostem, které se při výskytu vypálí. Například "rozpoznaný" událost požáry při rozpoznávání řeči. Musíte mít obslužnou rutinu události na místě pro zpracování rozpoznávání.

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

Další jazyky naleznete v [referenčních dokumentech sady Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Diktování

Při použití průběžného rozpoznávání můžete povolit zpracování diktování pomocí odpovídající funkce "povolit diktování". Tento režim způsobí, že instance konfigurace řeči interpretuje popisy slov větných struktur, jako je interpunkce. Například utterance "Žijete ve městě otazník" by být interpretován jako text "Žijete ve městě?".

::: zone pivot="programming-language-csharp"

Další informace o `EnableDictation` použití funkce naleznete v [dokumentech sady .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Další informace o `EnableDictation` použití funkce naleznete v [dokumentech sady C++ Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Další informace o `enableDictation` používání funkce naleznete v [dokumentech sady Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Další informace o `enable_dictation` používání funkce naleznete v [dokumentech sady Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Další jazyky naleznete v [referenčních dokumentech sady Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte další ukázky sady Speech SDK na GitHubu](https://aka.ms/csspeech/samples)
