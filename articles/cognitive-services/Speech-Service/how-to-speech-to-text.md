---
title: Umožňuje využít řeč na Text
description: Zjistěte, jak použít převod řeči na Text v Speech service
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 5e7916660275ab45c4556f1169f3e68fe1d3cb85
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282292"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>Použití "Speech to Text" v Speech service

Můžete použít **převod řeči na Text** ve svých aplikacích dvěma různými způsoby.

| Metoda | Popis |
|-|-|
| [Sada SDK](speech-sdk.md) | Nejjednodušší způsob pro vývojáře v jazyce C/C++, C# a Java |
| [REST](rest-apis.md) | Rozpoznání krátkých projevy pomocí požadavku HTTP POST | 

## <a name="using-the-sdk"></a>Použití sady SDK

[Sadou SDK pro řeč](speech-sdk.md) poskytuje nejjednodušší způsob, jak používat **převod řeči na Text** ve vaší aplikaci se všemi funkcemi.

1. Vytvořte objekt pro vytváření řeči, poskytuje klíče předplatného služby řeči a [oblasti](regions.md) nebo autorizační token. Můžete také nakonfigurovat možnosti, jako je například rozpoznávání jazyka nebo vlastní koncový bod pro vlastní modely pro rozpoznávání řeči, v tomto okamžiku.

2. Získání nástroje pro rozpoznávání z objekt pro vytváření. Jsou k dispozici tři různé typy nástrojů pro rozpoznávání. Každý typ nástroje pro rozpoznávání můžete použít výchozí mikrofon zařízení, zvukový datový proud nebo zvuk ze souboru.

    Rozpoznávání rukopisu | Funkce
    -|-
    Rozpoznávání řeči|Obsahuje text určené k transkripci řeči
    Nástroj pro rozpoznávání záměru|Je odvozen záměr mluvčího prostřednictvím [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) po rozpoznávání\*
    Překlad modulu pro rozpoznávání|Přeloží přepisována text, který má jiný jazyk (viz [překlad řeči](how-to-translate-speech.md))

    \* *Pro rozpoznání záměru budete muset použít samostatný klíč předplatného služby LUIS při vytváření továrny řeči pro rozpoznávání záměru.*
    
4. V případě potřeby vytížit události pro asynchronní operaci. Nástroj pro rozpoznávání pak zavolá obslužné rutiny události je dočasný a finální výsledky. Jinak vaše aplikace dostanou určené k transkripci konečný výsledek.

5. Začněte rozpoznávání.
   Rozpoznávání jednorázová, jako je rozpoznávání příkaz nebo dotaz, použijte `RecognizeAsync()`, která vrací první utterance bude rozpoznán.
   Pro dlouho běžící rozpoznávání, jako jsou určené k transkripci, použijte `StartContinuousRecognitionAsync()` a provázat postupy události pro asynchronní rozpoznávání výsledky.

### <a name="sdk-samples"></a>Ukázky sady SDK

Nejnovější sadu ukázek, najdete v článku [úložiště GitHub SDK ukázka Cognitive Services řeči](https://aka.ms/csspeech/samples).

## <a name="using-the-rest-api"></a>Pomocí rozhraní REST API

Rozhraní REST API je nejjednodušší způsob, jak rozpoznávání řeči, pokud nepoužíváte podporován sady SDK. Provedete požadavek HTTP POST do koncového bodu služby předávání celý utterance v textu požadavku. Obdržíte odpověď obsahující rozpoznaný text.

> [!NOTE]
> Projevy jsou omezená na 15 sekund nebo méně, při použití rozhraní REST API.

Další informace o **převod řeči na Text** rozhraní REST API najdete v tématu [rozhraní REST API](rest-apis.md#speech-to-text). Chcete-li zobrazit v akci, stáhněte si [ukázky rozhraní REST API](https://github.com/Azure-Samples/SpeechToText-REST) z Githubu.

## <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Jak rozpoznávat řeč v jazyce C++](quickstart-cpp-windows.md)
- [Rozpoznávání řeči v C#](quickstart-csharp-dotnet-windows.md)
- [Jak rozpoznávat řeč v jazyce Java](quickstart-java-android.md)
