---
title: Inteligentní skupiny
description: Inteligentní skupiny se, že agregace výstrah, které vám pomůžou omezily rušivé výstrahy
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: e0bef0fc4f4b61add24c243af0dac64933ad5bab
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54466906"
---
# <a name="smart-groups"></a>Inteligentní skupiny
Běžné challenge čelí při práci s výstrahami je procházení šumu a zjistěte, co skutečně záleží – inteligentní skupin by měla být řešení tohoto problému.  

Inteligentní skupiny jsou automaticky vytvořené využitím algoritmů strojového učení kombinování souvisejících výstrah, které představují jeden problém.  Když se vytvoří výstrahu, algoritmus ji přidá do novou inteligentní skupinu nebo existující inteligentní skupinu na základě informací, jako je například historické vzory, podobně jako vlastnosti a podobné struktury. Například, pokud % využití procesoru na několika virtuálních počítačích v rámci předplatného současně špičky, což vede k více jednotlivých výstrah a pokud takové oznámení došlo k společně kdykoli v minulosti, budou tyto výstrahy pravděpodobně seskupeny do jedné skupiny Smart, navrhněte potenciální původní příčiny běžných. To znamená, že pro uživatele, řešení potíží s upozorněními, inteligentní skupiny nejen jim umožňuje snížení šumu pomocí správy souvisejících výstrah jako jeden celek agregované, také provede jejich směrem možný, které způsobí, že společný kořen pro výstrahy.

V současné době algoritmus uvažuje pouze výstrahy ze stejné služby monitorování v rámci předplatného. Inteligentní skupiny můžete snížit až 99 % rušivé výstrahy prostřednictvím tohoto sloučení. Můžete prohlédnout příčinu výstrahy byly součástí skupiny na stránce podrobností inteligentní skupinu.

Můžete zobrazit podrobnosti o inteligentní skupiny a nastavit stav podobně jako na tom, jak s výstrahami. Každá výstraha je členem skupiny inteligentní jeden a pouze jeden. 

## <a name="smart-group-state"></a>Stav inteligentní skupiny
Stav inteligentní skupiny je podobný koncept na stav výstrahy, které vám umožní spravovat procesu překladu, který na úrovni inteligentní skupinu. Podobně jako stav výstrahy, když se vytvoří inteligentní skupinu, má **nový** stavu, které může být změněno buď **potvrzeno** nebo **uzavřeno**.

Jsou podporovány následující stavy inteligentní skupinu.

| Stav | Popis |
|:---|:---|
| Nová | Problém právě byl zjištěn a není zatím Nezkontrolováno. |
| Potvrzeno | Správce zkontroluje inteligentní skupinu a začnete pracovat na ní. |
| Zavřeno | Tento problém byl vyřešen. Po zavření inteligentní skupiny můžete znovu otevřít tak, že změníte do jiného stavu. |

[Zjistěte, jak změnit stav inteligentní skupinu.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Změna stavu inteligentní skupiny nezmění stav výstrahy jednotliví členové.

## <a name="smart-group-details-page"></a>Stránce s podrobnostmi o inteligentní skupiny

Když vyberete inteligentní skupinu, zobrazí se stránka podrobností inteligentní skupinu. Poskytuje podrobnosti o inteligentní skupiny, včetně odůvodnění, která byla použita k vytvoření skupiny a umožňuje změnit její stav.
 
![Inteligentní skupiny podrobností](media/alerts-smartgroups-overview/smart-group-detail.png)


Stránka s podrobnostmi inteligentní skupiny obsahuje následující části.

| Sekce | Popis |
|:---|:---|
| Výstrahy | Zobrazí seznam jednotlivých výstrah, které jsou součástí inteligentní skupinu. Vyberte výstrahu a otevřete její stránku podrobností výstrahy. |
| Historie | Obsahuje všechny akce podniknuté inteligentní skupinou a změnách, které se provedly. Toto je aktuálně omezená na změny stavu a výstrahy členství. |

## <a name="smart-group-taxonomy"></a>Inteligentní skupinu taxonomie

Název inteligentní skupiny je název své první výstraha. Nelze vytvořit ani přejmenovat inteligentní skupinu.

## <a name="next-steps"></a>Další postup

- [Spravovat inteligentní skupiny](https://aka.ms/managing-smart-groups)
- [Změna stavu výstrah a inteligentní skupinu](https://aka.ms/managing-alert-smart-group-states)


