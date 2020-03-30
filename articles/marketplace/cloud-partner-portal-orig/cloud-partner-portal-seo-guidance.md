---
title: Pokyny pro SEO Azure Marketplace
description: Obsahuje pokyny k maximalizaci optimalizace pro vyhledávače (SEO).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: dsindona
ms.openlocfilehash: 761cdc2233bce3619d4c2c9ce1d7d7177d3bc407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280146"
---
# <a name="azure-marketplace-seo-guidance"></a>Pokyny pro SEO Azure Marketplace

Tento článek vysvětluje, jak maximalizovat zjistitelnost nabídky prostřednictvím funkce vyhledávání na [Azure Marketplace](https://azuremarketplace.microsoft.com) a [AppSource](https://appsource.microsoft.com). 


## <a name="general-explanation-of-algorithm"></a>Obecné vysvětlení algoritmu

Tržiště Microsoftu využívají Azure Cognitive Search k napájení možností vyhledávání na webu. Algoritmus je založen na termínu frekvence-inverzní frekvence dokumentu frekvence ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). Používá se standardní [analyzátor Lucene.](https://lucene.apache.org/core/)

Obecně platí, že všechna textová pole, kategorie a odvětví a zahrnutá do vážení relevance. Specializované termíny, které se používají zřídka aplikace, ale často ve vaší aplikaci, vygenerují vyšší skóre shody při vyhledávání. Takže včetně termínů jako "VM" by nabídnout malou výhodu vzhledem k tomu, "Azure search" by bylo mnohem více specializované.
Níže jsou uvedeny nejrelevantnější pole, která je třeba zvážit.

 
|  Pole                   | Důležitost | Doprovodné materiály                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Název nabídky               |  Vysoká      | Přesná nebo blízká úplné shodě s vyhledávacím dotazem přinese vysoké hodnocení.                       |
| Název vydavatele           |  Vysoká      | Přesná nebo blízká úplné shodě s vyhledávacím dotazem přinese vysoké hodnocení.                       |
| Krátký popis        |  Střednědobé používání    | Vzhledem k tomu, pojmenování aplikací a jmen vydavatelů bude téměř zaručit vysoké hodnocení, nemusí být nejrelevantnější. V tomto případě je kritický krátký popis. Udržujte text výstižně a k věci. Pro dosažení nejlepšího výsledku by měla být zahrnuta klíčová slova a očekávané hledané výrazy.  Například "Toto je nejlepší retailový pos plně postavený nad dynamics 365" je méně efektivní než "Retail POS (prodejní místo) pro Dynamics 365".  | 
| Dlouhý popis         |  Nízká       | Popis nabízí způsob, jak jít do větší hloubky. Nejúčinnější popisy mají přiměřenou délku a používají se klíčová slova.  Popisy, které se používají ke klíčovým slovům, budou přínosem pro více než dlouhý a zdlouhavý text. Ujistěte se, že klíčové termíny, jako je "IoT", jsou k dispozici v popisu.  |
| Kategorie produktů       | Střednědobé používání     |  Kategorie produktů jsou určeny kombinací možností vydavatele a společnosti Microsoft. Vyberte tyto kategorie odpovídajícím způsobem, aby uživatelé mohli snadno najít aplikace ve správné kategorii. |
|  |  |  |


## <a name="other-tips"></a>Další tipy

-   Vyhledávání naznačuje, že se dostane k velké aktivitě uživatelů. Upřednostňuje shody podle názvu aplikace nebo vydavatele. Krátký popis se stane klíčovým polem, pokud hledaný výraz není přesnou shodou s názvem vydavatele/aplikace.
-   Dokumenty ke stažení nejsou zahrnuty v hledání hmotnosti.
-   Vaše aplikace skutečné akvizice a využití bude mít vliv na vyhledávání pořadí stejně. Například dvě ekvivalentní aplikace, kde má jeden mnohem více uživatelů, získají vyšší hodnocení.
