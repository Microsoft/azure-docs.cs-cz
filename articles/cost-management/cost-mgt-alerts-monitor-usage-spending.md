---
title: Monitorování využití a výdajů s náklady na upozornění | Dokumentace Microsoftu
description: Tento článek popisuje, jak náklady na výstrahy nápovědy monitorovat využití a výdajů ve službě Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: cost-management
manager: alavital
ms.custom: ''
ms.openlocfilehash: 813ec8f74371b6ae76ac306aea2c462f0beea1fb
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773423"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Použít cost výstrahy a monitorování využití a výdajů

Tento článek vám pomůže pochopit a výstrahy Cost Management můžete použít ke sledování Azure využití a výdajů. Nákladů výstrahy jsou automaticky generovány na základě když se spotřebovávají prostředky Azure. Správa nákladů a upozornění fakturace společně na jednom místě zobrazí všechny aktivní výstrahy. Dané prahové hodnoty dosáhne spotřebu výstrahy generuje služba Cost Management. Existují tři typy výstrah náklady: výstrahy rozpočtu, kredit upozornění a oznámení kvóty útraty oddělení.

## <a name="budget-alerts"></a>Upozornění na rozpočet

Rozpočet upozornění se zobrazí při útratu na základě využití nebo náklady, dosáhne nebo překračuje velikost podle [upozornění podmínku rozpočtu](tutorial-acm-create-budgets.md). Cost Management rozpočty lze vytvořit pomocí webu Azure portal nebo [Azure Consumption](https://docs.microsoft.com/rest/api/consumption) rozhraní API. 

Na webu Azure Portal jsou definovány rozpočty nákladů. Pomocí rozhraní Azure Consumption API, jsou definovány rozpočty nákladů nebo jejich využití. Upozornění na rozpočet podporují rozpočty nákladů na i na základě využití. Rozpočet výstrahy jsou generovány automaticky pokaždé, když jsou splněny podmínky upozornění na rozpočet. Na webu Azure Portal můžete zobrazit všechny výstrahy náklady. Pokaždé, když je vygenerována výstraha, bude uvedená v upozornění na náklady. Lidem v seznamu výstrah příjemci rozpočtu přijde e-mailové upozornění.

## <a name="credit-alerts"></a>Upozornění na kredit

Kredit oznámení vás upozorní, když se spotřebuje peněžní závazky vašeho kreditu Azure. Peněžní závazky jsou určené pro organizace s smlouvy Enterprise. Kredit výstrahy jsou generovány automaticky 90 % a 100 % zůstatek vašeho kreditu Azure. Pokaždé, když je vygenerována výstraha, to se projeví ve výstrahách náklady a v e-mailu na vlastníky účtů.

## <a name="department-spending-quota-alerts"></a>Oznámení kvóty útraty oddělení

Oddělení útraty kvóty oznámení vás upozorní, jakmile oddělení útraty dosáhne pevné prahové hodnoty kvóty. Útraty kvóty se konfigurují na portálu EA. Pokaždé, když je dosaženo prahové hodnoty generuje e-mailu oddělení vlastníkům a zobrazí se ve výstrahách náklady. Například, 50 % nebo % 75 kvóty.

## <a name="view-cost-alerts"></a>Zobrazení nákladů výstrah

Na webu Azure Portal klikněte v seznamu služeb na **Cost Management a fakturace**. Pak v seznamu **Cost Management**vyberte **nákladů výstrahy**.

![Obrázek příkladu výstrah zobrazených ve službě Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Celkový počet aktivních a zavřených výstrah se zobrazí na náklady na stránky s upozorněními.

Všechny výstrahy zobrazení typu výstrahy. Upozornění na rozpočet ukazuje důvod, proč byla vygenerována a název, který se vztahuje na rozpočtu. Každé upozornění zobrazuje datum, byl vygenerován, její stav a oboru (předplatné nebo skupinu pro správu), který se výstraha týká.

Je to možné stav zahrnuje **aktivní** a **zavře**. Aktivní stav znamená, že výstraha stále relevantní. Dismissed stav označuje, že někdo označil výstrahu, kterou chcete nastavit jako již nejsou relevantní.

Vyberte výstrahu v seznamu zobrazíte její podrobnosti. Podrobnosti výstrahy zobrazit další informace o výstraze. Upozornění na rozpočet zahrnout odkaz na rozpočtu. Pokud je k dispozici pro upozornění na rozpočet doporučení, pak odkaz na toto doporučení je zobrazen také. Rozpočtu, kredit a oznámení kvóty útraty oddělení mít odkaz na analýzu v analýzy nákladů kde můžete prozkoumat náklady pro obor upozornění. Následující příklad ukazuje výdaje za oddělení s detaily výstrahy.

![Příklad obrázek znázorňující výdaje za oddělení s detaily výstrahy](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Když zobrazujete podrobnosti zamítnuté výstrahy, můžete ho aktivovat v případě potřeby ruční akce. Příklad ukazuje následující obrázek.

![Obrázek znázorňující příklad zavřít a znovu aktivovat možnosti](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Další informace najdete v tématech

- Pokud jste již nebyly vytvořili rozpočtu nebo nastavit podmínky upozornění rozpočet, dokončete [vytvořit a spravovat rozpočty](tutorial-acm-create-budgets.md) kurzu.
