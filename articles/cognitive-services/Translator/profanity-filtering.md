---
title: Filtrování vulgárních výrazů – Translator Text API
titleSuffix: Azure Cognitive Services
description: Použijte filtrování vulgárních výrazů v Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 114dea098db5c824a1235ba1635f547383bf6743
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595190"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Přidání filtrování vulgárních výrazů pomocí Translator Text API

Služba Translator obvykle uchovává vulgární výrazy, které se nacházejí ve zdroji v překladu. Stupeň vulgárních výrazů a kontext, který dává slova neslušně odlišně mezi kulturami. V důsledku toho může být stupeň vulgárních výrazů v cílovém jazyce zesílený nebo omezený.

Pokud chcete zabránit zobrazení vulgárních výrazů v překladu, a to i v případě, že se ve zdrojovém textu vyskytuje vulgární text, použijte možnost filtrování vulgárních výrazů, která je k dispozici v metodě přeložit (). Tato možnost umožňuje zvolit, zda chcete odstranit vulgární výrazy, označené příslušnými značkami nebo Neprovádět žádnou akci.

Metoda přeložit () přebírá parametr options, který obsahuje nový prvek "ProfanityAction". Přijaté hodnoty ProfanityAction jsou "žádná akce", "označeno" a "odstraněno".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Přijaté hodnoty ProfanityAction a příkladů
|Hodnota ProfanityAction | Action | Příklad: Zdroj – japonština | Příklad: Cíl – angličtina|
| :---|:---|:---|:---|
| NoAction | Default (Výchozí). Stejné jako nastavení možnosti. Vulgární výrazy se předává ze zdroje do cíle. | 彼は変態です。 | Je Jerk. |
| Vyznačen | Slova v vulgárních výrazech jsou obklopená \<vulgárními značkami XML >... \</Profanity >. | 彼は変態です。 | Je to \<vulgární > Jerk\</Profanity >. |
| Odstraněné | Slova v vulgárních textech se z výstupu odeberou bez náhrady. | 彼は Marketplace. | Je. |

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Použití filtrování vulgárních výrazů s voláním rozhraní API pro překladatele](reference/v3-0-translate.md)
