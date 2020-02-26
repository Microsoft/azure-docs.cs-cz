---
title: Prozkoumejte výstrahy pomocí služby Azure Sentinel | Microsoft Docs
description: V tomto kurzu se dozvíte, jak prozkoumat výstrahy pomocí funkce Sentinel Azure.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585199"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Kurz: okamžité zjištění hrozeb


> [!IMPORTANT]
> Předem připravená detekce hrozeb je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Po [připojení zdrojů dat](quickstart-onboard.md) ke službě Azure Sentinel budete chtít dostávat oznámení, když dojde k nějakému podezřelému. Pokud to chcete povolit, Azure Sentinel vám poskytne předem připravené předdefinované šablony. Tyto šablony byly navržené týmem společnosti Microsoft pro odborníky na zabezpečení a analytikům na základě známých hrozeb, běžných vektorů útoku a řetězů eskalace útoků na podezřelé aktivity. Po povolení těchto šablon budou automaticky vyhledávat všechny aktivity, které vypadají podezřele napříč vaším prostředím. Mnohé z šablon můžete přizpůsobit tak, aby vyhledaly nebo odfiltroval aktivity podle vašich potřeb. Výstrahy vygenerované pomocí těchto šablon vytvoří incidenty, které můžete přiřadit a prozkoumat ve svém prostředí.

Tento kurz vám pomůže detekovat hrozby pomocí služby Azure Sentinel:

> [!div class="checklist"]
> * Použití předem připravených detekcí
> * Automatizace odpovědí na hrozby

## <a name="about-out-of-the-box-detections"></a>O dopředných detekcích box

Pokud chcete zobrazit všechna připravená zjišťování, pokračujte na **analýzy** a pak na **šablony pravidel**. Tato karta obsahuje všechna předdefinovaná pravidla Azure Sentinel.

   ![Použití vestavěných detekcí k nalezení hrozeb pomocí Sentinel Azure](media/tutorial-detect-built-in/view-oob-detections.png)

K dispozici jsou následující typy šablon:

- **Microsoft Security** – šablony zabezpečení Microsoftu automaticky vytvářejí incidenty Sentinel Azure z výstrah vygenerovaných jinými řešeními zabezpečení Microsoftu v reálném čase. Pravidla zabezpečení Microsoftu můžete použít jako šablonu k vytvoření nových pravidel s podobnou logikou. Další informace o pravidlech zabezpečení najdete v tématu [Automatické vytváření incidentů z výstrah zabezpečení společnosti Microsoft](create-incidents-from-alerts.md).
- **Syntéza** založená na technologii Fusion, Pokročilá detekce útoků s více fázemi v Azure Sentinel používá škálovatelné algoritmy strojového učení, které mohou korelovat mnoho výstrah a událostí s nízkou kvalitou v různých produktech na vysoce věrné a napadnutelné incidenty. Fusion je ve výchozím nastavení povolená. Vzhledem k tomu, že je logika skrytá, nemůžete ji použít jako šablonu k vytvoření více než jednoho pravidla.
- **Analýza chování ve strojovém učení** – tyto šablony jsou založené na vlastních algoritmech strojového učení od Microsoftu, takže nemůžete vidět interní logiku, jak fungují a kdy se spouštějí. Vzhledem k tomu, že je logika skrytá, nemůžete ji použít jako šablonu k vytvoření více než jednoho pravidla.
-   **Plánovaná** – plánovaná analytická pravidla jsou naplánované dotazy napsané odborníky na zabezpečení Microsoftu. Můžete zobrazit logiku dotazu a provést změny. Můžete použít plánovaná pravidla jako šablonu a vytvořit nová pravidla s podobnou logikou.

## <a name="use-out-of-the-box-detections"></a>Použití předem připravených detekcí

1. Pokud chcete použít předdefinovanou šablonu, klikněte na **vytvořit pravidlo** a vytvořte nové aktivní pravidlo založené na této šabloně. Každá položka má seznam požadovaných zdrojů dat, které jsou automaticky zkontrolovány, a výsledkem může být zakázané **Vytvoření pravidla** .
  
   ![Použití vestavěných detekcí k nalezení hrozeb pomocí Sentinel Azure](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Otevře se Průvodce vytvořením pravidla v závislosti na vybrané šabloně. Všechny podrobnosti jsou vyplněné a pro **plánovaná pravidla** nebo **pravidla zabezpečení Microsoftu**můžete logiku přizpůsobit tak, aby lépe vyhovovala vaší organizaci, nebo vytvořit další pravidla založená na předdefinované šabloně. Po provedení kroků v Průvodci vytvořením pravidla a dokončení vytvoření pravidla na základě šablony se nové pravidlo zobrazí na kartě **aktivní pravidla** .

Další informace o polích v průvodci najdete v tématu [kurz: vytvoření vlastních pravidel pro analytiky k detekci podezřelých hrozeb](tutorial-detect-threats-custom.md).



## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak začít s detekcí hrozeb pomocí funkce Azure Sentinel. 

Pokud se chcete dozvědět, jak automatizovat vaše odezvy na hrozby, [nastavte v Azure Sentinelu automatické odpovědi na hrozby](tutorial-respond-threats-playbook.md).

