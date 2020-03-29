---
title: Filtrování vulgárních výrazů - Překladač Text API
titleSuffix: Azure Cognitive Services
description: Filtrování vulgárních výrazů slouží k určení úrovně vulgárních výrazů přeložených v textu v textovém rozhraní Azure Cognitive Services Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836230"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Přidání filtrování vulgárních výrazů pomocí rozhraní Translator Text API

Za normálních okolností překladatel služby zachovává vulgární výrazy, které je k dispozici ve zdroji v překladu. Stupeň vulgárních výrazů a kontext, který dělá slova profánní se liší mezi kulturami. V důsledku toho může být stupeň vulgárních výrazů v cílovém jazyce zesílen nebo snížen.

Pokud se chcete vyhnout zobrazení vulgárních výrazů v překladu, i když je vulgární výraz y k dispozici ve zdrojovém textu, použijte možnost filtrování vulgárních výrazů, která je k dispozici v metodě Translate(). Tato možnost umožňuje zvolit, zda chcete zobrazit vulgární výrazy odstraněné, označené příslušnými značkami nebo neprovést žádnou akci.

Translate() Metoda trvá "možnosti" parametr, který obsahuje nový prvek "Vulgární akce". Přijaté hodnoty Vulgární akce jsou "NoAction", "Označeno" a "Odstraněno".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Přijaté hodnoty Vulgární akce a příklady
|Hodnota Vulgární akce | Akce | Příklad: Zdroj - japonština | Příklad: Target - angličtina|
| :---|:---|:---|:---|
| NoAkce | Default (Výchozí). Stejné jako nenastavovat možnost. Vulgární výrazy přechází ze zdroje na cíl. | V ěn | Je to blbec. |
| Označeny | Vulgární slova jsou obklopena XML \<tagy vulgárních výrazů> ... \</vulgární výrazy>. | V ěn | On je \<vulgární>blbec\</ vulgární>. |
| Odstranění | Vulgární slova jsou odstraněna z výstupu bez náhrady. | V roce 2017 | On je. |

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Použití filtrování vulgárních výrazů při volání rozhraní API překladače](reference/v3-0-translate.md)
