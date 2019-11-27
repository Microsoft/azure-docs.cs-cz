---
title: Monitorování využití a útraty pomocí upozornění na náklady | Microsoft Docs
description: Tento článek popisuje, jak vám náklady umožňují sledovat využití a výdaje v Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: alavital
ms.custom: ''
ms.openlocfilehash: 4be484cdff2014f11c872da9a246ef8406447712
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230121"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Použití upozornění na náklady ke sledování použití a výdajů

Tento článek vám pomůže pochopit a používat výstrahy Cost Management k monitorování využití a útraty Azure. Náklady se generují automaticky na základě toho, kdy se prostředky Azure spotřebovávají. Výstrahy zobrazují na jednom místě všechna aktivní upozornění na správu a účtování nákladů. Když vaše spotřeba dosáhne dané prahové hodnoty, výstrahy vygeneruje Cost Management. Existují tři typy upozornění na náklady: výstrahy rozpočtu, výstrahy kreditu a upozornění na kvótu útraty oddělení.

## <a name="budget-alerts"></a>Výstrahy rozpočtu

Výstrahy týkající se rozpočtu vás upozorní, když útrata vychází z využití nebo nákladů, dosáhne nebo překračuje množství definované v [podmínce výstrahy rozpočtu](tutorial-acm-create-budgets.md). Cost Management rozpočty se vytvářejí pomocí Azure Portal nebo rozhraní API pro [využití Azure](https://docs.microsoft.com/rest/api/consumption) .

V Azure Portal jsou rozpočty definovány podle nákladů. S využitím rozhraní API pro využití Azure se rozpočty definují náklady nebo využití spotřeby. Výstrahy rozpočtu podporují rozpočty založené na nákladech i využití. Výstrahy rozpočtu jsou generovány automaticky při splnění podmínek upozornění rozpočtu. Všechny výstrahy s náklady můžete zobrazit v Azure Portal. Pokaždé, když se vygeneruje výstraha, zobrazí se v upozorněních na náklady. Uživatelům v seznamu příjemci výstrahy se také pošle e-mail s upozorněním.

## <a name="credit-alerts"></a>Upozornění na kredit

Upozornění na kredit vás upozorní, když se spotřebují peněžní závazky Azure na kredity. Měnové závazky jsou pro organizace se smlouvou Enterprise. Upozornění na kredit se generují automaticky v 90% a na 100% bilance kreditu Azure. Pokaždé, když se vygeneruje výstraha, projeví se v upozorněních na náklady a v e-mailech odesílaných vlastníkům účtu.

## <a name="department-spending-quota-alerts"></a>Výstrahy kvóty útraty oddělení

Upozornění na kvótu útraty oddělení vás upozorní, když útrata v oddělení dosáhne pevné prahové hodnoty kvóty. Kvóty útraty se konfigurují na portálu EA. Pokaždé, když se splní prahová hodnota, vygeneruje e-mail vlastníkům oddělení a zobrazí se v nákladových výstrahách. Například 50% nebo 75% kvóty.

## <a name="supported-alert-features-by-offer-categories"></a>Podporované funkce výstrah podle kategorií nabídky

Podpora typů výstrah závisí na typu účtu Azure, který máte (nabídka Microsoft). V následující tabulce jsou uvedeny funkce výstrah, které podporuje různé nabídky společnosti Microsoft. Úplný seznam nabídek Microsoftu můžete zobrazit na adrese [cost management data](understand-cost-mgt-data.md).

| Typ upozornění | Smlouva Enterprise | Smlouva se zákazníkem Microsoftu | Web Direct/průběžné platby |
|---|---|---|---|
| Souhrnné | ✔ | ✔ | ✔ |
| Prospěch | ✔ |✘ | ✘ |
| Kvóta útraty pro oddělení | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Zobrazit upozornění na náklady

Chcete-li zobrazit upozornění na náklady, otevřete požadovaný obor v Azure Portal a v nabídce vyberte **rozpočty** . K přepnutí do jiného oboru použijte modul pilla **rozsahu** . V nabídce vyberte možnost **nákladové výstrahy** . Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

![Příklad obrázku výstrah zobrazených v Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Celkový počet aktivních a neodeslaných výstrah se zobrazí na stránce s náklady na upozornění.

Všechny výstrahy zobrazují typ výstrahy. Upozornění rozpočtu ukazuje důvod, proč byl vygenerován, a název rozpočtu, na který se vztahuje. Každé upozornění zobrazuje datum, které bylo vygenerováno, jeho stav a obor (předplatné nebo skupina pro správu), na který se výstraha vztahuje.

Možný stav zahrnuje **aktivní** a **zavřený**. Aktivní stav označuje, že je výstraha stále relevantní. Stav zrušeno znamená, že někdo označil výstrahu, aby ji nastavil jako již nerelevantní.

Výběrem výstrahy ze seznamu zobrazíte její podrobnosti. Podrobnosti výstrahy zobrazí další informace o výstraze. Výstrahy rozpočtu zahrnují odkaz na rozpočet. Pokud je pro výstrahu rozpočtu k dispozici doporučení, zobrazí se také odkaz na toto doporučení. Výstrahy kvót rozpočtu, kreditu a výdajů na oddělení mají odkaz na analýzu v analýze nákladů, kde můžete prozkoumat náklady na rozsah výstrahy. Následující příklad ukazuje útratu za oddělení s podrobnostmi výstrahy.

![Příklad obrázku ukazujícího útraty pro oddělení s podrobnostmi výstrahy](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Když si zobrazíte podrobnosti o odeslaném upozornění, můžete je znovu aktivovat, pokud je potřeba ruční akce. Příklad ukazuje následující obrázek.

![Příklad obrázku znázorňujícího možnosti zavřít a znovu aktivovat](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Viz také

- Pokud jste ještě nevytvořili podmínky rozpočtu nebo nastavování výstrah pro rozpočet, vyplňte kurz [Vytvoření a Správa rozpočtů](tutorial-acm-create-budgets.md) .
