---
title: Inteligentní skupiny
description: Inteligentní skupiny jsou agregace výstrah, které vám pomůžou snížit šum výstrah.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/15/2018
ms.openlocfilehash: 05b05f8bc079bb3768ac2f1a03593bc9260b41aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77665489"
---
# <a name="smart-groups"></a>Inteligentní skupiny

Běžným problémem, se kterým čelíte při práci s výstrahami, je prokládání hluku, aby bylo možné zjistit, co skutečně záleží na tom, jaké má řešení k tomuto problému.  

Inteligentní skupiny se automaticky vytvoří pomocí algoritmů strojového učení ke kombinování souvisejících výstrah, které představují jediný problém.  Když se vytvoří výstraha, algoritmus ji přidá do nové inteligentní skupiny nebo do existující inteligentní skupiny na základě informací, jako jsou historické vzory, podobné vlastnosti a podobná struktura. Pokud je například% CPU na několika virtuálních počítačích v předplatném současně špičky, což vede k mnoha jednotlivým výstrahám, a pokud k takovým výstrahám došlo v minulosti v minulosti, tyto výstrahy budou pravděpodobně seskupeny do jedné inteligentní skupiny a navrhují potenciální běžnou hlavní příčinu. To znamená, že pro uživatele, kteří mají potíže s řešením potíží, můžou inteligentní skupiny nejen snížit šum tím, že spravují související výstrahy jako jednu agregovanou jednotku, ale také je předají k možným běžným hlavním důvodům pro jejich výstrahy.

V současné době algoritmus v rámci předplatného zohledňuje jenom výstrahy ze stejné monitorovací služby. V rámci této konsolidace můžete pomocí inteligentních skupin snížit až 99% hluku výstrahy. Důvod zahrnutí výstrah do skupiny najdete na stránce s podrobnostmi inteligentní skupiny.

Můžete zobrazit podrobnosti inteligentních skupin a nastavit stav podobně jako u výstrah. Každá výstraha je členem jedné a pouze jedné inteligentní skupiny. 

## <a name="smart-group-state"></a>Stav inteligentní skupiny

Stav inteligentní skupiny je podobný koncept stavu výstrahy, který umožňuje správu procesu řešení na úrovni inteligentní skupiny. Podobně jako stav výstrahy, když je vytvořena inteligentní skupina, má **Nový** stav, který lze změnit na buď **potvrzeno** , nebo **Uzavřeno**.

Podporovány jsou následující stavy inteligentních skupin.

| State | Popis |
|:---|:---|
| Nová | Problém se právě zjistil a ještě není zkontrolovaný. |
| Potvrzeno | Správce zkontroloval tuto inteligentní skupinu a začal na ní pracovat. |
| Uzavřená | Problém byl vyřešen. Po zavření čipové skupiny ji můžete znovu otevřít změnou do jiného stavu. |

[Zjistěte, jak změnit stav vaší inteligentní skupiny.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Změna stavu inteligentní skupiny nemění stav výstrah jednotlivých členů.

## <a name="smart-group-details-page"></a>Stránka podrobností inteligentní skupiny

Stránka s podrobnostmi inteligentní skupiny se zobrazí, když vyberete inteligentní skupinu. Poskytuje podrobnosti o inteligentní skupině, včetně důvodů, které bylo použito k vytvoření skupiny, a umožňuje změnit její stav.
 
![Podrobnosti inteligentní skupiny](media/alerts-smartgroups-overview/smart-group-detail.png)


Stránka podrobností inteligentní skupiny obsahuje následující části.

| Sekce | Popis |
|:---|:---|
| Výstrahy | Zobrazí seznam jednotlivých výstrah, které jsou zahrnuty v inteligentní skupině. Výběrem výstrahy otevřete stránku s podrobnostmi o výstrahách. |
| Historie | Zobrazí všechny akce prováděné čipovou skupinou a veškeré změny, které jsou v něm provedeny. V současné době je omezen na změny stavu a změny členství v výstrahách. |

## <a name="smart-group-taxonomy"></a>Taxonomie inteligentních skupin

Název inteligentní skupiny je název první výstrahy. Nemůžete vytvořit nebo přejmenovat inteligentní skupinu.

## <a name="next-steps"></a>Další kroky

- [Správa inteligentních skupin](https://aka.ms/managing-smart-groups)
- [Změna upozornění a stavu inteligentní skupiny](https://aka.ms/managing-alert-smart-group-states)


