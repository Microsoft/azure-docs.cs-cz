---
title: Prozkoumat incidenty pomocí služby Azure Sentinel | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí funkce Azure Sentinel vytvořit Pokročilá pravidla výstrah, která generují incidenty, které můžete přiřadit a prozkoumat.
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
ms.openlocfilehash: 648d456534e86075a243a84ac0485d95a7f28479
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843103"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Kurz: zkoumání incidentů pomocí služby Azure Sentinel

> [!IMPORTANT]
> Graf šetření je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Tento kurz vám pomůže prozkoumat incidenty pomocí služby Azure Sentinel. Po připojení zdrojů dat ke službě Azure Sentinel chcete být upozorněni, když dojde k nějaké podezřelé situaci. Pokud to chcete povolit, Azure Sentinel vám umožní vytvářet Pokročilá pravidla výstrah, která generují incidenty, které můžete přiřadit a prozkoumat.

Tento článek popisuje:
> [!div class="checklist"]
> * Šetření incidentů
> * Použití grafu šetření
> * Reakce na hrozby

Incident může zahrnovat více výstrah. Jedná se o agregaci všech relevantních důkazů pro konkrétní šetření. Incident se vytvoří na základě analytických pravidel, která jste vytvořili na stránce **Analytics** . Vlastnosti související s výstrahami, jako je závažnost a stav, se nastavují na úrovni incidentu. Až budete chtít, aby služba Azure Sentinel znala, jaké druhy hrozeb hledáte a jak se mají najít, můžete monitorovat zjištěné hrozby zkoumáním incidentů.

## <a name="prerequisites"></a>Požadavky
- Incident budete moct prozkoumat jenom v případě, že jste při nastavování pravidla Analytics použili pole mapování entit. Graf šetření vyžaduje, aby původní incident zahrnoval entity.

- Pokud máte uživatele typu Host, který potřebuje přiřadit incidenty, musí být uživateli přiřazena role [čtečky adresáře](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) v TENANTOVI Azure AD. Ve výchozím nastavení mají tuto roli přiřazený běžný uživatel (bez hosta).

## <a name="how-to-investigate-incidents"></a>Postup při vyšetřování incidentů

1. Vyberte **incidenty**. Na stránce **incidenty** se dozvíte, kolik incidentů máte, kolik jich je otevřených, kolik se vám nastavilo **v průběhu**a kolik se zavřelo. U každého incidentu můžete vidět čas, ke kterému došlo, a stav incidentu. Podívejte se na závažnost, abyste se rozhodli, jaké incidenty se mají zpracovat jako první.

    ![Zobrazit závažnost incidentu](media/tutorial-investigate-cases/incident-severity.png)

1. Incidenty můžete podle potřeby filtrovat, například podle stavu nebo závažnosti.

1. Chcete-li zahájit šetření, vyberte konkrétní incident. Na pravé straně můžete zobrazit podrobné informace o incidentu, včetně jeho závažnosti, souhrnu počtu zúčastněných subjektů, nezpracovaných událostí, které aktivovaly tento incident, a jedinečného ID incidentu.

1. Chcete-li zobrazit další podrobnosti o výstrahách a entitách v incidentu, vyberte možnost **Zobrazit úplné podrobnosti** na stránce incident a zkontrolujte příslušné karty, které shrnují informace o incidentu. Na kartě **výstrahy** si prohlédněte samotnou výstrahu. Zobrazí se všechny relevantní informace o výstraze – dotaz, který aktivoval výstrahu, počet vrácených výsledků na dotaz a možnost spustit playbooky na výstrahách. Chcete-li přejít k podrobnostem i dál k incidentu, vyberte počet **událostí**. Otevře se dotaz, který vygeneroval výsledky, a události, které vyvolaly výstrahu v Log Analytics. Na kartě **entity** můžete zobrazit všechny entity, které jste namapovali jako součást definice pravidla výstrahy.

    ![Zobrazit podrobnosti výstrahy](media/tutorial-investigate-cases/alert-details.png)

1. Pokud aktivně zkoumáte incident, je vhodné nastavit stav incidentu na **probíhá** , dokud ho nezavřete.

