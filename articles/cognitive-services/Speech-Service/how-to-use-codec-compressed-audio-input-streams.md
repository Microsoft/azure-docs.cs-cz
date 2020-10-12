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
ms.openlocfilehash: 4585538e552e73f8f7a4b7b105153a9d26eeb4c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934088"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Použití komprimovaného zvukového vstupu kodeku se sadou Speech SDK

Rozhraní API pro **komprimovaný zvuk vstupního streamu** v sadě Speech Service poskytuje způsob, jak streamovat komprimovaný zvuk do služby Speech Service pomocí `PullStream` nebo `PushStream` .

V současné době se pro jazyky C#, C++, Java a Python v systému Windows podporuje streamování komprimovaného vstupu (aplikace UWP nejsou podporované) a Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8). Podporuje se i pro Java v Androidu.
* Pro RHEL 8 a CentOS 8 se vyžaduje sada Speech SDK verze 1.10.0 nebo novější.
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
