---
title: Základy syntézy řeči - Služba řeči
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak pomocí sady Speech SDK převést převod textu na řeč. V tomto článku se dozvíte o konstrukci objektů, podporovaných formátech zvukového výstupu a vlastních možnostech konfigurace pro syntézu řeči.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: b95b55588c51c4e202d7a02c6c158b26cdcd7ac7
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986230"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Naučte se základy syntézy řeči

V tomto článku se dozvíte běžné vzory návrhu pro syntézu převodu textu na řeč pomocí sady Speech SDK. Můžete začít tím, že dělá základní konfiguraci a syntézu a přejít na pokročilejší příklady pro vývoj vlastních aplikací, včetně:

* Získávání odpovědí jako datových proudů v paměti
* Přizpůsobení výstupní vzorkovací rychlosti a přenosové rychlosti
* Odesílání žádostí o syntézu pomocí SSML (jazyk značek syntézy řeči)
* Použití neurálních hlasů

> [!TIP]
> Pokud jste ještě neměli šanci dokončit jeden z našich rychlých startů, doporučujeme vám, abyste nakopli pneumatiky a vyzkoušeli rozpoznávání řeči sami.
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

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>Další kroky

* [Začínáme se službou Custom Voice](how-to-custom-voice.md)
* [Vylepšení syntézy pomocí jazyka SSML](speech-synthesis-markup.md)