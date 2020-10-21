---
title: Monitorování využití a útraty s využitím upozornění na náklady
description: Tento článek popisuje, jak vám upozornění na náklady umožňují sledovat využití a útraty ve službě Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: c59bd7f9bc8c5049572afdf93343222b30c0007b
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131900"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Použití upozornění na náklady ke sledování použití a výdajů

Tento článek vám pomůže pochopit a využít upozornění služby Cost Management k monitorování útraty a využití Azure. Upozornění na náklady se generují automaticky podle toho, kdy se spotřebovávají prostředky Azure. Funkce upozornění zobrazuje na jednom místě všechna aktivní upozornění na řízení nákladů a fakturaci. Když vaše spotřeba dosáhne dané prahové hodnoty, vygeneruje služba Cost Management upozornění. Existují tři typy upozornění na náklady: upozornění na rozpočet, upozornění na kredit a upozornění na kvótu útraty oddělení.

## <a name="budget-alerts"></a>Upozornění na rozpočet

Upozornění týkající se rozpočtu dostanete, když útrata na základě využití nebo nákladů dosáhne nebo překročí částku definovanou v [podmínce upozornění na rozpočet](tutorial-acm-create-budgets.md). Rozpočty služby Cost Management se vytvářejí pomocí webu Azure Portal nebo rozhraní API [Azure Consumption](/rest/api/consumption).

Na portálu Azure Portal jsou rozpočty definovány podle nákladů. Při použití rozhraní API Azure Consumption se rozpočty definují podle nákladů nebo podle využití spotřeby. Upozornění na rozpočet podporují rozpočty založené na nákladech i využití. Upozornění na rozpočet se generují automaticky při splnění podmínek upozornění na rozpočet. Všechna upozornění na náklady uvidíte na portálu Azure Portal. Pokaždé, když se vygeneruje upozornění, zobrazí se v upozorněních na náklady. Uživatelům v seznamu příjemců upozornění se také odešle e-mail s upozorněním.

K odesílání e-mailových upozornění v jiném jazyce můžete využít rozhraní API pro rozpočty. Další informace najdete v tématu [Podporovaná národní prostředí pro e-maily s upozorněními na rozpočet](manage-automation.md#supported-locales-for-budget-alert-emails).

## <a name="credit-alerts"></a>Upozornění kreditu

Upozornění kreditu vás upozorní, když vyčerpáte peněžní závazky kreditu Azure. Měnové závazky jsou určené organizacím se smlouvou Enterprise. Upozornění kreditu se generují automaticky při vyčerpání 90 % a 100 % zůstatku kreditu Azure. Pokaždé, když se vygeneruje upozornění, projeví se v upozorněních na náklady a v e-mailech posílaných vlastníkům účtu.

## <a name="department-spending-quota-alerts"></a>Upozornění na kvótu útraty oddělení

Upozornění na kvótu útraty oddělení vás upozorní, když útrata oddělení dosáhne pevné prahové hodnoty kvóty. Kvóty útraty se konfigurují na portálu EA. Při každém dosažení prahové hodnoty se vygeneruje e-mail vlastníkům oddělení a prahová hodnota se zobrazí v upozorněních na náklady. Například 50 % nebo 75 % kvóty.

## <a name="supported-alert-features-by-offer-categories"></a>Podporované funkce upozornění podle kategorií nabídek

Podpora typů upozornění závisí na typu účtu Azure, který máte (nabídka Microsoftu). V následující tabulce jsou uvedeny funkce upozornění, které jsou podporované různými nabídkami Microsoftu. Úplný seznam nabídek Microsoftu můžete zobrazit v tématu [Vysvětlení dat služby Cost Management](understand-cost-mgt-data.md).

| Typ upozornění | Smlouva Enterprise | Smlouva se zákazníkem Microsoftu | WebDirect / průběžné platby |
|---|---|---|---|
| Rozpočet | ✔ | ✔ | ✔ |
| Kredit | ✔ |✘ | ✘ |
| Kvóta útraty oddělení | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Zobrazení upozornění na náklady

Pokud chcete zobrazit upozornění na náklady, otevřete požadovaný obor v portálu Microsoft Azure a v nabídce vyberte **Rozpočty**. Pomocí oválku **Obor** můžete přepnout na jiný obor. V nabídce vyberte **Upozornění na náklady**. Další informace o oborech najdete v článku o [principech oborů a práci s nimi](understand-work-scopes.md).

![Obrázek s ukázkou upozornění zobrazených ve službě Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Celkový počet aktivních a zamítnutých upozornění se zobrazí na stránce s upozorněními na náklady.

Všechna upozornění zobrazují typ upozornění. Upozornění rozpočtu ukazuje důvod, proč bylo vygenerováno, a název rozpočtu, na který se vztahuje. Každé upozornění zobrazuje datum vygenerování, stav a rozsah (předplatné nebo skupinu pro správu), na který se upozornění vztahuje.

Mezi možné stavy patří **aktivní** a **zamítnuto**. Aktivní stav označuje, že je upozornění stále relevantní. Zamítnutý stav znamená, že někdo označil upozornění jako už nerelevantní.

Výběrem upozornění ze seznamu zobrazíte jeho podrobnosti. V podrobnostech upozornění jsou obsaženy další informace. Upozornění rozpočtu zahrnují odkaz na rozpočet. Pokud je pro upozornění rozpočtu k dispozici doporučení, zobrazí se také odkaz na toto doporučení. Upozornění rozpočtu, kreditu a kvóty útraty oddělení mají odkaz na analýzu v analýze nákladů, kde můžete prozkoumat náklady pro rozsah výstrahy. Následující příklad ukazuje útratu za oddělení s podrobnostmi upozornění.

![Obrázek s ukázkou útraty za oddělení s podrobnostmi upozornění](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Když si zobrazíte podrobnosti o zamítnutém upozornění, můžete ho znovu aktivovat, pokud je to potřeba udělat ručně. Příklad ukazuje následující obrázek.

![Obrázek s ukázkou znázorňující možnosti zamítnutí a aktivace](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Viz také

- Pokud jste ještě nevytvořili rozpočet nebo pro něj nenastavili podmínky upozornění, přečtěte si kurz [Vytvoření a správa rozpočtu](tutorial-acm-create-budgets.md).