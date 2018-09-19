---
title: Filtrování vulgárních výrazů – Translator Text API
titlesuffix: Azure Cognitive Services
description: Použijte vulgárních výrazů v rozhraní Translator Text API filtrování.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 87814571e6f1c20b219020651eb798fa49a28deb
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127923"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Přidat vulgárních výrazů filtrování s rozhraním Translator Text API

Službu Translator obvykle uchovává vulgárních výrazů, který je k dispozici ve zdroji v překladu. Do jaké míry vulgárních výrazů a stejného kontextu, který umožňuje neslušná slova se liší mezi jazykovými verzemi. Stupeň vulgárních výrazů v cílovém jazyce díky tomu mohou rozšířit nebo nižší.

Pokud chcete-li předejít zobrazení vulgárních výrazů v překladu (i když se nachází ve zdrojovém textu vulgárních výrazů), použijte vulgárních výrazů v metodě Translate() možnosti filtrování. Tato možnost umožňuje vybrat, zda chcete zobrazit vulgárních výrazů odstraněn nebo označené odpovídající značky nebo nebyla provedena žádná akce.

Metoda Translate() přijímá parametr "options", který obsahuje nový prvek "ProfanityAction". Přípustné hodnoty ProfanityAction jsou "NoAction," "Označené jako" a "Odstraněno".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Přípustné hodnoty ProfanityAction a příklady
|Hodnota ProfanityAction | Akce | Příklad: Zdroje – japonština | Příklad: Atribut Target – angličtina|
| :---|:---|:---|:---|
| NoAction | Default (Výchozí). Stejně jako nastavit možnost. Vulgárních výrazů předá ze zdroje do cíle. | 彼は変態です。 | Je Hlupák. |
| Označené | Urážlivá slova jsou ohraničeny značky XML \<vulgárních výrazů >... \</Profanity >. | 彼は変態です。 | Je \<vulgárních výrazů > jerk\</profanity >. |
| Odstraněno | Urážlivá slova se odeberou z výstupu bez nahrazení. | 彼は。 | Je. |

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Použít filtrování pomocí vaše volání rozhraní Translator API vulgárních výrazů](reference/v3-0-translate.md)

