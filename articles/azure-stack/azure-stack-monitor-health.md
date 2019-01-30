---
title: Monitorování stavu a výstrah ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o monitorování stavu a výstrah ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 3e37b8f45d7068586d20b9c4b3ccdabb017d0416
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242854"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Monitorování stavu a výstrah ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Azure Stack zahrnuje monitorování infrastruktury, které vám zobrazení stavu a výstrahy pro určitá oblast Azure Stack. **Správa oblastí** dlaždice připnuté v portálu správce ve výchozím nastavení pro výchozí předplatné poskytovatele, obsahuje seznam všech nasazených oblastech služby Azure Stack. Na dlaždici se zobrazuje počet aktivní kritických a výstražných oznámení pro každou oblast. Tato dlaždice je vaším vstupním bodem do stavu a výstrah funkce služby Azure Stack.

![Dlaždice Správa oblastí](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack"></a>Pochopení stavu ve službě Azure Stack

Stav poskytovatele prostředků spravuje stav a výstrahy. Komponenty infrastruktury Azure stacku zaregistrujte poskytovatele prostředků stavu během nasazení Azure stacku a konfigurace. Díky této registraci může zobrazovat stav a výstrahy pro jednotlivé komponenty. Stav ve službě Azure Stack je jednoduchý koncept. Pokud existují výstrahy pro registrované instance komponenty, stav této součásti odráží nejhorší aktivní závažnost výstrahy: varování nebo kritické.

## <a name="alert-severity-definition"></a>Definice závažnost výstrahy

Ve službě Azure Stack vyvolá výstrahy s pouze dvěma závažnosti: **upozornění** a **kritické**.

- **Upozornění**  
  Operátor může vyřešit výstražné upozornění naplánované způsobem. Upozornění obvykle nemá vliv na zatížení uživatele.

- **Kritická**  
  Operátor by měl se naléhavost zabývat kritickou výstrahu. Toto jsou problémy, které aktuálně ovlivnit nebo brzy ovlivní uživatele Azure stacku.


## <a name="view-and-manage-component-health-state"></a>Umožňuje zobrazit a spravovat stav součásti

Na portálu pro správce a prostřednictvím rozhraní REST API a Powershellu můžete zobrazit stav komponent.

Chcete-li zobrazit stav na portálu, klikněte na tlačítko, který chcete zobrazit v oblasti **Správa oblastí** dlaždici. Můžete zobrazit stav infrastruktury rolí a poskytovatelů prostředků.

![Seznam rolí infrastruktury](media/azure-stack-monitor-health/image2.png)

Můžete kliknout na poskytovatele prostředků nebo infrastrukturu role, chcete-li zobrazit podrobnější informace.

> [!WARNING]  
> Pokud klikněte na roli infrastrukturu a pak klikněte na instanci role, existují možnosti **Start**, **restartovat**, nebo **vypnutí**. Nepoužívejte tyto akce, při použití aktualizací na integrovaný systém. Také proveďte **není** tyto možnosti použít v prostředí Azure Stack Development Kit. Tyto možnosti jsou určeny pouze pro integrované systémy pro prostředí, ve kterých je více než jednu instanci role na roli serveru infrastruktury. Restartování instance role (zejména AzS-Xrp01) development Kit způsobí, že nestabilitu systému. Pomoc při řešení potíží, odeslat váš problém [fórum pro Azure Stack](https://aka.ms/azurestackforum).
>

## <a name="view-alerts"></a>Zobrazení upozornění

Seznam aktivních výstrah pro každou oblast Azure Stack je k dispozici přímo **Správa oblastí** okno. První dlaždice ve výchozím nastavení je **výstrahy** dlaždici, která zobrazuje souhrn důležité a upozorňující výstrahy pro oblast. Můžete připnout dlaždici výstrahy, stejně jako libovolnou dlaždici na toto okno na řídicí panel pro rychlý přístup.

![Dlaždice zobrazující upozornění upozornění](media/azure-stack-monitor-health/image3.png)

Výběrem horní část **výstrahy** dlaždice, přejděte na seznam všech aktivních výstrah pro danou oblast. Pokud vyberete buď **kritický** nebo **upozornění** položky řádku v bloku, přejděte na filtrovaný seznam výstrah (kritický nebo upozornění). 

**Výstrahy** okna podporuje možnost filtrovat ve stavu (aktivní nebo Uzavřeno) a závažnosti (kritický nebo upozornění). Výchozí zobrazení zobrazuje všechny aktivní výstrahy. Odeberou se všechny zavřené výstrahy ze systému po sedmi dnech.

![Podokno filtru pro filtrování podle kritický nebo stav varování](media/azure-stack-monitor-health/alert-view.png)

**Zobrazení rozhraní API** akce zobrazí rozhraní REST API, který se použil k vygenerování zobrazení seznamu. Tato akce nabízí rychlý způsob, jak se seznámit se syntaxí rozhraní REST API, můžete použít k upozornění dotazu. Toto rozhraní API můžete použít ve službě automation, nebo pro integraci s vašeho existujícího datacentra, monitorování, vytváření sestav a vytváření tiketů řešení.

Můžete kliknout na konkrétní výstrahu a prohlédněte si podrobnosti výstrahy. Podrobnosti výstrahy zobrazit všechna pole, které jsou spojeny s výstrahou a umožňují rychlou navigaci ovlivněné komponenty a zdroj výstrahy. Například následující výstraha v případě, některé z instancí rolí infrastruktury přejde do režimu offline nebo není přístupný.  

![V okně podrobností výstrah](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>Opravit upozornění

Můžete vybrat **opravit** v některé výstrahy.

Pokud je vybráno, **opravit** akce provede kroky konkrétní výstrahu, kterou chcete pokus o vyřešení problému. Po výběru stavu **opravit** akce je dostupná jako oznámení na portálu.

![Probíhá oprava](media/azure-stack-monitor-health/repair-in-progress.png)

**Opravit** akce oznámí úspěšné dokončení nebo se nepodařilo dokončit akci ve stejném okně portálu oznámení.  Pokud se opravné akce nezdaří výstrahu, může znovu spustíte **opravit** akce z podrobností výstrahy. Pokud se opravné akce úspěšně dokončí, **nejsou** znovu spustit **opravit** akce.

![Oprava úspěšně dokončí.](media/azure-stack-monitor-health/repair-completed.png)

Po instance role infrastruktury je zpátky do online režimu, tato výstraha se automaticky zavře. Mnoho, ale ne všechny výstrahy, se automaticky zavře po vyřešení problému. Výstrahy, které poskytují tlačítko Akce opravy se automaticky zavře, je-li tento problém řeší Azure Stack.  Pro všechny ostatní výstrahy vyberte **zavřít upozornění** po provedení nápravných kroků. Pokud se problém nevyřeší, Azure Stack vygeneruje nová výstraha. Pokud chcete problém vyřešit, výstrahu zůstává uzavřený a vyžaduje žádné další kroky.

## <a name="next-steps"></a>Další postup

[Správa aktualizací ve službě Azure Stack](azure-stack-updates.md)

[Správa oblastí ve službě Azure Stack](azure-stack-region-management.md)
