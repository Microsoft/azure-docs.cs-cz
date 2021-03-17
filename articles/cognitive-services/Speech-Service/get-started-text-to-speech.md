---
title: Rychlý Start převodu textu na řeč – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se používat sadu Speech SDK pro převod textu na řeč. V tomto rychlém startu se naučíte vytvářet konstrukce objektů a navrhovat vzory, podporované formáty zvukového výstupu, rozpoznávání řeči a možnosti vlastní konfigurace pro syntézu řeči.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: převod textu na řeč
ms.openlocfilehash: 7a41c4d9c1074b376da3de556caf63ced0bc84ec
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428179"
---
# <a name="get-started-with-text-to-speech"></a>Začínáme s převodem textu na řeč

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

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>Získat informace o pozici

Váš projekt může potřebovat znát, když je slovo mluvené řeči mluveným textem, aby bylo možné provést konkrétní akci na základě tohoto časování. Pokud byste například chtěli Zvýrazňovat slova při jejich mluveném, měli byste znát, co je třeba zvýraznit, kdy je zvýraznit a jak dlouho je zvýraznit.

To můžete provést pomocí `WordBoundary` události dostupné v rámci `SpeechSynthesizer` . Tato událost se vyvolá na začátku každého nového mluveného slova a v rámci vstupní výzvy vám poskytne časový posun v rámci mluveného vysílání a také posun textu.

* `AudioOffset` hlásí uplynulý čas výstupu mezi začátkem syntézy a začátkem následujícího slova. To se měří v jednotkách se stovkami (HNS) s 10 000, který odpovídá 1 milisekundu.
* `WordOffset` oznamuje pozici znaku ve vstupním řetězci (původní text nebo [SSML](speech-synthesis-markup.md)) těsně před slovem, který se má vymluvené.

> [!NOTE]
> `WordBoundary` události jsou vyvolány, protože výstupní zvuková data budou k dispozici, což bude rychlejší než přehrávání do výstupního zařízení. Správné synchronizace časování datového proudu do "reálného času" musí být provedeno volajícím.

Příklady použití najdete `WordBoundary` v [ukázkách převodu textu na řeč](https://aka.ms/csspeech/samples) na GitHubu.

## <a name="next-steps"></a>Další kroky

* [Začínáme se službou Custom Voice](how-to-custom-voice.md)
* [Vylepšení syntézy pomocí jazyka SSML](speech-synthesis-markup.md)
* Přečtěte si, jak používat [dlouhé zvukové rozhraní API](long-audio-api.md) pro velké vzorky textu, jako jsou knihy a novinové články.
* Podívejte se na [ukázky rychlý Start](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) na GitHubu.
