---
title: Základy syntézy řeči – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se používat sadu Speech SDK pro převod textu na řeč. V tomto článku se naučíte vytváření objektů, podporované formáty zvukového výstupu a možnosti vlastní konfigurace pro syntézu řeči.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-sixteen
ms.openlocfilehash: 74f762dd0004bf72b703d0f611a16762b1ff028d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399563"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Naučte se základy syntézy řeči.

V tomto článku se seznámíte s běžnými návrhovými vzory pro provádění syntézy textu na řeč pomocí sady Speech SDK. Začnete tím, že provádíte základní konfiguraci a shrnutí a přejdete k pokročilejším příkladům pro vývoj vlastních aplikací, včetně:

* Získávání odpovědí jako proudů v paměti
* Přizpůsobení výstupní vzorkovací frekvence a přenosové rychlosti
* Odesílání požadavků na syntézu pomocí SSML (Speech syntézy Markup Language)
* Používání hlasů neuronové

> [!TIP]
> Pokud jste si ještě neučinili možnost Dokončit některý z našich rychlých startů, doporučujeme, abyste si zavedli pneumatiky a vyzkoušeli rozpoznávání řeči sami.
> * [Rozpoznávání řeči z mikrofonu](quickstarts/text-to-speech.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>Další kroky

* [Začínáme se službou Custom Voice](how-to-custom-voice.md)
* [Vylepšení syntézy pomocí jazyka SSML](speech-synthesis-markup.md)