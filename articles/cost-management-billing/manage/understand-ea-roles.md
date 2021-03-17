---
title: Principy rolí správce smluv Enterprise (EA) v Azure
description: Přečtěte si informace o rolích správce smlouvy Enterprise v Azure. Můžete přiřadit pět různých rolí správce.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 1ceed171b0516e293ffe58bca0225d3d3dfdb414
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094658"
---
# <a name="managing-azure-enterprise-agreement-roles"></a>Správa rolí pro smlouvu Enterprise v Azure

Zákazníci Azure se smlouvou Enterprise (EA) můžou přiřazovat pět různých rolí správce, které jim usnadní správu využití a výdajů v rámci organizace:

- Podnikový správce
- Podnikový správce (jen pro čtení)<sup>1</sup>
- Správce oddělení
- Správce oddělení (jen pro čtení)
- Vlastník účtu<sup>2</sup>

<sup>1</sup> V této roli bude příjemce faktury za smlouvu Enterprise.

<sup>2</sup> Fakturační kontakt nejde změnit ani přidat na webu Azure EA Portal a přidá se do registrace EA podle toho, který uživatel je nastavený jako fakturační kontakt na úrovni smlouvy. Případnou změnu fakturačního kontaktu je potřeba prostřednictvím partnera / softwarového poradce vyžádat z regionálního operačního centra.

První správce registrace, který je nastavený během zřizování registrace, určuje typ ověřování pro účet fakturačního kontaktu. Fakturační kontakt po přidání na EA Portal jako správce jen pro čtení dostává ověřování pomocí účtu Microsoft. 

Pokud je například jako původní typ ověřování nastavený typ Smíšený, EA se přidá jako účet Microsoft a fakturační kontakt bude mít oprávnění správce EA jen pro čtení. Pokud správce EA neschválí pro stávající fakturační kontakt nechválí ověřování pomocí účtu Microsoft, může správce EA dotčeného uživatele odstranit a požádat zákazníka, aby tohoto uživatele přidal znovu jako správce jen pro čtení s nastavením Pracovní nebo školní účet na úrovni registrace na webu EA Portal.

Tyto role jsou specifické pro správu smluv Azure Enterprise a doplňují předdefinované role, pomocí kterých Azure řídí přístup k prostředkům. Další informace najdete v tématu [Předdefinované role v Azure](../../role-based-access-control/built-in-roles.md).

## <a name="azure-enterprise-portal-hierarchy"></a>Hierarchie portálu Azure Enterprise

Hierarchie portálu Azure Enterprise se skládá z těchto částí:

- **Portál Azure Enterprise** je online portál pro správu, který pomáhá se správou nákladů na služby Azure EA. Můžete:

  - Vytvořit hierarchii Azure EA s odděleními, účty a předplatnými.
  - Párovat náklady na spotřebované služby, stahovat sestavy o využití a zobrazovat ceníky.
  - Vytvořit klíče rozhraní API pro registraci.

- **Oddělení** usnadňují segmentaci nákladů do logických seskupení. Oddělení umožňují nastavit rozpočet nebo kvótu na úrovni oddělení.

- **Účty** jsou organizační jednotky na portálu Azure Enterprise. Pomocí účtů můžete spravovat předplatná a přistupovat k sestavám.

- **Předplatná** představují nejmenší jednotku na portálu Azure Enterprise. Jedná se o kontejnery pro služby Azure spravované správcem služeb.

Následující diagram znázorňuje jednoduché hierarchie Azure EA.