1. Incidenty je možné přiřadit konkrétnímu uživateli. U každého incidentu můžete vlastníka přiřadit nastavením pole **vlastník incidentu** . Všechny incidenty začínají jako nepřiřazené. Můžete také přidat komentáře, aby ostatní analytiké mohli pochopit, co jste prošetřili a co se týkají incidentů.

    ![Přiřadit incident uživateli](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Vyberte **prozkoumat** pro zobrazení mapy šetření.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Použití grafu šetření k hlubokým podrobněm

Graf šetření umožňuje analytikům požádat o správné otázky pro každé šetření. Graf šetření vám pomůže pochopit rozsah a identifikovat hlavní příčinu potenciální bezpečnostní hrozby tím, že koreluje relevantní data se všemi zúčastněnými entitami. Můžete podrobně hlubší a prozkoumat jakoukoli entitu zobrazenou v grafu tak, že ji vyberete a zvolíte mezi různými možnostmi rozšíření.  
  
Graf šetření vám poskytne:

- **Vizuální kontext z nezpracovaných dat**: Live, Visual Graph zobrazuje vztahy mezi entitami extrahovanými automaticky z nezpracovaných dat. Díky tomu můžete snadno zobrazit připojení v různých zdrojích dat.

- **Zjišťování rozsahu úplného šetření**: Rozšiřte svůj rozsah šetření pomocí integrovaných dotazů na průzkum, které doplní celou oblast porušení.

- **Integrované kroky pro šetření**: pomocí předdefinovaných možností průzkumu se ujistěte, že na začátku hrozby vyžádáte správné otázky.

Použití grafu šetření:

1. Vyberte incident a pak vyberte **prozkoumat**. Tím přejdete do grafu šetření. Graf poskytuje ilustrativní mapu entit přímo připojených k výstraze a dalších prostředků, které jsou propojeny.

   > [!IMPORTANT] 
   > Incident budete moct prozkoumat jenom v případě, že jste při nastavování pravidla Analytics použili pole mapování entit. Graf šetření vyžaduje, aby původní incident zahrnoval entity.

   ![Zobrazení mapy](media/tutorial-investigate-cases/map1.png)

1. Vyberte entitu a otevřete tak podokno **entity** , abyste mohli zkontrolovat informace o dané entitě.

    ![Zobrazit entity v mapě](media/tutorial-investigate-cases/map-entities.png)
  
1. Rozbalíte své šetření tak, že najedete myší na každou entitu a zobrazíte seznam otázek, které byly navrženy našimi odborníky na zabezpečení a analytiky na typ entity, a prohloubit šetření. Zavoláme tyto možnosti **průzkumu dotazů**.

    ![Prozkoumat další podrobnosti](media/tutorial-investigate-cases/exploration-cases.png)

   Například na počítači můžete požádat o související výstrahy. Pokud vyberete dotaz průzkumu, výsledné nároky se přidají zpátky do grafu. V tomto příkladu vyberete **související výstrahy** , které vrátí následující výstrahy do grafu:

    ![Zobrazit související výstrahy](media/tutorial-investigate-cases/related-alerts.png)

1. Pro každý dotaz průzkumu můžete vybrat možnost pro otevření nezpracovaných výsledků události a dotaz použitý v Log Analytics, a to tak, že **vyberete \> události**.

1. Aby bylo možné porozumět incidentu, graf vám nabídne paralelní časovou osu.

    ![Zobrazit časovou osu v mapě](media/tutorial-investigate-cases/map-timeline.png)

1. Najeďte myší na časovou osu, abyste viděli, které věci v grafu nastaly v daném časovém okamžiku.

    ![Použití časové osy v mapě k prozkoumání výstrah](media/tutorial-investigate-cases/use-timeline.png)

## <a name="closing-an-incident"></a>Uzavření incidentu

Po vyřešení konkrétního incidentu (například když vaše šetření dosáhlo svého uzavření) byste měli nastavit stav incidentu na **Uzavřeno**. Když to uděláte, budete požádáni o klasifikaci incidentu tím, že zadáte důvod, který ho zavíráte. Tento krok je povinný. Klikněte na **Vybrat klasifikaci** a v rozevíracím seznamu vyberte jednu z následujících možností:

- Pravdivá pozitivní podezřelá aktivita
- Neškodné kladné podezřelé, ale očekávané
- Falešně pozitivní – nekorektní logika výstrahy
- Falešně pozitivní – nesprávná data
- Neurčené

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="Snímek obrazovky, který zvýrazní klasifikace dostupné v seznamu vybrat klasifikace.":::

Po zvolení příslušné klasifikace přidejte do pole **Komentář** nějaký popisný text. To bude užitečné v případě, že se potřebujete vrátit k tomuto incidentu. Až budete hotovi, klikněte na **použít** a incident se uzavře.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="Snímek obrazovky, který zvýrazní klasifikace dostupné v seznamu vybrat klasifikace.":::

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak začít s vyšetřováním incidentů pomocí služby Azure Sentinel. Přejděte k kurzu, [jak reagovat na hrozby pomocí automatizovaného playbooky](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reakce na hrozby](tutorial-respond-threats-playbook.md) pro automatizaci reakcí na hrozby.

