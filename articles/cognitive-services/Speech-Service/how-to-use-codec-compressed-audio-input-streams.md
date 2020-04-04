---
title: Streamkací kodek komprimovaný zvuk pomocí služby Speech SDK - Speech
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak streamovat komprimovaný zvuk do služby Řeč pomocí sady Speech SDK. K dispozici pro C++, C# a Java pro Linux, Java v Androidu a Objective-C v iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: bd6b500a823bde654da4442704b75451806d6a46
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637274"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Použití komprimovaného zvukového vstupu kodeku se sadou Speech SDK

Rozhraní API **komprimovaného vstupního vstupního datového proudu** zvuku sady Speech service `PullStream` `PushStream`poskytuje způsob streamování komprimovaného zvuku do služby Řeč pomocí služby a nebo .

Streamování komprimovaného vstupního zvuku je v současné době podporováno pro C#, C++, Java v systému Windows (aplikace UPW nejsou podporovány) a Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Je také podporován pro Java v Androidu a Objective-C v platformě iOS.
* Řeč SDK verze 1.10.0 nebo novější je vyžadována pro RHEL 8 a CentOS 8
* Pro systém Windows je vyžadována sada Speech SDK verze 1.11.0 nebo novější.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Požadavky

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Ukázkový kód pomocí komprimovaného zvukového vstupu kodeku

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se rozpoznávat řeč](quickstarts/speech-to-text-from-microphone.md)
