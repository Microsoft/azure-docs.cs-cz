---
title: Správa rozpočtů Cloudyn v Azure
description: Tento článek vám pomůže rychle vytvořit rozpočtů a zahájení správy ve službě Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 9d7d0e049d3c35aab56145beb94c8e41e56c5785
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219100"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Správa Azure rozpočtů s Cloudyn

Nastavení rozpočtů a na základě rozpočtu výstrahy zobrazíte nápovědu, jak zlepšit cloudu zásad správného řízení a zodpovědnost. Tento článek vám pomůže rychle vytvořit rozpočtů a zahájení správy ve službě Cloudyn.

Když máte účet organizace nebo MSP, můžete přiřadit různé organizační jednotky, oddělení nebo jiná entita náklady na měsíční kvóty rozpočtu struktury entity hierarchické náklady. Pokud máte účet Premium, můžete použít funkce správy rozpočtu, který se následně použije na vaše cloudové výdaje. Jsou všechny rozpočty přiřadit ručně.

Založené na přiřazené rozpočty, můžete nastavit prahové hodnoty výstrahy podle procenta z rozpočtu, který je zpracován a definovat závažnost jednotlivých prahovou hodnotu.

Rozpočet sestavy zobrazit přiřazené rozpočtu. Uživatelé můžou zobrazit po jejich útraty přes, v části nebo nominální s jejich využití v čase. Když vyberete možnost **Zobrazit/skrýt pole** v horní části sestavy rozpočtu, můžete zobrazit náklady, rozpočet, kumulované náklady nebo celkový rozpočet.

Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace o rozpočtech v Cost Management najdete v tématu [vytváření a Správa rozpočtů](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Vytvoření rozpočtů

Při vytváření rozpočtu, nastavte pro fiskální rok a platí pro konkrétní entitu.

Vytvoření rozpočtu a přiřaďte ho do entity:

1. Přejděte na **náklady** &gt; **cost management** &gt; **rozpočtu**.
2. Na stránce Správa rozpočtu v části **entity**vyberte entitu, u které chcete vytvořit rozpočet.
3. V roce rozpočtu vyberte v roce, ve kterém chcete vytvořit rozpočtu.
4. Pro každý měsíc nastavte hodnotu rozpočtu. Až skončíte, klikněte na **Uložit**.
V tomto příkladu měsíční rozpočet. června 2018 nastavena na 135,000 $. Celkový rozpočet rok je 1,615,000.00 $.
![vytvořit stránku rozpočtu, kde můžete nastavit rozpočet pro každý měsíc](./media/manage-budgets/set-budget.png)


Import souboru pro roční rozpočtu:

1. V části **Akce**vyberte **exportovat** a stáhněte prázdnou šablonu sdíleného svazku clusteru, kterou použijete jako základ pro rozpočet.
2. Zadejte soubor CSV s položky vašeho rozpočtu a uloží do místního prostředí.
3. V části **Akce**vyberte **importovat**.
4. Vyberte uložený soubor a klikněte na tlačítko **OK**.

Pokud chcete exportovat dokončený rozpočet jako soubor CSV, vyberte v části **Akce**možnost **exportovat** a Stáhněte soubor.

## <a name="view-budget-in-reports"></a>Zobrazit rozpočtu v sestavách

Po dokončení se váš rozpočet zobrazuje ve většině sestav nákladů za **náklady** &gt; **analýzy nákladů** a v sestavě náklady vs. rozpočet v čase. Sestavy můžete také plánovat na základě prahových hodnot rozpočtu pomocí **akcí**.

Tady je příklad sestavy analýzy nákladů. Zobrazuje celkový rozpočet a náklady na typy úloh a využití od začátku roku.

![Příklad sestavy analýzy nákladů s rozpočtu](./media/manage-budgets/cost-analysis-budget-example.png)

V tomto příkladu se předpokládá aktuální datum je 22. června. Bezplatně. června 2018 se $71,611.28 ve srovnání s měsíční rozpočtu 135,000 $. Náklady jsou mnohem nižší než měsíční rozpočtu, protože stále existují osm dní útraty do konce měsíce.

Dalším způsobem, jak zobrazit sestavu je podívat se na celkové náklady vs vašemu rozpočtu. Chcete-li zobrazit nahromaděné náklady, vyberte v části **Zobrazit nebo skrýt pole**celkové **náklady** a **Celkový rozpočet**. Tady je příklad ukazující celkové náklady od začátku roku.

![Příklad kumulovaných nákladů a celkových rozpočtů zobrazených v sestavě náklady vs. rozpočet v čase](./media/manage-budgets/accumulated-budget.png)

Někdy v budoucnu vaše celkové náklady může překročit vašemu rozpočtu. Můžete snadněji vidět, že pokud změníte zobrazení grafu na typ _řádku_ .

![Rozpočtu ukazuje spojnicový graf v náklady podle měsíců](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Vytvářet upozornění na rozpočet filtr

V předchozím příkladu vidíte, že celkové náklady dosaženy rozpočtu. Automatické rozpočtu výstrahy můžete vytvořit tak, aby se oznámení, když výdaje přístupy nebo překračuje vašemu rozpočtu. Výstraha představuje v podstatě naplánované sestavy s prahovou hodnotou. Zahrnout metriky pro prahovou hodnotu pro výstrahu rozpočtu:

- Zbývající náklady a rozpočet – chcete-li určit prahové hodnoty měny
- Procento nákladů a rozpočet – k určení procentuální hodnota prahová hodnota

Pojďme se podívat na příklad.

V sestavě náklady vs. rozpočet v průběhu času klikněte na **Akce** a pak vyberte **naplánovat sestavu**. Na kartě prahovou hodnotu vyberte metriku prahovou hodnotu. Například **procento nákladů vs. rozpočet**. Vyberte typ výstrahy a zadejte hodnotu v procentech rozpočtu. Pokud chcete dostávat oznámení jenom jednou, vyberte **počet po sobě jdoucích výstrah** a pak zadejte _1_. Klikněte na možnost **Uložit**.

![Vytváření upozornění na rozpočet Save plánu nebo momentální tohoto pole sestavy](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Další kroky

- Pokud jste ještě nedokončili první kurz pro Cloudyn, přečtěte si ho na stránce [Kontrola využití a nákladů](tutorial-review-usage.md).
- Přečtěte si další informace o [sestavách dostupných v Cloudyn](use-reports.md).
