---
title: Filtrování vulgárních výrazů – Translator
titleSuffix: Azure Cognitive Services
description: Pomocí filtrování vulgárních výrazů můžete určit úroveň vulgárních výrazů v textu ve službě Azure Cognitive Services Translator.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 9f389d669e69dbfa6ec6d4d0b4716d2367443f17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896795"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Přidání filtrování vulgárních výrazů pomocí překladatele

Služba Translator obvykle uchovává vulgární výrazy, které se nacházejí ve zdroji v překladu. Stupeň vulgárních výrazů a kontext, který dává slova neslušně odlišně mezi kulturami. V důsledku toho může být stupeň vulgárních výrazů v cílovém jazyce zesílený nebo omezený.

Pokud chcete zabránit zobrazení vulgárních výrazů v překladu, a to i v případě, že se ve zdrojovém textu vyskytuje vulgární text, použijte možnost filtrování vulgárních výrazů, která je k dispozici v metodě přeložit (). Tato možnost umožňuje zvolit, zda chcete odstranit vulgární výrazy, označené příslušnými značkami nebo Neprovádět žádnou akci.

Metoda přeložit () přebírá parametr options, který obsahuje nový prvek "ProfanityAction". Přijaté hodnoty ProfanityAction jsou "žádná akce", "označeno" a "odstraněno".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Přijaté hodnoty ProfanityAction a příkladů
|Hodnota ProfanityAction | Akce | Příklad: zdroj – japonština | Příklad: cíl – angličtina|
| :---|:---|:---|:---|
| Akce | Default (Výchozí). Stejné jako nastavení možnosti. Vulgární výrazy se předává ze zdroje do cíle. | 彼は変態です Marketplace. | Je Jerk. |
| Vyznačen | Slova v vulgárních výrazech jsou obklopena značkami XML \<profanity> ... \</profanity> . | 彼は変態です Marketplace. | Je \<profanity> Jerk \</profanity> . |
| Odstraněné | Slova v vulgárních textech se z výstupu odeberou bez náhrady. | 彼は Marketplace. | Je. |

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Použití filtrování vulgárních výrazů u volání překladatele](reference/v3-0-translate.md)