![Diagram jednoduchých hierarchií Azure EA](./media/understand-ea-roles/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Role podnikových uživatelů

Následující role administrativního uživatele jsou součástí registrace Enterprise:

- Podnikový správce
- Správce oddělení
- Vlastník účtu
- Správce služeb
- Kontakt pro oznámení

Role plní úlohy na dvou různých portálech. [Portál Azure Enterprise](https://ea.azure.com) se používá ke správě fakturace a nákladů a web [Azure Portal](https://portal.azure.com) ke správě služeb Azure.

Role uživatelů se přidružují k jednotlivým uživatelským účtům. Pro ověření identity uživatele je nutné, aby měl platný pracovní nebo školní účet nebo účet Microsoft. Zajistěte, aby byl každý účet přidružený k e-mailové adrese, která je aktivně sledovaná. Na tuto e-mailovou adresu se budou odesílat oznámení týkající se účtu.

Při nastavování uživatelů můžete k roli Podnikový správce přiřadit více účtů. Roli vlastníka účtu ale může mít pouze jeden účet. Jednomu účtu můžete také přiřadit jak roli podnikového správce, tak roli vlastníka účtu.

### <a name="enterprise-administrator"></a>Podnikový správce

Uživatelé s touto rolí mají nejvyšší úroveň přístupu. Mohou:

- spravovat účty a jejich vlastníky,
- spravovat jiné podnikové správce,
- spravovat správce oddělení,
- spravovat kontakty pro oznámení,
- zobrazovat využití napříč všemi účty,
- zobrazovat nefakturované poplatky napříč všemi účty.
- zobrazovat a spravovat všechny objednávky a rezervace, které souvisejí se smlouvou Enterprise.
  - Podnikový správce (jen pro čtení) může zobrazovat rezervace a objednávky rezervací. Nemůže je ale spravovat.

V podnikové registraci můžete mít více podnikových správců. Podnikovým správcům můžete udělit přístup jen pro čtení. Všichni navíc přebírají roli Správce oddělení.

### <a name="department-administrator"></a>Správce oddělení

Uživatelé s tou rolí mohou:

- vytvářet a spravovat oddělení,
- vytvářet nové vlastníky účtů,
- zobrazovat podrobnosti o využití pro oddělení, která spravují,
- zobrazovat náklady, pokud mají udělená potřebná oprávnění.

V podnikové registraci můžete mít více správců oddělení.

Při úpravách nebo vytváření nového správce oddělení můžete tomuto správci udělit přístup jen pro čtení. Možnost Read-Only (Jen pro čtení) nastavte na **Yes** (Ano).

### <a name="account-owner"></a>Vlastník účtu

Uživatelé s tou rolí mohou:

- vytvářet a spravovat předplatná,
- vytvářet správce služeb,
- zobrazovat využití u předplatných.

Každý účet vyžaduje jedinečný pracovní nebo školní účet nebo účet Microsoft. Další informace o správcovských rolích na portálu Azure Enterprise najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Správce služeb

Role Správce služeb má oprávnění ke správě služeb přes Azure Portal a k přiřazování role spolusprávce jiným uživatelům.

### <a name="notification-contact"></a>Kontakt pro oznámení

Kontakt pro oznámení dostává oznámení o využití související s příslušnou smlouvou.

Následující části popisují omezení a možnosti jednotlivých rolí.

## <a name="user-limit-for-admin-roles"></a>Limit počtu uživatelů pro role správce

|Role| Limit počtu uživatelů|
|---|---|
|Podnikový správce|Unlimited|
|Podnikový správce (jen pro čtení)|Unlimited|
|Správce oddělení|Unlimited|
|Správce oddělení (jen pro čtení)|Unlimited|
|Vlastník účtu|1 na účet<sup>3</sup>|

<sup>3</sup> Každý účet vyžaduje jedinečný účet Microsoft nebo pracovní nebo školní účet.

## <a name="organization-structure-and-permissions-by-role"></a>Organizační struktura a oprávnění podle role

|Úlohy| Podnikový správce|Podnikový správce (jen pro čtení)|Správce oddělení|Správce oddělení (jen pro čtení)|Vlastník účtu| Partner|
|---|---|---|---|---|---|---|
|Zobrazení podnikových správců|✔|✔|✘|✘|✘|✔|
|Přidání nebo odebrání podnikových správců|✔|✘|✘|✘|✘|✘|
|Zobrazení kontaktů pro oznámení<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|Přidání nebo odebrání kontaktů pro oznámení<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|Vytvoření a správa oddělení |✔|✘|✘|✘|✘|✘|
|Zobrazení správců oddělení|✔|✔|✔|✔|✘|✔|
|Přidání nebo odebrání správců oddělení|✔|✘|✔|✘|✘|✘|
|Zobrazení účtů v registraci |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Přidání účtů do registrace a změna vlastníka účtu|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|Vytvoření a správa předplatných a oprávnění předplatných|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Kontaktům pro oznámení se odesílá e-mailová komunikace týkající se smlouvy Azure Enterprise.
- <sup>5</sup> Úloha je omezená na účty ve vašem oddělení.

## <a name="add-a-new-enterprise-administrator"></a>Přidání nového podnikového správce

Podnikoví správci mají při správě registrace do Azure EA největší oprávnění. Úvodní správce Azure EA byl vytvořen při nastavení smlouvy EA. Nové správce můžete kdykoliv přidat nebo odebrat. Nové správce přidávají jenom stávající správci. Další informace o přidání dalších podnikových správců najdete v tématu [Vytvoření dalšího podnikového správce](ea-portal-administration.md#create-another-enterprise-administrator). Další informace o rolích a úlohách fakturačního profilu najdete v tématu [Role a úlohy fakturačního profilu](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>Aktualizace stavu vlastníka účtu z Čeká na vyřízení na Aktivní

Při prvním přidání nových vlastníků účtů do registrace Azure EA se u nových vlastníků zobrazí stav _Čeká na vyřízení_. Jakmile noví vlastníci účtu dostanou úvodní aktivační e-mail, můžou se přihlásit a aktivovat si účet. Jakmile si účet aktivují, stav účtu se aktualizuje z _Čeká na vyřízení_ na _Aktivní_. Vlastník účtu si musí přečíst zprávu upozornění a pak vybrat **Pokračovat**. Při vytvoření komerčního účtu se může novým uživatelům zobrazit výzva k zadání jména a příjmení. Pokud se zobrazí, musí zadat požadované informace, aby mohli pokračovat, a pak se účet aktivuje.

## <a name="add-a-department-admin"></a>Přidání správce oddělení

Jakmile správce Azure EA vytvoří oddělení, může přidat správce oddělení a přidružit je k oddělením. Správce oddělení může vytvářet nové účty. Nové účty jsou potřeba pro vytvářená předplatná Azure EA.

Další informace o přidání správce oddělení najdete v tématu věnovaném [vytvoření správce oddělení Azure EA](ea-portal-administration.md#add-a-department-administrator).

## <a name="usage-and-costs-access-by-role"></a>Přístup k využití a nákladům podle role

|Úlohy| Podnikový správce|Podnikový správce (jen pro čtení)|Správce oddělení|Správce oddělení (jen pro čtení) |Vlastník účtu| Partner|
|---|---|---|---|---|---|---|
|Zobrazení zůstatku kreditu včetně zálohy na Azure|✔|✔|✘|✘|✘|✔|
|Zobrazení kvót útraty oddělení|✔|✔|✘|✘|✘|✔|
|Nastavení kvót útraty oddělení|✔|✘|✘|✘|✘|✘|
|Zobrazení ceníku EA v organizaci|✔|✔|✘|✘|✘|✔|
|Zobrazení podrobností o využití a nákladech|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Správa prostředků na webu Azure Portal|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> Podnikový správce musí na webu Enterprise Portal povolit zásadu **DA view charges** (Správce oddělení může zobrazit náklady). Správce oddělení pak může zobrazit podrobnosti o nákladech daného oddělení.
- <sup>7</sup> Podnikový správce musí na webu Enterprise Portal povolit zásadu **AO view charges** (Vlastník účtu může zobrazit náklady). Vlastník účtu pak může zobrazit podrobnosti o nákladech daného účtu.

## <a name="see-pricing-for-different-user-roles"></a>Zobrazit ceny pro různé role uživatelů

V závislosti na vaší roli pro správu a na tom, jak podnikový správce nastavil zásady zobrazování nákladů, se vám můžou na webu Azure Portal zobrazovat různé ceny. Na zobrazení cen na webu Azure Portal má vliv nastavení těchto dvou zásad na webu Enterprise Portal:

- DA view charges (Správce oddělení může zobrazit náklady)
- AO view charges (Vlastník účtu může zobrazit náklady)

Informace o tom, jak tyto zásady nastavit, najdete v tématu [Správa přístupu k fakturačním informacím v Azure](manage-billing-access.md).

V následující tabulce je uvedený vztah mezi rolemi správce smlouvy Enterprise, zásadami zobrazování poplatků, rolí Azure na webu Azure Portal a cenami, které se zobrazují na webu Azure Portal. Podnikovému správci se vždycky zobrazují podrobnosti o využití na základě cen EA v organizaci. Správci oddělení a vlastníkovi účtu se ale zobrazují různé ceny v závislosti na nastavení zásad zobrazování poplatků a jejich role Azure. Role Správce oddělení uvedená v následující tabulce se týká role Správce oddělení i role Správce oddělení (jen pro čtení).

|Role správce smlouvy Enterprise|Zásady zobrazování nákladů pro roli|Role Azure|Zobrazení cen|
|---|---|---|---|
|Vlastník účtu nebo Správce oddělení|✔ Povoleno|Vlastník|Ceny EA v organizaci|
|Vlastník účtu nebo Správce oddělení|✘ Zakázáno|Vlastník|Maloobchodní ceny|
|Vlastník účtu nebo Správce oddělení|✔ Povoleno |Žádná|Žádné ceny|
|Vlastník účtu nebo Správce oddělení|✘ Zakázáno |Žádná|Žádné ceny|
|Žádná|Neuvedeno |Vlastník|Maloobchodní ceny|

Role správce smlouvy Enterprise a zásady zobrazování nákladů se nastavují na webu Enterprise Portal. Roli Azure je možné aktualizovat na webu Azure Portal. Další informace najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md).



## <a name="next-steps"></a>Další kroky

- [Správa přístupu k fakturačním údajům v Azure](manage-billing-access.md)
- [Přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Předdefinované role v Azure](../../role-based-access-control/built-in-roles.md)
