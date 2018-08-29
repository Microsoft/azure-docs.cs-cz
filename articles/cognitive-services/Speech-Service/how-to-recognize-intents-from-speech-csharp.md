---
title: Rozpoznat záměry z řeči pomocí sadou SDK pro řeč pro jazyk C#
titleSuffix: Microsoft Cognitive Services
description: >
  Naučily se rozpoznávat záměry z řeči ze souboru nebo mikrofon pomocí sadou SDK pro řeč pro jazyk C#.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 7de53ec33e678b5532cd6980f7efbee228459082
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144746"
---
# <a name="recognize-intents-from-speech-by-using-the-speech-sdk-for-c"></a>Rozpoznat záměry z řeči pomocí sadou SDK pro řeč pro jazyk C#

[!INCLUDE [Article selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-intro.md)]

[!INCLUDE [Introduction for top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-microphone.md)]

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!INCLUDE [Introduction - using microphone and language](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-microphone-language.md)]

[!code-csharp[Intent recognition by using a microphone in a specified language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!INCLUDE [Introduction to using a continuous file](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-continuous.md)]

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte kód, který se používá v tomto článku ve složce samples/csharp/sharedcontent/console.

## <a name="next-steps"></a>Další postup

- [Jak rozpoznávání řeči](how-to-recognize-speech-csharp.md)
- [Způsob převodu řeči](how-to-translate-speech-csharp.md)
