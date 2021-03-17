---
title: Správa rozpočtů Cloudynu v Azure
description: Tento článek vám pomůže rychle vytvořit rozpočty a začít je spravovat v Cloudynu.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: vitavor
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: b7a26e7d94bbfea77bfc8121923377a086aef037
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690863"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Správa rozpočtů Azure pomocí Cloudynu

Nastavení rozpočtů a upozornění založených na rozpočtech usnadní řízení a odpovědnost v cloudu. Tento článek vám pomůže rychle vytvořit rozpočty a začít je spravovat v Cloudynu.

Pokud máte účet Enterprise nebo MSP, můžete k přiřazení měsíčních kvót rozpočtu různým obchodním jednotkám, oddělením nebo jiným entitám nákladů použít hierarchickou strukturu entit nákladů. Pokud máte účet Premium, můžete použít funkci správy rozpočtu, která se pak použije na celkové výdaje na cloud. Všechny rozpočty jsou ručně přiřazené.

Na základě přiřazených rozpočtů můžete nastavit upozornění na prahovou hodnotu založená na procentuální hodnotě spotřebovaného rozpočtu a definovat závažnost každé prahové hodnoty.

Sestavy rozpočtu zobrazují přiřazený rozpočet. Uživatelé můžou zobrazit, kdy je jejich útrata pod nebo na úrovni nominální hodnoty jejich spotřeby v průběhu času nebo ji překročí. Když vyberete možnost **Show/Hide Fields** (Zobrazit/skrýt pole) v horní části sestavy rozpočtu, můžete zobrazit náklady, rozpočet, kumulované náklady nebo celkový rozpočet.

Azure Cost Management nabízí podobné funkce jako Cloudyn. Azure Cost Management je nativní řešení Azure pro správu nákladů. Umožní vám analyzovat náklady, vytvářet a spravovat rozpočty, exportovat data, kontrolovat optimalizační doporučení k úspoře peněz a reagovat na ně. Další informace o rozpočtech v Cost Managementu najdete v tématu [Vytváření a správa rozpočtů](../costs/tutorial-acm-create-budgets.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="create-budgets"></a>Tvorba rozpočtů

Když vytváříte rozpočet, nastavíte ho pro fiskální rok a bude se používat pro konkrétní entitu.

Vytvoření rozpočtu a jeho přiřazení entitě:

1. Přejděte na **Costs** (Náklady) &gt; **Cost Management** (Správa nákladů) &gt; **Budget** (Rozpočet).
2. Na stránce Budget Management (Správa rozpočtu) v oblasti **Entities** (Entity) vyberte entitu, ve které chcete vytvořit rozpočet.
3. V roce rozpočtu vyberte rok, ve kterém chcete vytvořit rozpočet.
4. Pro každý měsíc nastavte hodnotu rozpočtu. Po dokončení klikněte na  **Save** (Uložit).
V tomto příkladu je měsíční rozpočet pro červen 2018 nastavený na 135 000 USD. Celkový rozpočet pro rok je 1 615 000,00 USD.
![Vytvoření stránky rozpočtu, kde nastavíte rozpočet pro každý měsíc](./media/manage-budgets/set-budget.png)


Soubor pro roční rozpočet naimportujete takto:

1. Výběrem možnosti **Export** (Exportovat) v oblasti **Actions** (Akce) stáhněte prázdnou šablonu CSV, kterou použijete jako základ pro rozpočet.
2. Vyplňte tento soubor CSV položkami rozpočtu a místně ho uložte.
3. V oblasti **Actions** (Akce) vyberte **Import** (Importovat).
4. Vyberte uložený soubor a klikněte na **OK**.

Pokud chcete vyplněný rozpočet exportovat jako soubor CSV, výběrem možnosti **Export** (Exportovat) v oblasti **Actions** (Akce) tento soubor stáhněte.

## <a name="view-budget-in-reports"></a>Zobrazení rozpočtu v sestavách

Po dokončení se rozpočet zobrazuje ve většině sestav nákladů v části **Costs** (Náklady) &gt; **Cost Analysis** (Analýza nákladů) a v sestavě nákladů oproti rozpočtu v průběhu času. Sestavy můžete také plánovat na základě prahových hodnot rozpočtu pomocí možnosti **Actions** (Akce).

Tady je příklad sestavy analýzy nákladů. Zobrazuje celkový rozpočet a náklady podle úlohy a typů využití od začátku roku.

![Ukázková sestava analýzy nákladů s rozpočtem](./media/manage-budgets/cost-analysis-budget-example.png)

V tomto příkladu předpokládáme, že aktuální datum je 22. června. Náklady pro červen 2018 jsou 71 611,28 USD, měsíční rozpočet je 135 000 USD. Náklady jsou mnohem nižší než měsíční rozpočet, protože do konce měsíce zbývá ještě osm dní útraty.

Dalším způsobem zobrazení sestavy je podívat se na kumulované náklady a rozpočet. Pokud chcete zobrazit kumulované náklady, vyberte v části **Show/Hide Fields** (Zobrazit/skrýt pole) možnost **Accumulated Cost** (Kumulované náklady) a **Total Budget** (Celkový rozpočet). Tady je příklad znázorňující kumulované náklady od začátku roku.

![Příklad kumulovaných nákladů a celkového rozpočtu zobrazených v sestavě nákladů oproti rozpočtu v průběhu času](./media/manage-budgets/accumulated-budget.png)

Kumulované náklady mohou někdy v budoucnu překročit rozpočet. Lepší přehled získáte tak, že zobrazení grafu změníte na _spojnicový_ typ.

![Rozpočet zobrazený ve spojnicovém grafu v sestavě nákladů podle měsíců](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Vytváření upozornění rozpočtu pro filtr

V předchozím příkladu vidíte, že se kumulované náklady blíží rozpočtu. Můžete vytvořit automatická upozornění rozpočtu, abyste byli upozorněni, když se útraty přiblíží vašemu rozpočtu nebo ho přesáhnou. Upozornění je v podstatě naplánovaná sestava s prahovou hodnotou. Metriky prahové hodnoty pro upozornění rozpočtu zahrnují:

- Zbývající náklady oproti rozpočtu – pro určení prahové hodnoty měny
- Procentuální hodnota nákladů oproti rozpočtu – pro určení prahové hodnoty v procentech

Pojďme se podívat na příklad.

V sestavě nákladů oproti rozpočtu v průběhu času klikněte na **Actions** (Akce) a potom vyberte **Schedule report** (Naplánovat sestavu). Na kartě Threshold (Prahová hodnota) vyberte metriku prahové hodnoty. Například **procentuální hodnota nákladů oproti rozpočtu**. Vyberte typ upozornění a zadejte procentuální hodnotu rozpočtu. Pokud chcete být upozorněni jenom jednou, vyberte **Number of consecutive alerts** (Počet po sobě jdoucích upozornění) a pak zadejte _1_. Klikněte na **Uložit**.

![Vytvoření upozornění rozpočtu v okně pro uložení nebo naplánování této sestavy](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Další kroky

- Pokud jste ještě nedokončili první kurz ke službě Cloudyn, přečtěte si část o [kontrole využití a nákladů](tutorial-review-usage.md).
- Přečtěte si další informace o [sestavách, které jsou k dispozici v Cloudynu](use-reports.md).
