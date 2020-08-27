---
title: Monitorování využití a útraty s využitím upozornění na náklady
description: Tento článek popisuje, jak vám upozornění na náklady umožňují sledovat využití a útraty ve službě Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: aeeb630cf15aedd62c085e2070e08fd223656094
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683432"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Použití upozornění na náklady ke sledování použití a výdajů

Tento článek vám pomůže pochopit a využít upozornění služby Cost Management k monitorování útraty a využití Azure. Upozornění na náklady se generují automaticky podle toho, kdy se spotřebovávají prostředky Azure. Funkce upozornění zobrazuje na jednom místě všechna aktivní upozornění na řízení nákladů a fakturaci. Když vaše spotřeba dosáhne dané prahové hodnoty, vygeneruje služba Cost Management upozornění. Existují tři typy upozornění na náklady: upozornění na rozpočet, upozornění na kredit a upozornění na kvótu útraty oddělení.

## <a name="budget-alerts"></a>Upozornění na rozpočet

Upozornění týkající se rozpočtu dostanete, když útrata na základě využití nebo nákladů dosáhne nebo překročí částku definovanou v [podmínce upozornění na rozpočet](tutorial-acm-create-budgets.md). Rozpočty služby Cost Management se vytvářejí pomocí webu Azure Portal nebo rozhraní API [Azure Consumption](https://docs.microsoft.com/rest/api/consumption).

Na portálu Azure Portal jsou rozpočty definovány podle nákladů. Při použití rozhraní API Azure Consumption se rozpočty definují podle nákladů nebo podle využití spotřeby. Upozornění na rozpočet podporují rozpočty založené na nákladech i využití. Upozornění na rozpočet se generují automaticky při splnění podmínek upozornění na rozpočet. Všechna upozornění na náklady uvidíte na portálu Azure Portal. Pokaždé, když se vygeneruje upozornění, zobrazí se v upozorněních na náklady. Uživatelům v seznamu příjemců upozornění se také odešle e-mail s upozorněním.

## <a name="credit-alerts"></a>Upozornění na kredit

Upozornění na kredit dostanete, když se v Azure spotřebují peněžní závazky na kredit. Peněžní závazky existují pro organizace se smlouvami Enterprise. Upozornění na kredit se generují automaticky při 90% a 100% vyčerpání zůstatku kreditu v Azure. Pokaždé, když se vygeneruje upozornění, objeví se v upozorněních na náklady a v e-mailech odesílaných vlastníkům účtů.

## <a name="department-spending-quota-alerts"></a>Upozornění na kvótu útraty oddělení

Upozornění na kvótu útraty oddělení dostanete, když útrata v oddělení dosáhne pevné prahové hodnoty kvóty. Kvóty útraty se konfigurují na portálu EA Portal. Při každém naplnění prahové hodnoty se vygeneruje e-mail vlastníkům oddělení a zobrazí se v upozorněních na náklady. Příklad: 50 % nebo 75 % kvóty.

## <a name="supported-alert-features-by-offer-categories"></a>Podporované funkce upozornění podle kategorií nabídky

Podpora typů upozornění závisí na typu účtu Azure, který máte (nabídka Microsoftu). V následující tabulce jsou uvedeny funkce upozornění, které podporují různé nabídky Microsoftu. Úplný seznam nabídek Microsoftu můžete zobrazit v tématu [Vysvětlení dat služby Cost Management](understand-cost-mgt-data.md).

| Typ upozornění | Smlouva Enterprise | Smlouva se zákazníkem Microsoftu | WebDirect / průběžné platby |
|---|---|---|---|
| Rozpočet | ✔ | ✔ | ✔ |
| Kredit | ✔ |✘ | ✘ |
| Kvóta útraty oddělení | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Zobrazení upozornění na náklady

Chcete-li zobrazit upozornění na náklady, otevřete požadovaný rozsah na webu Azure Portal a v nabídce vyberte **Rozpočty**. Pomocí tlačítka **Rozsah** přepněte na jiný rozsah. V nabídce vyberte **Upozornění na náklady**. Další informace o rozsazích najdete v tématu [Vysvětlení a práce s rozsahy](understand-work-scopes.md).

![Obrázek s ukázkou upozornění zobrazených ve službě Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Celkový počet aktivních a zamítnutých upozornění se zobrazí na stránce upozornění na náklady.

U všech upozornění se zobrazuje typ upozornění. Upozornění na rozpočet uvádí důvod, proč bylo vygenerováno, a název rozpočtu, ke kterému se vztahuje. Každé upozornění zobrazuje datum vygenerování, stav a rozsah (předplatné nebo skupinu pro správu), na který se upozornění vztahuje.

Možné stavy jsou **aktivní** a **zamítnuto**. Aktivní stav označuje, že je upozornění stále relevantní. Zamítnutý stav znamená, že někdo upozornění označil jako již nerelevantní.

Výběrem upozornění ze seznamu zobrazíte jeho podrobnosti. V podrobnostech upozornění najdete další informace o upozornění. Upozornění na rozpočet obsahují odkaz na rozpočet. Pokud je pro upozornění na rozpočet k dispozici doporučení, zobrazí se také odkaz na toto doporučení. Upozornění na rozpočet, kredit a kvótu útraty oddělení mají odkaz na analýzu v analýze nákladů, kde můžete prozkoumat náklady pro daný rozsah upozornění. Následující příklad ukazuje útratu za oddělení s podrobnostmi upozornění.

![Obrázek s ukázkou útraty za oddělení s podrobnostmi upozornění](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Když si zobrazíte podrobnosti o zamítnutém upozornění, můžete ho znovu aktivovat, pokud je to potřeba udělat ručně. Příklad ukazuje následující obrázek.

![Obrázek s ukázkou znázorňující možnosti zamítnutí a aktivace](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Viz také

- Pokud jste ještě nevytvořili rozpočet nebo pro něj nenastavili podmínky upozornění, přečtěte si kurz [Vytvoření a správa rozpočtu](tutorial-acm-create-budgets.md).
