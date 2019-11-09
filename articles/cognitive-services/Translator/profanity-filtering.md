---
title: Filtrování vulgárních výrazů – Translator Text API
titleSuffix: Azure Cognitive Services
description: Pomocí filtrování vulgárních výrazů můžete určit úroveň vulgárních výrazů v textu ve službě Azure Cognitive Services Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836230"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Přidání filtrování vulgárních výrazů pomocí Translator Text API

Služba Translator obvykle uchovává vulgární výrazy, které se nacházejí ve zdroji v překladu. Stupeň vulgárních výrazů a kontext, který dává slova neslušně odlišně mezi kulturami. V důsledku toho může být stupeň vulgárních výrazů v cílovém jazyce zesílený nebo omezený.

Pokud chcete zabránit zobrazení vulgárních výrazů v překladu, a to i v případě, že se ve zdrojovém textu vyskytuje vulgární text, použijte možnost filtrování vulgárních výrazů, která je k dispozici v metodě přeložit (). Tato možnost umožňuje zvolit, zda chcete odstranit vulgární výrazy, označené příslušnými značkami nebo Neprovádět žádnou akci.

Metoda přeložit () přebírá parametr options, který obsahuje nový prvek "ProfanityAction". Přijaté hodnoty ProfanityAction jsou "žádná akce", "označeno" a "odstraněno".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Přijaté hodnoty ProfanityAction a příkladů
|Hodnota ProfanityAction | Akce | Příklad: zdroj – japonština | Příklad: cíl – angličtina|
| :---|:---|:---|:---|
| Akce | Default (Výchozí). Stejné jako nastavení možnosti. Vulgární výrazy se předává ze zdroje do cíle. | 彼は変態です Marketplace. | Je Jerk. |
| Vyznačen | Slova v vulgárních výrazech jsou obklopeny značkami XML \<vulgárních výrazů >... \</Profanity >. | 彼は変態です Marketplace. | \<vulgárních výrazů > Jerk\</Profanity >. |
| Odstraněno | Slova v vulgárních textech se z výstupu odeberou bez náhrady. | 彼は Marketplace. | Je. |

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Použití filtrování vulgárních výrazů s voláním rozhraní API pro překladatele](reference/v3-0-translate.md)
