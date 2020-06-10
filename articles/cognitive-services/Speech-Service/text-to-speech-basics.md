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
ms.custom: tracking-python
zone_pivot_groups: programming-languages-set-two-with-js
ms.openlocfilehash: 2bba1536c9d0990eb771f62d27b852ae1eb3b45c
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84604611"
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
