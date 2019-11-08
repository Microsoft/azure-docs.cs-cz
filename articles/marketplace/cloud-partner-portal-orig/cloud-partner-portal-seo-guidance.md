---
title: Pokyny k Azure Marketplace SEO
description: Poskytuje pokyny pro maximalizaci optimalizace vyhledávacích vyhledávačů (SEO).
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: pabutler
ms.openlocfilehash: 7115798faadc3209413d22a384433417ec0ddff0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819584"
---
# <a name="azure-marketplace-seo-guidance"></a>Pokyny k Azure Marketplace SEO

Tento článek vysvětluje, jak maximalizovat zjistitelnost vaší nabídky prostřednictvím funkce hledání v [Azure Marketplace](https://azuremarketplace.microsoft.com) a [AppSource](https://appsource.microsoft.com). 


## <a name="general-explanation-of-algorithm"></a>Obecné vysvětlení algoritmu

Microsoft Marketplace využívá Azure Kognitivní hledání k zajištění možností vyhledávání na webu. Algoritmus je založen na četnosti termínů – inverzní frekvence dokumentů ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). Použije se standardní [analyzátor Lucene](https://lucene.apache.org/core/) .

Obecně platí, že všechna textová pole, kategorie a odvětví jsou zahrnutá do váhy, která je v závažnosti. Specializované výrazy, které se často používají aplikacemi, ale často ve vaší aplikaci, vygenerují vyšší skóre shody s hledáním. Podobně jako "VM" by to vedlo k malým výhodám, protože "Azure Search" by bylo mnohem více specializované.
Níže jsou uvedená nejdůležitější pole, která je potřeba vzít v úvahu.

 
|  Pole                   | Závažnost | Doprovodné materiály                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Název nabídky               |  Vysoký      | Přesné nebo blízko úplné shody s vyhledávacím dotazem bude poskytovat vysoké hodnocení.                       |
| Název vydavatele           |  Vysoký      | Přesné nebo blízko úplné shody s vyhledávacím dotazem bude poskytovat vysoké hodnocení.                       |
| Krátký popis        |  Střednědobé používání    | Vzhledem k tomu, že pojmenovávání názvů aplikací a vydavatelů bude téměř zaručeno vysoké hodnocení, nemusí být relevantní. V tomto případě je krátký popis kritický. Ponechte text stručný a k bodu. K dosažení nejlepšího výsledku by měly být zahrnuty klíčová slova a očekávané hledané výrazy.  Například "Toto je nejlepší maloobchodní POS sestavený plně na Dynamics 365" je méně efektivní než "Retail POS (Point of prodej) pro Dynamics 365".  | 
| Dlouhý popis         |  Nízká       | Popis nabízí způsob, jak přejít do větší hloubky. Nejefektivnější popisy mají rozumnou délku a používají se klíčová slova.  Popisy k bodům pomocí klíčových slov budou využívat více než dlouhého textu, který by měl být dlouhý. Ujistěte se, že se v popisu nacházejí Klíčové výrazy, například "IoT".  |
| Kategorie produktů       | Střednědobé používání     |  Kategorie produktů jsou určeny kombinací možností vydavatele a společnosti Microsoft. Tyto kategorie vyberte odpovídajícím způsobem, aby uživatelé mohli snadno najít aplikace ve správné kategorii. |
|  |  |  |


## <a name="other-tips"></a>Další tipy

-   Při hledání se bude jednat o velkou aktivitu uživatelů. Určuje prioritu shody s názvem aplikace a vydavatelem. Krátký popis se zobrazí jako klíčové pole, pokud hledaný termín není přesná shoda s názvem vydavatele nebo aplikace.
-   Dokumenty ke stažení nejsou zahrnuté do váhy vyhledávání.
-   Skutečné získání a využití vašich aplikací ovlivní také hodnocení vyhledávání. Například dvě ekvivalentní aplikace, kde jedna má příliš velké množství uživatelů, získají vyšší hodnocení.
