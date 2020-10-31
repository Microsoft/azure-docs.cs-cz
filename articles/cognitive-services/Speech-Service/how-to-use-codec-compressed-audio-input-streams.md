---
title: Kodek Stream komprimovaný zvuk se sadou Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Naučte se streamovat komprimovaný zvuk do služby Speech pomocí sady Speech SDK. K dispozici pro jazyky C++, C# a Java pro Linux, Java v Androidu a objektivní-C v iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: 7d6a0388507d836050550a3a98255deabb826cde
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128317"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Použití komprimovaného zvukového vstupu kodeku se sadou Speech SDK

Rozhraní API pro **komprimovaný zvuk vstupního streamu** v sadě Speech Service poskytuje způsob, jak streamovat komprimovaný zvuk do služby Speech Service pomocí `PullStream` nebo `PushStream` .

Platforma | Jazyky | Podporovaná verze GStreamer
| :--- | ---: | :---:
Windows (s výjimkou UWP)  | C++, C#, Java, Python | [1.15.1](https://gstreamer.freedesktop.org/data/pkg/windows/1.15.1/)
Linux  | C++, C#, Java, Python | [Podporované distribuce systému Linux a cílové architektury](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Pro komprimovaný zvukový vstup se vyžaduje verze sady Speech SDK.
* Pro RHEL 8 a CentOS 8 se vyžaduje sada Speech SDK verze 1.10.0 nebo novější.
* Pro systém Windows je vyžadována sada Speech SDK verze 1.11.0 nebo novější.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Předpoklady

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Příklad kódu pomocí komprimovaného zvukového vstupu kodeku

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se rozpoznávat řeč.](quickstarts/speech-to-text-from-microphone.md)
