---
title: Ukázka pro rozpoznání záměru | Dokumentace Microsoftu
titleSuffix: Microsoft Cognitive Services
description: Tady je ukázka pro rozpoznání záměru.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 3e9afc990d6bfa73eb045e7ed76dfd194df309c6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213194"
---
# <a name="sample-for-intent-recognition"></a>Ukázka pro rozpoznání záměru

Nejdřív získáte klíč předplatného. Na rozdíl od jiných služeb podporovaných službou Cognitive Service SDK řeči rozpoznávání záměru služby vyžaduje klíč konkrétní předplatné. [Tady](https://www.luis.ai) najdete další informace o technologii rozpoznávání záměru, jakož i informace o tom, jak získat klíč předplatného. Nahraďte vlastní klíč předplatného Language Understanding [oblasti vašeho předplatného](regions.md)a AppId záměru modelu na příslušných místech ve vzorcích.

## <a name="top-level-declarations"></a>Deklaracemi nejvyšší úrovně

Pro všechny ukázky níže následující nejvyšší úrovně deklarace by měla:

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

## <a name="intent-recognition-using-microphone"></a>Rozpoznání záměru použití mikrofonu

Následující fragment kódu ukazuje, jak se rozpoznávání záměru ze vstupu mikrofon ve výchozím jazyce (`en-US`).

[!code-csharp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Rozpoznání záměru do zadaného jazyku pomocí mikrofon

Následující fragment kódu ukazuje, jak se rozpoznávání záměru ze vstupu mikrofon v zadaný jazyk, v tomto případě v němčině (`de-de`).

[!code-csharp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

## <a name="intent-recognition-from-a-file-using-events"></a>Rozpoznání záměru ze souboru, s použitím událostí

Fragment kódu ukazuje, jak se rozpoznávání záměru ve výchozím jazyce (`en-US`) průběžné způsobem. Tento kód umožňuje přístup na další informace, jako jsou průběžné výsledky. Vstup je převzata z zvukový soubor, podporovaný formát je jeden kanál (mono) WAV / PCM s vzorkovací frekvenci kHz 16.

[!code-csharp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Další postup

- [Rozpoznávání řeči](./speech-to-text-sample.md)

- [Překlad](./translation.md)
