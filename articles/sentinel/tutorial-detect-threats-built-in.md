---
title: Prozkoumejte výstrahy pomocí služby Azure Sentinel | Microsoft Docs
description: Naučte se používat předem připravené šablony pro detekci hrozeb Azure, které vás upozorní, když dojde k nějakému podezřelému.
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 5d73337c25c812363b7a542bf42372ca3baa10e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605407"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Kurz: Dodávaná detekce hrozeb


> [!IMPORTANT]
> Předem připravená detekce hrozeb je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Po [připojení zdrojů dat](quickstart-onboard.md)   ke službě Azure Sentinel budete chtít být upozorněni, když dojde k nějakému podezřelému. To je důvod, proč Azure Sentinel poskytuje předem připravené předdefinované šablony, které vám pomůžou vytvořit pravidla detekce hrozeb. Tyto šablony byly navržené týmem společnosti Microsoft pro odborníky na zabezpečení a analytikům na základě známých hrozeb, běžných vektorů útoku a řetězů eskalace útoků na podezřelé aktivity. Pravidla vytvořená z těchto šablon budou automaticky vyhledávat v celém prostředí pro všechny aktivity, které vypadají jako podezřelé. Mnohé z šablon lze přizpůsobit pro hledání aktivit nebo jejich filtrování podle vašich potřeb. Výstrahy vygenerované pomocí těchto pravidel vytvoří incidenty, které můžete přiřadit a prozkoumat ve svém prostředí.

Tento kurz vám pomůže detekovat hrozby pomocí služby Azure Sentinel:

> [!div class="checklist"]
> * Použití předem připravených detekcí hrozeb
> * Automatizace odpovědí na hrozby

## <a name="about-out-of-the-box-detections"></a>Informace o předem připravených detekcích

Pokud chcete zobrazit všechna připravená zjišťování, pokračujte na **analýzy** a pak na **šablony pravidel**. Tato karta obsahuje všechna předdefinovaná pravidla Azure Sentinel.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Použití vestavěných detekcí k nalezení hrozeb pomocí Sentinel Azure":::

K dispozici jsou následující typy šablon:

- **Zabezpečení Microsoftu**
   
   Šablony zabezpečení Microsoftu automaticky vytvářejí incidenty Sentinel Azure z výstrah vygenerovaných v jiných řešeních zabezpečení Microsoftu v reálném čase. Pravidla zabezpečení Microsoftu můžete použít jako šablonu k vytvoření nových pravidel s podobnou logikou. Další informace o pravidlech zabezpečení najdete v tématu [Automatické vytváření incidentů z výstrah zabezpečení společnosti Microsoft](create-incidents-from-alerts.md).

- **Fúze** 

    V závislosti na technologii Fusion Pokročilá detekce útoků s více fázemi v Azure Sentinel používá škálovatelné algoritmy strojového učení, které mohou korelovat mnoho výstrah a událostí s nízkou kvalitou v různých produktech na vysokou věrnou a napadnutelnou incidenty. Fusion je ve výchozím nastavení povolená. Vzhledem k tomu, že je logika skrytá a proto není přizpůsobitelná, můžete s touto šablonou vytvořit jenom jedno pravidlo.

- **Analýza chování ve strojovém učení**

    Tyto šablony jsou založené na speciálních algoritmech strojového učení od Microsoftu, takže nemůžete vidět interní logiku, jak fungují a kdy se spouštějí. Vzhledem k tomu, že je logika skrytá a proto není přizpůsobitelná, můžete vytvořit pouze jedno pravidlo s každou šablonou tohoto typu.

- **Uvedenou**

    Plánovaná analytická pravidla jsou založená na integrovaných dotazech zapsaných odborníky na zabezpečení Microsoftu. Můžete zobrazit logiku dotazu a provést změny. Můžete použít šablonu naplánované pravidla a přizpůsobit logiku dotazu a nastavení plánování pro vytváření nových pravidel.

## <a name="use-out-of-the-box-detections"></a>Použití předem připravených detekcí

1. Pokud chcete použít předdefinovanou šablonu, klikněte na název šablony a potom v podokně podrobností klikněte na tlačítko **vytvořit pravidlo** a vytvořte nové aktivní pravidlo založené na této šabloně. Každá šablona obsahuje seznam požadovaných zdrojů dat. Když otevřete šablonu, zdroje dat budou automaticky kontrolovány dostupnosti. Pokud dojde k problému s dostupností, tlačítko **vytvořit pravidlo** může být zakázané nebo se může zobrazit upozornění.
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Použití vestavěných detekcí k nalezení hrozeb pomocí Sentinel Azure":::
 
1. Kliknutím na tlačítko **vytvořit pravidlo** otevřete Průvodce vytvořením pravidla v závislosti na vybrané šabloně. Všechny podrobnosti jsou vyplněné a pomocí **naplánovaných** nebo šablon **zabezpečení Microsoftu** můžete přizpůsobit logiku a další nastavení pravidla, aby lépe vyhovovala vašim konkrétním potřebám. Tento postup můžete opakovat a vytvořit další pravidla na základě předdefinované šablony. Po provedení kroků v Průvodci vytvořením pravidla na konci budete muset vytvořit pravidlo založené na šabloně. Nová pravidla se zobrazí na kartě **aktivní pravidla** .

    Další podrobnosti o tom, jak přizpůsobit pravidla v Průvodci vytvořením pravidla, najdete v tématu [kurz: vytvoření vlastních pravidel analýzy pro detekci hrozeb](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak začít s detekcí hrozeb pomocí funkce Azure Sentinel. 

Pokud se chcete dozvědět, jak automatizovat vaše odezvy na hrozby, [nastavte v Azure Sentinelu automatické odpovědi na hrozby](tutorial-respond-threats-playbook.md).

