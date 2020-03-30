---
title: Inteligentní skupiny
description: Inteligentní skupiny jsou agregace výstrah, které vám pomohou snížit výstražný šum
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/15/2018
ms.openlocfilehash: 05b05f8bc079bb3768ac2f1a03593bc9260b41aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665489"
---
# <a name="smart-groups"></a>Inteligentní skupiny

Společnou výzvou, které čelíme při vyřizování výstrah, je prosévání hluku, aby se zjistilo, na čem skutečně záleží - inteligentní skupiny mají být řešením tohoto problému.  

Inteligentní skupiny se automaticky vytvářejí pomocí algoritmů strojového učení ke kombinování souvisejících výstrah, které představují jeden problém.  Když je vytvořena výstraha, algoritmus ji přidá do nové inteligentní skupiny nebo existující inteligentní skupiny na základě informací, jako jsou historické vzory, podobné vlastnosti a podobnou strukturu. Pokud například % procesoru na několika virtuálních počítačích v předplatném současně špičky vedoucí k mnoha jednotlivých výstrah, a pokud tyto výstrahy došlo společně kdykoli v minulosti, budou tyto výstrahy pravděpodobně seskupeny do jedné inteligentní skupiny, což naznačuje, potenciální společnou hlavní příčinu. To znamená, že pro někoho, kdo řeší výstrahy, inteligentní skupiny nejen umožňuje snížit hluk tím, že spravuje související výstrahy jako jednu agregovnou jednotku, ale také vede je k možným běžným příčinám jejich výstrah.

V současné době algoritmus bere v úvahu pouze výstrahy ze stejné služby monitorování v rámci předplatného. Inteligentní skupiny mohou touto konsolidací snížit až 99 % výstražného šumu. Důvod, proč byly výstrahy zahrnuty do skupiny, můžete zobrazit na stránce s podrobnostmi o inteligentní skupině.

Můžete zobrazit podrobnosti o inteligentních skupinách a nastavit stav podobně jako pomocí výstrah. Každá výstraha je členem jedné a pouze jedné inteligentní skupiny. 

## <a name="smart-group-state"></a>Stav inteligentní skupiny

Stav inteligentní skupiny je podobný koncept stavu výstrahy, který umožňuje spravovat proces řešení na úrovni inteligentní skupiny. Podobně jako stav výstrahy, když je vytvořena inteligentní skupina, má **nový** stav, který lze změnit na **Potvrzeno** nebo **Uzavřeno**.

Podporovány jsou následující stavy inteligentní skupiny.

| Stav | Popis |
|:---|:---|
| Nová | Problém byl právě zjištěn a dosud nebyl přezkoumán. |
| Potvrzeno | Správce inteligentní skupinu zkontroloval a začal na ní pracovat. |
| Zavřeno | Problém byl vyřešen. Po zavření inteligentní skupiny ji můžete znovu otevřít změnou do jiného stavu. |

[Přečtěte si, jak změnit stav vaší chytré skupiny.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Změna stavu inteligentní skupiny nezmění stav jednotlivých výstrah členů.

## <a name="smart-group-details-page"></a>Stránka s podrobnostmi o inteligentní skupině

Stránka podrobností inteligentní skupiny se zobrazí, když vyberete inteligentní skupinu. Obsahuje podrobnosti o inteligentní skupině, včetně odůvodnění, které bylo použito k vytvoření skupiny, a umožňuje změnit její stav.
 
![Detail inteligentní skupiny](media/alerts-smartgroups-overview/smart-group-detail.png)


Stránka s podrobnostmi o inteligentní skupině obsahuje následující části.

| Sekce | Popis |
|:---|:---|
| Výstrahy | Zobrazí seznam jednotlivých výstrah, které jsou zahrnuty v inteligentní skupině. Výběrem výstrahy otevřete stránku s podrobnostmi výstrahy. |
| Historie | Zobrazí seznam jednotlivých akcí provedených inteligentní skupinou a všech změn, které jsou v ní provedeny. To je v současné době omezena na změny stavu a upozornit změny členství. |

## <a name="smart-group-taxonomy"></a>Inteligentní skupinová taxonomie

Název inteligentní skupiny je název první výstrahy. Inteligentní skupinu nelze vytvořit ani přejmenovat.

## <a name="next-steps"></a>Další kroky

- [Správa inteligentních skupin](https://aka.ms/managing-smart-groups)
- [Změna stavu výstrahy a inteligentní skupiny](https://aka.ms/managing-alert-smart-group-states)


