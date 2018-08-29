---
title: Překlad řeči pomocí sadou SDK pro řeč pro Javu
titleSuffix: Microsoft Cognitive Services
description: Ukazuje, jak překlad řeči pomocí sadou SDK pro řeč pro Javu.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: cddea05a237936fd9a2cc6a4d807c6b1f300077c
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144610"
---
# <a name="translate-speech-with-the-cognitive-services-speech-sdk-for-java"></a>Překlad řeči sadou Cognitive Services řeči SDK pro Javu

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!INCLUDE [Introduction to top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-java [Translation from a microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithMicrophoneAsync)]

[!INCLUDE [Introduction to using a file](../../../includes/cognitive-services-speech-service-how-to-translate-speech-file.md)]

[!code-java [Translation from file input](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithFileAsync)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte kód, který se používá v tomto článku ve složce samples/java/jre/konzoly.

## <a name="next-steps"></a>Další postup

- [Jak rozpoznávání řeči](how-to-recognize-speech-java.md)
- [Jak rozpoznat záměry z řeči](how-to-recognize-intents-from-speech-java.md)
