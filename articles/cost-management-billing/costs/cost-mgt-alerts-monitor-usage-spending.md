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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988497"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Použití upozornění na náklady ke sledování použití a výdajů

Tento článek vám pomůže pochopit a používat výstrahy Cost Management k monitorování využití a útraty Azure. Náklady se generují automaticky na základě toho, kdy se prostředky Azure spotřebovávají. Výstrahy zobrazují na jednom místě všechna aktivní upozornění na správu a účtování nákladů. Když vaše spotřeba dosáhne dané prahové hodnoty, výstrahy vygeneruje Cost Management. Existují tři typy upozornění na náklady: upozornění rozpočtu, upozornění kreditu a upozornění na kvótu útraty oddělení.

## <a name="budget-alerts"></a>Upozornění rozpočtu

Výstrahy týkající se rozpočtu vás upozorní, když útrata vychází z využití nebo nákladů, dosáhne nebo překračuje množství definované v [podmínce výstrahy rozpočtu](tutorial-acm-create-budgets.md). Cost Management rozpočty se vytvářejí pomocí Azure Portal nebo rozhraní API pro [využití Azure](https://docs.microsoft.com/rest/api/consumption) .

V portálu Microsoft Azure jsou rozpočty definovány podle nákladů. S využitím rozhraní API pro využití Azure se rozpočty definují podle nákladů nebo využití spotřeby. Upozornění rozpočtu podporují rozpočty založené na nákladech i na využití. Upozornění rozpočtu se generují automaticky při splnění podmínek upozornění rozpočtu. Všechna upozornění na náklady můžete zobrazit v portálu Microsoft Azure. Pokaždé, když se vygeneruje upozornění, zobrazí se v upozorněních na náklady. Uživatelům v seznamu příjemců upozornění rozpočtu se také pošle e-mail s upozorněním.

## <a name="credit-alerts"></a>Upozornění kreditu

Upozornění kreditu vás upozorní, když vyčerpáte peněžní závazky kreditu Azure. Měnové závazky jsou určené organizacím se smlouvou Enterprise. Upozornění kreditu se generují automaticky při vyčerpání 90 % a 100 % zůstatku kreditu Azure. Pokaždé, když se vygeneruje upozornění, projeví se v upozorněních na náklady a v e-mailech posílaných vlastníkům účtu.

## <a name="department-spending-quota-alerts"></a>Upozornění na kvótu útraty oddělení

Upozornění na kvótu útraty oddělení vás upozorní, když útrata oddělení dosáhne pevné prahové hodnoty kvóty. Kvóty útraty se konfigurují na portálu EA. Při každém dosažení prahové hodnoty se vygeneruje e-mail vlastníkům oddělení a prahová hodnota se zobrazí v upozorněních na náklady. Například 50 % nebo 75 % kvóty.

## <a name="supported-alert-features-by-offer-categories"></a>Podporované funkce upozornění podle kategorií nabídek

Podpora typů upozornění závisí na typu účtu Azure, který máte (nabídka Microsoftu). V následující tabulce jsou uvedeny funkce upozornění, které jsou podporované různými nabídkami Microsoftu. Úplný seznam nabídek Microsoftu můžete zobrazit na adrese [cost management data](understand-cost-mgt-data.md).

| Typ upozornění | Smlouva Enterprise | Smlouva se zákazníkem Microsoftu | Web Direct/průběžné platby |
|---|---|---|---|
| Rozpočet | ✔ | ✔ | ✔ |
| Kredit | ✔ |✘ | ✘ |
| Kvóta útraty oddělení | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Zobrazení upozornění na náklady

Pokud chcete zobrazit upozornění na náklady, otevřete požadovaný obor v portálu Microsoft Azure a v nabídce vyberte **Rozpočty**. Pomocí oválku **Obor** můžete přepnout na jiný obor. V nabídce vyberte **Upozornění na náklady**. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

![Příklad obrázku upozornění zobrazených v Cost Managementu](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Celkový počet aktivních a zamítnutých upozornění se zobrazí na stránce s upozorněními na náklady.

Všechna upozornění zobrazují typ upozornění. Upozornění rozpočtu ukazuje důvod, proč bylo vygenerováno, a název rozpočtu, na který se vztahuje. Každé upozornění zobrazuje datum, které bylo vygenerováno, jeho stav a obor (předplatné nebo skupina pro správu), na který se výstraha vztahuje.

Mezi možné stavy patří **aktivní** a **zamítnuto**. Aktivní stav označuje, že je upozornění stále relevantní. Zamítnutý stav znamená, že někdo označil upozornění jako už nerelevantní.

Výběrem upozornění ze seznamu zobrazíte jeho podrobnosti. V podrobnostech upozornění jsou obsaženy další informace. Upozornění rozpočtu zahrnují odkaz na rozpočet. Pokud je pro upozornění rozpočtu k dispozici doporučení, zobrazí se také odkaz na toto doporučení. Upozornění rozpočtu, kreditu a kvóty útraty oddělení mají odkaz na analýzu v analýze nákladů, kde můžete prozkoumat náklady pro rozsah výstrahy. Následující příklad ukazuje útratu za oddělení s podrobnostmi výstrahy.

![Příklad obrázku ukazujícího útraty pro oddělení s podrobnostmi výstrahy](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Když si zobrazíte podrobnosti o odeslaném upozornění, můžete je znovu aktivovat, pokud je potřeba ruční akce. Příklad ukazuje následující obrázek.

![Příklad obrázku znázorňujícího možnosti zavřít a znovu aktivovat](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Další informace najdete v tématech

- Pokud jste ještě nevytvořili podmínky rozpočtu nebo nastavování výstrah pro rozpočet, vyplňte kurz [Vytvoření a Správa rozpočtů](tutorial-acm-create-budgets.md) .
