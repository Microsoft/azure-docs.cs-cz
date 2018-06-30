---
title: Ukázka pro záměrné rozpoznávání | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Zde je ukázka pro rozpoznávání záměrné.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 1c9c1e2d54ccb200ef009be3566f6da9ced01175
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111163"
---
# <a name="sample-for-intent-recognition"></a>Ukázka pro záměrné rozpoznávání

> [!NOTE]
> Pokyny ke stažení této ukázce a ostatní uživatele najdete v tématu [ukázky pro rozpoznávání řeči SDK](samples.md).

> [!NOTE]
> Nejdřív získejte klíč předplatného. Na rozdíl od jiných služeb nepodporuje kognitivní řeči SDK služby služby rozpoznávání záměr vyžaduje klíč konkrétní předplatné. [Zde](https://www.luis.ai) můžete najít další informace o technologii záměrné rozpoznávání a také informace o tom, jak získat klíč předplatného. Nahraďte svůj vlastní klíč předplatného [oblasti vašeho předplatného](regions.md)a AppId na příslušná místa v ukázkách záměrné modelu.

> [!NOTE]
> Pro všechny ukázky níže následující nejvyšší úrovně deklarace musí být k dispozici:
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>Záměrné rozpoznávání pomocí mikrofon

Následující fragment kódu ukazuje, jak rozpoznat záměr z mikrofon vstup ve výchozí jazyk (`en-US`).

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Záměrné rozpoznávání pomocí mikrofon v určený jazyk.

Následující fragment kódu ukazuje, jak rozpoznat záměr ze vstupu mikrofon v určený jazyk, v takovém případě v němčina (`de-de`).

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>Záměrné rozpoznávání ze souboru

Následující fragment kódu rozpozná záměr z zvukový soubor ve výchozím jazyce (`en-US`), podporované formát je jeden kanál (mono) WAV nebo PCM s vzorkovací frekvenci 16 kHz.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>Záměrné rozpoznávání pomocí událostí

Fragment kódu ukazuje, jak rozpoznat záměr průběžné způsobem. Tento kód umožňuje přístup na další informace, jako je mezilehlých výsledků. 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Ukázka zdrojového kódu

Nejnovější sadu vzorků, najdete v článku [úložiště GitHub SDK ukázka kognitivní služby řeči](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Další postup

- [Rozpoznávání řeči](./speech-to-text-sample.md)

- [Překlad](./translation.md)
