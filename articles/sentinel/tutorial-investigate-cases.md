---
title: Vyšetřování incidentů s Azure Sentinelem| Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak vyšetřovat incidenty s Azure Sentinel.
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
ms.openlocfilehash: ecd8c508d05bfeb541a6cb5efbcdf2fffd3c78d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587188"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Kurz: Vyšetřování incidentů s Azure Sentinelem

> [!IMPORTANT]
> Graf šetření je v současné době ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Tento kurz vám pomůže vyšetřit incidenty s Azure Sentinel. Po připojení zdrojů dat k Azure Sentinelu chcete být upozorněni, když se stane něco podezřelého. Aby vám to umožnilo, Azure Sentinel umožňuje vytvářet pokročilá pravidla výstrah, která generují incidenty, které můžete přiřadit a prozkoumat.

Tento článek se zabývá:
> [!div class="checklist"]
> * Šetření incidentů
> * Použití grafu šetření
> * Reakce na hrozby

Incident může zahrnovat více výstrah. Je to agregace všech relevantních důkazů pro konkrétní vyšetřování. Incident se vytvoří na základě analytických pravidel, která jste vytvořili na stránce **Analytics.** Vlastnosti související s výstrahami, jako je například závažnost a stav, jsou nastaveny na úrovni incidentu. Poté, co dáte Azure Sentinelu vědět, jaké druhy hrozeb hledáte a jak je najít, můžete zjistit zjištěné hrozby vyšetřováním incidentů.

## <a name="prerequisites"></a>Požadavky
Incident budete moci vyšetřit pouze v případě, že jste při nastavovat analytické pravidlo použili pole mapování entit. Graf vyšetřování vyžaduje, aby původní incident zahrnoval entity.

## <a name="how-to-investigate-incidents"></a>Postup při vyšetřování incidentů

1. Vyberte **možnost Incidenty**. Na stránce **Incidenty** se dozvíte, kolik incidentů máte, kolik je otevřených, kolik jste nastavili na **Probíhá**a kolik je zavřených. U každého incidentu můžete zobrazit čas, kdy k němu došlo, a stav incidentu. Podívejte se na závažnost rozhodnout, které incidenty zpracovat jako první.

    ![Zobrazit závažnost incidentu](media/tutorial-investigate-cases/incident-severity.png)

1. Incidenty můžete podle potřeby filtrovat, například podle stavu nebo závažnosti.

1. Chcete-li zahájit vyšetřování, vyberte konkrétní incident. Na pravé straně můžete zobrazit podrobné informace o incidentu, včetně jeho závažnosti, souhrnu počtu zúčastněných entit, nezpracovaných událostí, které tento incident vyvolaly, a jedinečného ID incidentu.

1. Chcete-li zobrazit další podrobnosti o výstrahách a entitách v incidentu, vyberte **zobrazit úplné podrobnosti** na stránce incidentu a zkontrolujte příslušné karty, které shrnují informace o incidentu. Na kartě **Výstrahy** zkontrolujte samotnou výstrahu. Můžete zobrazit všechny relevantní informace o výstraze – dotaz, který spustil výstrahu, počet vrácených výsledků na dotaz a možnost spouštět playbooky na výstrahy. Chcete-li přejít k podrobnostem o incidentu ještě dále, vyberte počet **událostí**. Tím se otevře dotaz, který generoval výsledky a události, které spustily výstrahu v Log Analytics. Na kartě **Entity** můžete zobrazit všechny entity, které jste namapovali jako součást definice pravidla výstrahy.

    ![Zobrazit podrobnosti výstrahy](media/tutorial-investigate-cases/alert-details.png)

1. Pokud incident aktivně vyšetřujete, je vhodné nastavit stav incidentu na **Probíhá,** dokud ho nezavřete.

