---
title: O převod textu na řeč | Microsoft Docs
description: Přehled možností převod textu na řeč.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 84baf03c83bb63883b80982056cdf6e1e25b3fb7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343781"
---
# <a name="about-the-text-to-speech-api"></a>O převod textu na řeč rozhraní API

**Převod textu na řeč** (převod textu na ŘEČ) rozhraní API služby řeči převede vstupního textu přirozený zní řeči (také nazývané *řeči souhrnnou*).

Pokud chcete vygenerovat rozpoznávání řeči, vaše aplikace odešle požadavky HTTP POST do služby řeči. Zde je text syntetizován do lidské zní řeči a byla vrácena jako zvukový soubor. Celou řadu hlasy a jazyky jsou podporovány.

Mezi scénáře, ve které řeči souhrnnou přijat patří:

* *Zlepšení dostupnosti:* **převod textu na řeč** technologie umožňuje vlastníci obsahu a vydavatelé reagovat na lidi, různé způsoby interakci s jejich obsah. Osoby s visual poškození nebo vyřešit potíže se čtení oceníte, schopnost zvukově využívat obsah. Hlasový výstup také usnadňuje uživatelům získejte textový obsah, například novinových článcích nebo blogy na mobilních zařízeních při dojíždění nebo výkonu.

* *Odpovídá ve scénářích multitasking:* **převod textu na řeč** umožňuje uživatelům rychle a pohodlně pojmout důležité informace při řízení nebo jinak mimo pohodlnou čtení prostředí. Navigace je běžné aplikace v této oblasti. 

* *Rozšíření learning s několika režimy:* jiné osoby další nejvhodnější různými způsoby. Online učení odborníky ukázaly, že poskytování hlasové a text společně vám pomohou lépe informace a další zachovat.

* *Doručování intuitivní robotů nebo Pomocníci:* komunikace může být nedílnou součástí inteligentního chat robota nebo virtuální pomocníka. Více společností vývoji chat robotů zajistit nestačí, aby zákazník služby prostředí pro zákazníků. Hlasové přidá jinou dimenzi tím, že robota odpovědi k přijetí zvukově (například po telefonu).

## <a name="voice-support"></a>Podpora hlasu

Microsoft **převod textu na řeč** služby nabízí více než 75 hlasy ve více než 45 jazyky a národní prostředí. Pokud chcete použít tyto standardní "hlas písem", stačí zadat název hlasové s několik dalších parametrů, při volání rozhraní API služby REST. Podrobnosti o hlasy podporovány, naleznete v části [podporované jazyky](supported-languages.md). 

Pokud chcete jedinečný hlasu pro vaši aplikaci, můžete vytvořit [vlastní hlasové písem](how-to-customize-voice-font.md) z vlastní řeči ukázky.

## <a name="next-steps"></a>Další postup

* [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Informace o tom, rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
