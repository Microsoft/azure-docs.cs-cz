---
title: Prozkoumat případů s Sentinelu ve verzi Preview Azure | Dokumentace Microsoftu
description: A zjistěte, jak prozkoumat případy s Sentinelu Azure pomocí tohoto kurzu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 7c4994863f3c145c7095bcc12dd69ff02fab8455
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540222"
---
# <a name="tutorial-investigate-cases-with-azure-sentinel-preview"></a>Kurz: Prozkoumat případů s Azure Sentinelu ve verzi Preview

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento kurz pomůže odhalit hrozby s využitím Azure Sentinelu.

Poté co [připojené zdroje dat](quickstart-onboard.md) Sentinelu Azure chcete budete upozorněni, když se stane něco podezřelé. Pokud chcete povolit, můžete k tomu, Sentinelu Azure, které vytvoříte upřesňující pravidla výstrah, které generují případů, které můžete přiřadit a použití jak hluboko zjistit anomálie a hrozby ve vašem prostředí. 

> [!div class="checklist"]
> * Vytvořte případy
> * Prozkoumávání případů
> * Reakce na hrozby

## <a name="investigate-cases"></a>Prozkoumávání případů

Případ může obsahovat více výstrah. Je souhrn všech důkazy, které jsou relevantní pro konkrétní šetření. Případ je založeno na výstrahy, kterou jste definovali v **Analytics** stránky. Vlastnosti související s výstrahami, jako je například stav a závažnost jsou nastaveny na úrovni případu. Po necháte Azure Sentinelu vědět, jaké druhy hrozeb, které hledáte a jak je najít, můžete monitorovat hrozby, kterých se zjišťují šetření případů. 

1. Vyberte **případů**. **Případů** stránky vám umožňuje vědět, kolik případů budete mít, kolik se otevře, kolik jste nastavili **probíhá**, a kolik jsou zavřené. Pro každý případ můžete zobrazit čas, kdy se k němu došlo a stav případu. Podívejte se na závažnosti při rozhodování o zpracování první. V **případů** stránky, klikněte na tlačítko **výstrahy** kartu zobrazíte všechny výstrahy, které souvisí s případem. Entity, které jste namapovali jako součást tento případ, lze zobrazit v dříve **entity** kartu.  Případy můžete filtrovat podle potřeby, například podle stavu nebo závažnosti. Když se podíváte **případů** kartě, zobrazí se vám otevřených případů, které obsahují upozornění aktivované testováním vaše pravidla detekce, které jsou definované v **Analytics**. V horní části uvidíte aktivních obchodních případů, nové případy a v průběhu případy. Naleznete v tématu Přehled o všech případů podle závažnosti.

  ![Řídicí panel výstrah](./media/tutorial-investigate-cases/cases.png)

2. Pokud chcete začít šetření, klikněte na zvláštní případ. Na pravé straně zobrazí se podrobné informace o tento případ, včetně závažnosti, souhrn počtu účastnící se entity (podle mapování pracovního). Každý případ má jedinečné ID. Závažnost případu se určuje podle nejvyšší závažnost výstrahy, které jsou zahrnuté v případě.  

1. Chcete-li zobrazit další podrobnosti o výstrahách a entit v případě, klikněte na **zobrazit úplné podrobnosti** v případě stránky a zkontrolujte příslušné karty, které shrnují informací o případu.  Zobrazení úplného případů konsoliduje všechny důkazy v upozornění, související výstrahy a entity.

1. V **výstrahy** kartu, přezkoumejte toto upozornění, samotný – když se aktivuje a kolik překročení prahových hodnot, které jste nastavili. Zobrazí se všechny relevantní informace o dané výstraze – dotaz, který aktivoval výstrahu, počet výsledků vrácených na dotaz a možnost spustit playbooky na výstrahy. Přejít dolů ještě dál do případu, klikněte na počet přístupů. Otevře se dotaz, který vygeneroval výsledky a výsledky, které se aktivuje upozornění v Log Analytics.

3. V **entity** kartě, zobrazí se všechny entity, které jste namapovali jako součást definice pravidla upozornění. 

4. Pokud budete aktivně ho prověřují případ, je vhodné nastavit stav případu **probíhá** dokud ho neukončíte. Můžete zavřít i v případě, kde **uzavřeno přeložit** je stav pro případy, které označují, že byla zpracována incident, při **uzavřeno dismissed** je stav pro případy, které nevyžadují zpracování. Vysvětlení jsou požadovány, s vysvětlením, vaše důvody pro uzavření případu.

5. Případy můžete přiřadit pro konkrétního uživatele. Pro každý případ můžete přiřadit vlastníka, tak, že nastavíte tak **vlastníka** pole. Všechny případy start jako nepřiřazené. Můžete přejít do případy a filtrovat podle vaše jméno, zobrazí všechny případy, které vlastníte. 

5. Klikněte na tlačítko **prošetření** zobrazíte mapa šetření a rozsahu porušení s kroky k nápravě. 



## <a name="respond-to-threats"></a>Reakce na hrozby

Azure Sentinel poskytuje dvě primární možnosti pro reakce na hrozby pomocí playbooků. Můžete nastavit playbook spustit automaticky, když se aktivuje upozornění, nebo můžete ručně spustit playbooku v reakci na výstrahy.

- Můžete nastavit playbook spustit automaticky, když se aktivuje upozornění, když konfigurujete playbooku. 

- Playbook z uvnitř výstrahu, můžete spustit ručně kliknutím **zobrazit playbooky** a následným výběrem playbook ke spuštění.




## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak začít šetření případů použití ověřovacích Azure. Dál najdete v tomto kurzu [týkající se reakce na hrozby pomocí automatizovaných playbooky](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reakce na hrozby](tutorial-respond-threats-playbook.md) k automatizaci vaše odpovědi na ně.

