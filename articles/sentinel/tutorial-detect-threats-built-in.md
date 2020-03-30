---
title: Prozkoumejte výstrahy pomocí Azure Sentinelu| Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak prozkoumat výstrahy s Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: df855aa768fdeb279482b8407259be1a644322d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585199"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Kurz: Zjišťování hrozeb ioutací


> [!IMPORTANT]
> Out-of-the-box detekce hrozeb je aktuálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Po [připojení zdrojů](quickstart-onboard.md) dat k Azure Sentinelu chcete být upozorněni, když se stane něco podezřelého. Aby vám to umožnilo, Azure Sentinel vám poskytuje předem integrované šablony. Tyto šablony byly navrženy týmem odborníků a analytiků společnosti Microsoft na základě známých hrozeb, běžných vektorů útoků a řetězců eskalace podezřelých aktivit. Po povolení těchto šablon budou automaticky vyhledávat všechny aktivity, které vypadají podezřele ve vašem prostředí. Mnoho šablon lze přizpůsobit tak, aby vyhledávalo nebo odfiltrovalo aktivity podle vašich potřeb. Výstrahy generované těmito šablonami vytvoří incidenty, které můžete přiřadit a prozkoumat ve vašem prostředí.

Tento kurz vám pomůže zjistit hrozby s Azure Sentinelem:

> [!div class="checklist"]
> * Použití out-of-the-box detekce
> * Automatizace reakcí na hrozby

## <a name="about-out-of-the-box-detections"></a>Informace o předem připravených detekcích

Chcete-li zobrazit všechna zjišťování před výběrem, přejděte na **službu Analytics** a potom na **šablony pravidel**. Tato karta obsahuje všechna předdefinovaná pravidla Azure Sentinelu.

   ![Hledání hrozeb pomocí Azure Sentinelu pomocí integrovaných detekčních detekcí](media/tutorial-detect-built-in/view-oob-detections.png)

K dispozici jsou následující typy šablon:

- **Zabezpečení Microsoftu** – šablony zabezpečení Microsoftu automaticky vytvářejí incidenty Azure Sentinelu z výstrah generovaných v jiných bezpečnostních řešeních Microsoftu v reálném čase. Pravidla zabezpečení společnosti Microsoft můžete použít jako šablonu k vytvoření nových pravidel s podobnou logikou. Další informace o pravidlech zabezpečení naleznete v tématu [Automatické vytváření incidentů z výstrah zabezpečení společnosti Microsoft](create-incidents-from-alerts.md).
- **Fusion** – na základě technologie Fusion používá pokročilá vícestupňová detekce útoků v Azure Sentinelu škálovatelné algoritmy strojového učení, které mohou korelovat mnoho výstrah a událostí s nízkou věrností napříč různými produkty do incidentů s vysokou věrností a možností použití. Fúze je ve výchozím nastavení povolena. Vzhledem k tomu, že logika je skrytá, nelze použít jako šablonu k vytvoření více než jedno pravidlo.
- **Analýza chování strojového učení** – tyto šablony jsou založeny na proprietárních algoritmech strojového učení microsoftu, takže nevidíte vnitřní logiku jejich práce a při jejich spuštění. Vzhledem k tomu, že logika je skrytá, nelze použít jako šablonu k vytvoření více než jedno pravidlo.
-   **Plánovaná** – plánovaná analytická pravidla jsou naplánované dotazy napsané odborníky společnosti Microsoft na zabezpečení. Můžete zobrazit logiku dotazu a provést v ní změny. Naplánovaná pravidla můžete použít jako šablonu k vytvoření nových pravidel s podobnou logikou.

## <a name="use-out-of-the-box-detections"></a>Použití out-of-the-box detekce

1. Chcete-li použít předdefinovanou šablonu, klikněte na **Vytvořit pravidlo** a vytvořte nové aktivní pravidlo založené na této šabloně. Každá položka obsahuje seznam požadovaných zdrojů dat, které jsou automaticky kontrolovány, což může mít za následek zakázání **pravidla vytvořit.**
  
   ![Hledání hrozeb pomocí Azure Sentinelu pomocí integrovaných detekčních detekcí](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Otevře se průvodce vytvořením pravidla na základě vybrané šablony. Všechny podrobnosti jsou automaticky **vyplněny** a pro plánovaná pravidla nebo **pravidla zabezpečení společnosti Microsoft**můžete přizpůsobit logiku tak, aby lépe vyhovovala vaší organizaci, nebo vytvořit další pravidla založená na předdefinované šabloně. Po provedení kroků v průvodci vytvořením pravidla a dokončení vytvoření pravidla založeného na šabloně se nové pravidlo zobrazí na kartě **Aktivní pravidla.**

Další informace o polích v průvodci naleznete v [tématu Výuka: Vytvoření vlastních analytických pravidel pro zjišťování podezřelých hrozeb](tutorial-detect-threats-custom.md).



## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili, jak začít rozpoznávat hrozby pomocí Azure Sentinelu. 

Chcete-li se dozvědět, jak automatizovat odpovědi na hrozby, [nastavte automatické reakce na hrozby v Azure Sentinelu](tutorial-respond-threats-playbook.md).

