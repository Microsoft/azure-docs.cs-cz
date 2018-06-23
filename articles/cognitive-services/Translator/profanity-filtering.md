---
title: Filtrování pomocí rozhraní API služby Microsoft překladač Text vulgárnost | Microsoft Docs
description: Použijte vulgárnost filtrování v rozhraní API služby Microsoft překladač Text.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a7172e1e8aa336c011fb06e93fc5c4b54d26a3cd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342767"
---
# <a name="how-to-add-profanity-filtering-with-the-microsoft-translator-text-api"></a>Postup přidání vulgárnost filtrování pomocí rozhraní API služby Microsoft překladač textu

Za normálních okolností službu překladač zachová vulgárnost, která je k dispozici ve zdroji v překladu. Na úrovni vulgárnost a kontext, který umožňuje slova znevažujícího liší jazykové verze. V důsledku stupeň vulgárnost v jazyce cíl může rozšířit nebo nižší.

Pokud chcete nechcete, aby vulgárnost v překlad (bez ohledu na přítomnost vulgárnost v zdrojový text), můžete použít možnost filtrování vulgárnost v metodě Translate(). Možnost umožňuje vybrat, zda chcete zobrazit vulgárnost odstraněna, nebo pokud označené odpovídající značky nebo nebyla provedena žádná akce.

Metoda Translate() přebírá parametr "možnosti", která obsahuje nového elementu "ProfanityAction". Přijaté hodnoty ProfanityAction jsou "NoAction", "Označené" a "Odstraněno".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Možné hodnoty ProfanityAction a příklady
|Hodnota ProfanityAction | Akce | Příklad: Zdroj - japonština | Příklad: Target – angličtina|
| :---|:---|:---|:---|
| NoAction | Default (Výchozí). Stejné jako nastavení není možnost. Vulgárnost předá ze zdrojové do cílové. | 彼は変態です。 | Se Hlupák. |
| Označit | Znevažujícího slova jsou obklopená značky XML \<vulgárnost >... \</Profanity >. | 彼は変態です。 | Se \<vulgárnost > jerk\</profanity >. |
| Odstraněno | Znevažujícího slova se odeberou z výstupu bez nahrazení. | 彼は。 | Se. |

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Použít vulgárnost filtrování pomocí volání API překladač](reference/v3-0-translate.md)

