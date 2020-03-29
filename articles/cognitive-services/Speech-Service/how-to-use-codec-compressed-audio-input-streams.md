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
ms.date: 03/09/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 3fab02d3dc567a2c54edad5bfb05abe7d99f7b7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943827"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Použití komprimovaného zvukového vstupu kodeku se sadou Speech SDK

Rozhraní API **komprimovaného vstupního vstupního datového proudu** zvuku sady Speech service `PullStream` `PushStream`poskytuje způsob streamování komprimovaného zvuku do služby Řeč pomocí služby a nebo .

> [!IMPORTANT]
> Streamování komprimovaného vstupního zvuku je v současné době podporováno pro C#, C++, Java na Linuxu (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Je také podporován pro Java v Androidu a Objective-C v platformě iOS.
> Je vyžadována sada Speech SDK verze 1.7.0 nebo vyšší (verze 1.10.0 nebo vyšší pro RHEL 8, CentOS 8).

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Požadavky

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Ukázkový kód pomocí komprimovaného zvukového vstupu kodeku

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se rozpoznávat řeč](quickstarts/speech-to-text-from-microphone.md)
