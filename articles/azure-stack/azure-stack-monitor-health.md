---
title: Sledování stavu a výstrahy v zásobníku Azure | Microsoft Docs
description: Naučte se monitorovat stav a výstrahy v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: mabrigg
ms.openlocfilehash: e20670476f4549a06d80d5f85e2ebd8ab1f50b05
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047237"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Sledování stavu a výstrahy v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Azure Stack zahrnuje možnosti, které vám umožní zobrazit stav a výstrahy pro oblast Azure zásobníku monitorování infrastruktury. **Oblast správy** dlaždice, připnuté ve výchozím nastavení v portálu správce pro předplatné výchozího zprostředkovatele, obsahuje seznam všech nasazených oblastech Azure zásobníku. Na dlaždici znázorňuje počet aktivních výstrah kritických a výstražných pro každou oblast a je vašim vstupním bodem do stavu a výstrah funkce Azure zásobníku.

 ![Na dlaždici správy oblast](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Pochopení stavu v Azure zásobníku

 Stav a výstrahy spravuje poskytovatel prostředků stavu. Součásti infrastruktury Azure zásobníku zaregistrovat u zprostředkovatele stavu prostředků během nasazení Azure zásobníku a konfigurace. Tato registrace umožňuje zobrazení stavu a výstrahy pro každou součást. Stav v zásobníku Azure je jednoduchý koncept. Pokud existují výstrahy pro registrovanou instanci součásti, stav této součásti odráží nejhorší active závažnost výstrahy; upozornění nebo kritickou.

## <a name="alert-severity-definition"></a>Definice závažnost výstrahy

V zásobníku Azure jsou vyvolány výstrahy s pouze dvěma závažnosti: **upozornění** a **kritické**.

**upozornění**  
Operátor můžete vyřešit výstražné upozornění naplánované způsobem. Výstraha obvykle nemá negativní vliv na zatížení uživatele.

**Kritické**  
Operátor je potřeba se naléhavost vyřešit kritickou výstrahu. Toto jsou problémy, které aktuálně ovlivnit nebo brzy ovlivní uživatele Azure zásobníku. 

 
 ## <a name="view-and-manage-component-health-state"></a>Zobrazit a spravovat stav součásti
 
 Jako operátor zásobník Azure můžete zobrazit stav komponent na portálu pro správce a prostřednictvím REST API a prostředí PowerShell.
 
Chcete-li zobrazit stav na portálu, klikněte na tlačítko oblast, kterou chcete zobrazit v **oblast správy** dlaždici. Můžete zobrazit stav infrastruktury rolí a zprostředkovatele prostředků.

![Seznam rolí infrastruktury](media/azure-stack-monitor-health/image2.png)

Můžete kliknout na poskytovatele prostředků nebo role infrastruktury, chcete-li zobrazit podrobnější informace.

> [!WARNING]
>Pokud klikněte roli infrastruktury a pak klikněte na instanci role, nejsou možnosti spuštění, restartování nebo vypnutí. Při použití aktualizací pro integrovaný systém, nepoužívejte tyto akce. Kromě toho proveďte **není** pomocí těchto možností v prostředí Azure zásobníku Development Kit. Tyto možnosti jsou určeny pouze pro prostředí, integrované systémy, kde existuje více než jedna instance role na role infrastruktury. Restartování instance role (zejména AzS-Xrp01) v sadě pro vývoj způsobí, že nestabilitě systému. Pomoc při řešení potíží, post váš problém [fórum Azure zásobníku](https://aka.ms/azurestackforum).
>
 
## <a name="view-alerts"></a>Zobrazení výstrah

Seznam aktivních výstrah pro každou oblast Azure zásobník je k dispozici přímo z **oblast správy** okno. První dlaždice ve výchozím nastavení je **výstrahy** dlaždice, který zobrazuje souhrn důležité a výstrahy varování pro oblast. Budete moct připnout dlaždice výstrahy, jako ostatní dlaždice v tomto okně na řídicí panel pro rychlý přístup.   

![Výstrahy dlaždici, která ukazuje varování](media/azure-stack-monitor-health/image3.png)

Výběrem horní část **výstrahy** dlaždice, přejdete do seznamu všechny aktivní výstrahy pro oblast. Pokud vyberete buď **kritický** nebo **upozornění** řádku položky v rámci dlaždice, přejděte na filtrovaný seznam výstrah (kritický nebo upozornění). 

![Filtrované varovné výstrahy.](media/azure-stack-monitor-health/image4.png)
  
**Výstrahy** okno podporuje možnost filtrování stavu (aktivní nebo zavřené) a závažnosti (kritický nebo upozornění). Výchozí zobrazení se zobrazí všechny aktivní výstrahy. Všechny výstrahy uzavřené se odeberou ze systému po sedmi dnech.

![Podokno filtru filtrovat podle kritický nebo stav varování](media/azure-stack-monitor-health/image5.png)

**Zobrazení rozhraní API** akce zobrazí rozhraní REST API, která se používá ke generování zobrazení seznamu. Tato akce poskytuje rychlý způsob, jak se seznámit se syntaxí REST API, který můžete použít k dotazu výstrahy. Toto rozhraní API můžete použít ve službě automation, nebo pro integraci s vaší existující datacenter monitorování, vytváření sestav a lístků pro řešení. 

![Možnost zobrazení rozhraní API, která zobrazuje rozhraní REST API](media/azure-stack-monitor-health/image6.png)

Můžete kliknout na konkrétní výstrahu a prohlédněte si podrobnosti výstrahy. Detaily výstrahy zobrazují všechna pole, které jsou spojeny s výstrahou a povolit rychlé navigace na ovlivněných komponentu a zdroj výstrahy. Například následující výstraha vyskytuje, pokud jedna z instancí role infrastruktury přejde do režimu offline nebo není přístupná.  

![V okně podrobností výstrahy](media/azure-stack-monitor-health/image7.png)

Po instance role infrastruktury je zpět do režimu online, tato výstraha se automaticky zavře. Mnoho, ale ne každá výstraha automaticky zavře základní problém nebude vyřešen. Doporučujeme vám, že jste vybrali **zavřít výstrahu** po provedení kroků nápravu. Pokud potíže potrvají, zásobník Azure generuje novou výstrahu. Pokud problém vyřešit, výstrahu zůstává uzavřený a nevyžaduje žádné další akce.

## <a name="next-steps"></a>Další postup

[Správa aktualizací v Azure zásobníku](azure-stack-updates.md)

[Oblast správy v Azure zásobníku](azure-stack-region-management.md)
