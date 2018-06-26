---
title: Spravovat rozpočty v Azure náklady na správu | Microsoft Docs
description: Tento článek vám pomůže vytvořit a spravovat rozpočty v náklady na správu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/25/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 9d6bf29909393846ec17a1bcc210fb989efd7f99
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938376"
---
# <a name="manage-budgets"></a>Spravovat rozpočty

Nastavení rozpočty a nároky na základě výstrahy zvýšení cloudu zásad správného řízení a odpovědnosti za nápovědy. Tento článek pomáhá rychle vytvořit rozpočty a spustit správu je v náklady na správu.

Když máte účet organizace nebo MSP, můžete přiřadit různé organizační jednotky, oddělení nebo jiná entita náklady na měsíční kvóty nároky strukturu entity hierarchické náklady. Když máte účet Premium, můžete použít funkce správy rozpočtu, které se pak použije k vaší výdajů celý cloudu. Jsou všechny rozpočty přiřadit ručně.

Podle toho, přiřazené rozpočty, můžete nastavit prahové hodnoty výstrahy založené na procento rozpočtem, která se využívá a definovat závažnost jednotlivých prahové hodnoty.

Nároky sestavy zobrazit přiřazené nároky. Uživatelé mohou zobrazit, když se jejich výdaje přes, v části nebo nominální jejich spotřebu v čase. Když vyberete **zobrazit nebo skrýt pole** v horní části sestavy rozpočtu, můžete zobrazit náklady, rozpočtu, Akumulovaná náklady nebo celkové nároky.

## <a name="create-budgets"></a>Vytvoření rozpočty

Když vytvoříte rozpočtu, můžete nastavit pro fiskálního roku a platí pro konkrétní entitu.

Vytvoření nároky a přiřaďte ho na entitu:

1. Přejděte na **náklady** &gt; **Správa nákladů** &gt; **nároky**.
2. Na stránce Správa nároky pod **entity**, vyberte entity, kde chcete vytvořit rozpočet.
3. V roce rozpočtu vyberte rok, kde chcete vytvořit rozpočtu.
4. Pro každý měsíc nastavte hodnotu nároky. Když jste hotovi, klikněte na tlačítko **Uložit**.
V tomto příkladu měsíční rozpočet pro června 2018 nastavena na 135,000 $. Celkový počet rozpočet roku je $1,615,000.00.
![Vytvořit rozpočet](./media/manage-budgets/set-budget.png)


Chcete-li importovat soubor pro roční rozpočet:

1. V části **akce**, vyberte **exportovat** stáhnout prázdnou šablonu sdíleného svazku clusteru používat jako vaše základ pro rozpočet.
2. Zadejte soubor CSV s vaší položky rozpočtu a uložit místně.
3. V části **akce**, vyberte **Import**.
4. Vyberte uložený soubor a pak klikněte na tlačítko **OK**.

Pro export dokončena rozpočet jako soubor CSV v části **akce**, vyberte **exportovat** stažení souboru.

## <a name="view-budget-in-reports"></a>Nároky zobrazení v sestavách

Po dokončení rozpočet se zobrazí v sestavách většina náklady **náklady** &gt; **analýza nákladů** a v sadě vs náklady. Sestava nároky v čase. Můžete také naplánovat sestavy na základě prahových hodnot nároky pomocí **akce**.

Tady je příklad sestava analýzy náklady. Zobrazuje celkové nároky a náklady na typy úloh a využití od začátku roku.

![Sestava analýzy náklady příklad s rozpočtu](./media/manage-budgets/cost-analysis-budget-example.png)

V tomto příkladu se předpokládá, aktuální datum následuje června 22. Náklady pro června 2018 je $71,611.28 ve srovnání s měsíční rozpočet $135,000. Náklady na je mnohem nižší než měsíční rozpočtu, protože stále existují osm dní výdaje do konce měsíce.

Další možností zobrazení sestavy je prohlédnout Akumulovaná náklady vs rozpočet. V části zobrazíte souhrnné náklady, **zobrazit nebo skrýt pole**, vyberte **Akumulovaná náklady** a **celkový rozpočet**. Tady je příklad zobrazující celkové náklady na od začátku roku.

![Akumulovaná nároky](./media/manage-budgets/accumulated-budget.png)

Někdy v budoucnu Kumulované náklady může překročit rozpočet. Můžete snadno uvidíte, že pokud změníte zobrazení grafu _řádku_ typu.

![Nároky ukazuje spojnicový graf](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Vytvořte výstrahy nároky pro filtr

V předchozím příkladu vidíte, že náklady na Akumulovaná dosaženy rozpočtu. Automatické nároky výstrahy můžete vytvořit tak, aby se upozorněni, když výdaje přístupy nebo překračuje rozpočet. Výstraha je v podstatě, plánované sestavy s prahovou hodnotou. Nároky prahové hodnoty pro výstrahu metrikám patří:

- Zbývající náklady a rozpočet – chcete-li zadat mezní hodnotu hodnotu měny
- Procento nákladů a rozpočet – chcete-li zadat mezní hodnotu procentuální hodnotu

Podívejme se na příklad.

V sadě vs náklady. Rozpočet přes čas sestavy, klikněte na tlačítko **akce** a pak vyberte **Naplánování sestavy**. Na kartě prahová hodnota vyberte metriku prahovou hodnotu. Například **rozpočet vs procento nákladů**. Vyberte typ výstrahy a zadejte hodnotu v procentech rozpočtu. Pokud chcete zobrazit oznámení, jenom jednou, vyberte **počet po sobě jdoucích výstrah** a pak zadejte _1_. Klikněte na **Uložit**.

![Výstraha nároky](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Další postup

- Pokud jste ještě nedokončili první kurz pro náklady na správu, přečtěte si ho na [zkontrolujte využití a náklady na](https://docs.microsoft.com/en-us/azure/cost-management/tutorial-review-usage).
- Další informace o [sestavy, které jsou k dispozici v náklady na správu](use-reports.md).