1. Incidenty lze přiřadit konkrétnímu uživateli. Pro každý incident můžete přiřadit vlastníka nastavením pole **Vlastník incidentu.** Všechny incidenty začínají jako nepřiřazené. Můžete také přidat komentáře, aby ostatní analytici byli schopni pochopit, co jste vyšetřovali a jaké jsou vaše obavy kolem incidentu.

    ![Přiřazení incidentu uživateli](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Chcete-li zobrazit mapu šetření, vyberte možnost **Prozkoumat.**

## <a name="use-the-investigation-graph-to-deep-dive"></a>Použijte graf šetření k hlubokému prozkoumání

Graf šetření umožňuje analytikům klást správné otázky pro každé šetření. Graf šetření vám pomůže pochopit rozsah a identifikovat hlavní příčinu potenciální bezpečnostní hrozby korelacemi relevantních dat s jakoukoli přidruženou entitou. Můžete se ponořit hlouběji a prozkoumat libovolnou entitu uvedenou v grafu tak, že ji vyberete a vyberete mezi různými možnostmi rozšíření.  
  
Graf šetření poskytuje:

- **Vizuální kontext z nezpracovaných dat**: Živý vizuální graf zobrazuje vztahy entit extrahované automaticky z nezpracovaných dat. To umožňuje snadno zobrazit připojení napříč různými zdroji dat.

- **Úplné zjišťování oboru šetření:** Rozšiřte rozsah šetření pomocí předdefinovaných dotazů průzkumu, abyste se vynořili v plném rozsahu porušení.

- **Vestavěné kroky vyšetřování**: Pomocí předdefinovaných možností průzkumu se ujistěte, že pokládáte správné otázky tváří v tvář hrozbě.

Použití grafu šetření:

1. Vyberte incident a pak vyberte **Vyšetřovat**. Tím se dostanete do grafu vyšetřování. Graf poskytuje ilustrativní mapu entit přímo připojených k výstraze a každého dalšího připojeného prostředku.

   > [!IMPORTANT] 
   > Incident budete moci vyšetřit pouze v případě, že jste při nastavovat analytické pravidlo použili pole mapování entit. Graf vyšetřování vyžaduje, aby původní incident zahrnoval entity.

   ![Zobrazení mapy](media/tutorial-investigate-cases/map1.png)

1. Vyberte entitu, chcete-li otevřít podokno **Entity,** abyste mohli zkontrolovat informace o této entitě.

    ![Zobrazení entit v mapě](media/tutorial-investigate-cases/map-entities.png)
  
1. Rozšiřte své vyšetřování tak, že najedete na jednotlivé entity a odhalte seznam otázek, které byly navrženy našimi bezpečnostními odborníky a analytiky podle typu entity, abyste prohloubili vaše vyšetřování. Nazýváme tyto možnosti **průzkum dotazy**.

    ![Další podrobnosti](media/tutorial-investigate-cases/exploration-cases.png)

   Například v počítači můžete požadovat související výstrahy. Pokud vyberete průzkumný dotaz, výsledné nároky se přidají zpět do grafu. V tomto příkladu výběr **souvisejícívýstrahy** vrátil následující výstrahy do grafu:

    ![Zobrazit související výstrahy](media/tutorial-investigate-cases/related-alerts.png)

1. Pro každý průzkumný dotaz můžete vybrat možnost otevřít nezpracované výsledky událostí a dotaz použitý v Log Analytics výběrem **události\>**.

1. Chcete-li porozumět incidentu, graf poskytuje paralelní časovou osu.

    ![Zobrazit časovou osu v mapě](media/tutorial-investigate-cases/map-timeline.png)

1. Najeďte na časovou osu a zjistěte, ke kterým věcem v grafu došlo v jakém okamžiku.

    ![Použití časové osy v mapě k prozkoumání výstrah](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se dozvěděli, jak začít vyšetřovat incidenty pomocí Azure Sentinelu. Pokračujte v kurzu, [jak reagovat na hrozby pomocí automatických playbooků](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reagujte na hrozby](tutorial-respond-threats-playbook.md) a automatizujte své reakce na hrozby.

