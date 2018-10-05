---
title: Příručka pro vydavatele na Azure Marketplace optimalizace pro vyhledávací weby | Dokumentace Microsoftu
description: Obsahuje pokyny k maximalizaci optimalizace pro vyhledávací weby (SEO).
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: b8ca5fc5348818a7d0f1075557d4fe5c7e3bef44
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809321"
---
<a name="azure-marketplace-seo-publisher-guide"></a>Příručka pro vydavatele na Azure Marketplace optimalizace pro vyhledávací weby
=======================================

### <a name="general-explanation-of-algorithm"></a>Obecné vysvětlení algoritmu.

Na webu marketplace využívá Azure Search pro provozování možnosti vyhledávání webu. Algoritmus je založen na termín frekvence – inverzní dokumentu frekvence ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). Standardní [analyzátor Lucene](http://lucene.apache.org/core/) se používá.

Obecně platí veškerý text pole, kategorie a odvětví a zahrnut do weightage význam. Specializované podmínky, které nejsou používány často aplikací, ale často ve vaší aplikaci bude generovat vyšší ohodnocení shoda s vyhledávání. Proto včetně jako "Virtuální počítač" by nabízejí málo výhodné, že "Služba Azure search" by mnohem více specializovaný.
Níže jsou uvedeny nejdůležitější pole vzít v úvahu.

 
|  Pole                   | Důležitost | Doprovodné materiály                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Název nabídky               |  Vysoký      | Přesné nebo blízko úplnou shodu s vyhledávací dotaz získáte vysoké hodnocení.                       |
| Název vydavatele           |  Vysoký      | Přesné nebo blízko úplnou shodu s vyhledávací dotaz získáte vysoké hodnocení.                       |
| Krátký popis        |  Střednědobé používání    | Zadané názvy aplikací a vydavatel názvy téměř zaručí vysoké hodnocení, nemusí být nejdůležitější. V tomto případě je důležité krátký popis. Zachovat text, stručné a výstižně. Klíčová slova a očekávané hledané termíny by měly být zahrnuty pro nejlepší výsledek.  Například "Toto je nejlepší maloobchodní POS plně postavené na Dynamics 365" je méně efektivní než "Retail POS (point prodeje) pro Dynamics 365".  | 
| Dlouhý popis         |  Nízká       | Popis nabízí způsob, jak do větší hloubky. Nejúčinnější popisy jsou přiměřené a klíčová slova jsou používány.  Do bodu popisy pomocí klíčových slov bude přínosem pro více než dlouho dlouhý text. Zkontrolujte že klíčových pojmů, jako je například "IoT", jsou k dispozici v popisu.  |
| Kategorie produktů       | Střednědobé používání     |  Kategorie produktů jsou určeny kombinaci možností vydavatele a Microsoftu. Správný výběr těchto kategorií tak, aby uživatelé snadno najít aplikace správnou kategorii. |
|  |  |  |


### <a name="other-tips"></a>Další tipy

-   Hledání navrhuje aktivity získá náročné uživatelů. Upřednostňuje shody názvu nebo vydavatele aplikace. Krátký popis stane pole klíče pro Pokud hledaný termín není přesná shoda s názvem vydavatele nebo aplikaci.
-   Dokumenty, kde stahování nejsou součástí weightage vyhledávání.
-   Vaše aplikace skutečný pořízení a použití bude mít vliv i pořadí hledání. Například dva ekvivalentní aplikací, kde jeden má výrazně více uživatelů získají vyšší pořadí.
