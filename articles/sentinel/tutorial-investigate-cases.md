---
title: Prozkoumat incidenty pomocí Azure Sentinel Preview | Microsoft Docs
description: V tomto kurzu se dozvíte, jak prozkoumat incidenty pomocí služby Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: bad3fddd6caf7e6eb455e59280f181c787b95a4e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780387"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel-preview"></a>Kurz: Prozkoumat incidenty pomocí Azure Sentinel Preview

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento kurz vám pomůže detekovat hrozby pomocí služby Azure Sentinel.

Po [připojení zdrojů dat](quickstart-onboard.md) ke službě Azure Sentinel chcete být upozorněni, když dojde k nějaké podezřelé situaci. Abyste to mohli udělat, Azure Sentinel vám umožní vytvářet Pokročilá pravidla výstrah, která generují incidenty, které můžete přiřadit a použít k hloubku šetření anomálií a hrozeb ve vašem prostředí. 

> [!div class="checklist"]
> * Vytvořit incidenty
> * Šetření incidentů
> * Reakce na hrozby

## <a name="investigate-incidents"></a>Šetření incidentů

incident může zahrnovat více výstrah. Jedná se o agregaci všech relevantních důkazů pro konkrétní šetření. incident se vytvoří na základě výstrah, které jste definovali na stránce **Analytics** . Vlastnosti související s výstrahami, jako je závažnost a stav, se nastavují na úrovni incidentu. Až budete chtít, aby služba Azure Sentinel znala, jaké druhy hrozeb hledáte, a jak je najít, můžete monitorovat hrozby zjištěné vyšetřováním incidentů. 

1. Vyberte **incidenty**. Na stránce incidenty se dozvíte, kolik incidentů máte, kolik jich je otevřených, kolik se vám nastavilo **v průběhu**a kolik se zavřelo. U každého incidentu můžete vidět čas, ke kterému došlo, a stav incidentu. Podívejte se na závažnost, abyste se rozhodli, co se má zpracovat jako první. Kliknutím na kartu **výstrahy** na stránce **incidenty** zobrazíte všechny výstrahy, které se vztahují k incidentu. Entity, které jste namapovali dříve jako součást incidentu, lze zobrazit na kartě **entity** .  Incidenty můžete podle potřeby filtrovat, například podle stavu nebo závažnosti. Když se podíváte na kartu incidenty, zobrazí se otevřené incidenty obsahující výstrahy, které aktivovala vaše pravidla detekce definovaná v tématu **Analýza**. V horní části uvidíte aktivní incidenty, nové incidenty a probíhající incidenty. Můžete si také prohlédnout přehled všech incidentů podle závažnosti.

   ![Řídicí panel výstrah](./media/tutorial-investigate-cases/cases.png)

2. Chcete-li zahájit šetření, klikněte na konkrétní incident. Na pravé straně můžete zobrazit podrobné informace o incidentu včetně jeho závažnosti, souhrnu počtu zúčastněných entit (na základě vašeho mapování). Každý incident má jedinečné ID. Závažnost incidentu je určena podle nejzávažnějšího upozornění, které je součástí incidentu.  

1. Pokud chcete zobrazit další podrobnosti o výstrahách a entitách v incidentu, klikněte na **Zobrazit úplné podrobnosti** na stránce incidentu a Projděte si příslušné karty, které shrnují informace o incidentu.  Zobrazení úplného incidentu slučuje všechny legitimace v výstrahy, související výstrahy a entity.

1. Na kartě **výstrahy** si přečtěte samotnou výstrahu, když byla aktivována, a podle toho, kolik překročilo nastavené prahové hodnoty. Zobrazí se všechny relevantní informace o výstraze – dotaz, který aktivoval výstrahu, počet vrácených výsledků na dotaz a možnost spustit playbooky na výstrahách. Pokud chcete ještě více přejít k incidentu, klikněte na počet přístupů. Otevře se dotaz, který vygeneroval výsledky, a výsledky, které vyvolaly výstrahu v Log Analytics.

3. Na kartě **entity** můžete zobrazit všechny entity, které jste namapovali jako součást definice pravidla výstrahy. 

4. Pokud aktivně zkoumáte incident, je vhodné nastavit stav incidentu na **probíhá** , dokud ho nezavřete. Incident můžete také uzavřít, kde uzavřená **vyřešená** je stav incidentů, který signalizuje, že byl incident zpracován, zatímco **uzavřený** je stav incidentů, které nevyžadují zpracování. Jsou požadovány vysvětlení, která objasňují důvody uzavření incidentu.

5. Incidenty je možné přiřadit konkrétnímu uživateli. U každého incidentu můžete vlastníka přiřadit nastavením pole **vlastník** incidentu. Všechny incidenty začínají jako nepřiřazené. Pokud chcete zobrazit všechny incidenty, které vlastníte, můžete přejít na incidenty a filtrovat podle vašeho jména. 

5. Kliknutím na **prozkoumat** zobrazte mapu a rozsah porušení s postupem nápravy. 



## <a name="respond-to-threats"></a>Reakce na hrozby

Azure Sentinel nabízí dvě primární možnosti pro reagování na hrozby pomocí playbooky. Můžete nastavit, aby se PlayBook spouštěla automaticky, když se aktivuje výstraha, nebo můžete ručně spustit PlayBook v reakci na výstrahu.

- Můžete nastavit, aby se PlayBook spouštěla automaticky, když se aktivuje výstraha, když nakonfigurujete PlayBook. 

- Můžete ručně spustit PlayBook z výstrahy, a to tak, že kliknete na **Zobrazit playbooky** a pak vyberete PlayBook, které se má spustit.




## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak začít s vyšetřováním incidentů pomocí služby Azure Sentinel. Přejděte k kurzu, [jak reagovat na hrozby pomocí automatizovaného playbooky](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reakce](tutorial-respond-threats-playbook.md) na hrozby pro automatizaci reakcí na hrozby.

