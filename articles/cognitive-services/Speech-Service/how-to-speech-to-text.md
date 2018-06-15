---
title: Použít převod řeči na Text | Microsoft Docs
description: Další informace o použití řeči na Text v rámci služby řeči
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 878a31992415b1f8688afcfb186fcd94ce2567b4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "35343955"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>Použití "Řeči na Text" ve službě řeči

Můžete použít **řeči na Text** ve svých aplikacích dvěma různými způsoby.

| Metoda | Popis |
|-|-|
| [Sada SDK](speech-sdk.md) | Nejjednodušším způsobem pro C/C++, C# a Java * vývojáře |
| [REST](rest-apis.md) | Rozpoznat krátké utterances pomocí požadavku HTTP POST | 

\* *Je součástí sady Java SDK [řeči zařízení SDK](speech-devices-sdk.md).*

## <a name="using-the-sdk"></a>Použití sady SDK

[Řeči SDK](speech-sdk.md) poskytuje nejjednodušší způsob, jak používat **řeči na Text** v aplikaci se všemi funkcemi.

1. Vytvořte objekt pro vytváření rozpoznávání řeči, poskytuje klíč předplatného služby řeči nebo autorizační token. Možnosti, například rozpoznávání jazyk nebo vlastní koncový bod pro vlastní modely rozpoznávání řeči, můžete také nakonfigurovat v tomto okamžiku.

2. Získání nástroje pro rozpoznávání z objektu pro vytváření. Jsou k dispozici tři různé typy nástroje pro rozpoznávání. Každý typ pro rozpoznávání můžete použít výchozí mikrofon vaše zařízení, zvukové datový proud nebo zvuk ze souboru.

    Rozpoznávání rukopisu | Funkce
    -|-
    Rozpoznávání řeči|Poskytuje text přepis řeči
    Záměrné rozpoznávání rukopisu|Odvozená mluvčího záměr prostřednictvím [LEOŠ](https://docs.microsoft.com/azure/cognitive-services/luis/) po rozpoznávání\*
    Pro překlad rozpoznávání|Přeloží přepisována text, který má jiný jazyk (viz [řeči překlad](how-to-translate-speech.md))

    \* *Pro záměrné rozpoznávání budete muset použít samostatné klíč předplatného LEOŠ při vytváření objekt pro vytváření řeči pro záměrné rozpoznávání rukopisu.*
    
4. V případě potřeby vytížit události pro asynchronní operace. Nástroj pro rozpoznávání pak zavolá vaší obslužné rutiny událostí, pokud má průběžné a konečné výsledky. Aplikace, jinak se zobrazí přepis konečný výsledek.

5. Spusťte rozpoznávání.

### <a name="sdk-samples"></a>Ukázky sady SDK

Nejnovější sadu vzorků, najdete v článku [úložiště GitHub SDK ukázka kognitivní služby řeči](https://aka.ms/csspeech/samples).

## <a name="using-the-rest-api"></a>Pomocí rozhraní REST API

Rozhraní API REST je nejjednodušší způsob, jak rozpoznávat řeč, pokud nepoužíváte sadu SDK podporován. Provedete požadavek HTTP POST koncovému bodu služby předávání celý utterance v textu požadavku. Obdržíte odpověď obsahující rozpoznaný text.

> [!NOTE]
> Utterances jsou omezené na 15 sekund nebo méně, při použití rozhraní REST API.


Další informace o **řeči na Text** REST API najdete v tématu [rozhraní REST API](rest-apis.md#speech-to-text). Nevidíte v akci, stáhněte si [ukázky rozhraní REST API](https://github.com/Azure-Samples/SpeechToText-REST) z Githubu.

## <a name="next-steps"></a>Další postup

- [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
- [Jak rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
