---
title: Ukázka pro převod řeči na Text | Dokumentace Microsoftu
titleSuffix: Microsoft Cognitive Services
description: Tady je ukázka pro převod řeči na text.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: b08b461ceccbdf5e79d7bb4320bdc15d09c4b859
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114238"
---
# <a name="sample-for-speech-to-text"></a>Ukázka pro převod řeči na Text

[!include[Get a Subscription Key](../../../includes/cognitive-services-speech-service-get-subscription-key.md)]

## <a name="top-level-declarations"></a>Deklaracemi nejvyšší úrovně

Všechny ukázky níže najdete následující nejvyšší úrovně deklarace by měla:

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#toplevel)]

## <a name="speech-recognition-using-the-microphone"></a>Rozpoznávání řeči pomocí mikrofon.

Následující fragment kódu ukazuje, jak rozpoznávat řeč mikrofon ve výchozím jazyce (`en-US`).

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

[!code-java[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#recognitionWithMicrophone)]

## <a name="speech-recognition-using-a-customized-model"></a>Rozpoznávání řeči pomocí přizpůsobených modelu

[Custom Speech Service (CRIS) která](https://www.cris.ai/) poskytuje možnosti vlastního nastavení modul převodu řeči na text od Microsoftu pro vaši aplikaci. Následující fragment kódu ukazuje, jak rozpoznávání řeči z mikrofonu pomocí vašeho modelu CRIS; Zadejte klíč předplatného CRIS a vlastní identifikaci nasazení před jejím spuštěním.

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

[!code-java[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#recognitionCustomized)]

## <a name="continuous-speech-recognition-from-a-file"></a>Rozpoznávání řeči průběžné ze souboru

Následující fragment kódu průběžně rozpozná hlasový vstup z zvukový soubor ve výchozím jazyce (`en-US`), podporovaný formát je jeden kanál (mono) WAV / PCM s vzorkovací frekvenci kHz 16.

[!include[Sample Audio](../../../includes/cognitive-services-speech-service-sample-audio.md)]

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#recognitionContinuousWithFile)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionWithFile)]

[!code-java[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#recognitionContinuousWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Další postup

- [Rozpoznávání záměru](./intent.md)

- [Překlad](./translation.md)
