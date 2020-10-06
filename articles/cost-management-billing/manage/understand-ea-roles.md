---
title: Principy rolí správce smlouvy Enterprise v Azure
description: Přečtěte si informace o rolích správce smlouvy Enterprise v Azure. Můžete přiřadit pět různých rolí správce.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: 13b344d3f13993dc7b6acf7bfe9a0ccdea0c866b
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371350"
---
# <a name="managing-azure-enterprise-roles"></a>Správa rolí Azure Enterprise

Zákazníci Azure se smlouvou Enterprise (EA) můžou přiřazovat pět různých rolí správce, které jim v organizaci usnadní spravovat využívání a výdaje:

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

Podnikoví správci mají při správě registrace do Azure EA největší oprávnění. Úvodní správce Azure EA byl vytvořen při nastavení smlouvy EA. Nové správce můžete kdykoliv přidat nebo odebrat. Nové správce přidávají jenom stávající správci. Další informace o přidání dalších podnikových správců najdete v tématu [Vytvoření dalšího podnikového správce](ea-portal-get-started.md#create-another-enterprise-administrator). Další informace o rolích a úlohách fakturačního profilu najdete v tématu [Role a úlohy fakturačního profilu](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>Aktualizace stavu vlastníka účtu z Čeká na vyřízení na Aktivní

Při prvním přidání nových vlastníků účtů do registrace Azure EA se u nových vlastníků zobrazí stav _Čeká na vyřízení_. Jakmile noví vlastníci účtu dostanou úvodní aktivační e-mail, můžou se přihlásit a aktivovat si účet. Jakmile si účet aktivují, stav účtu se aktualizuje z _Čeká na vyřízení_ na _Aktivní_. Vlastník účtu si musí přečíst zprávu upozornění a pak vybrat **Pokračovat**. Při vytvoření komerčního účtu se může novým uživatelům zobrazit výzva k zadání jména a příjmení. Pokud se zobrazí, musí zadat požadované informace, aby mohli pokračovat, a pak se účet aktivuje.

## <a name="add-a-department-admin"></a>Přidání správce oddělení

Jakmile správce Azure EA vytvoří oddělení, může přidat správce oddělení a přidružit je k oddělením. Správce oddělení může vytvářet nové účty. Nové účty jsou potřeba pro vytvářená předplatná Azure EA.

Další informace o přidání správce oddělení najdete v tématu věnovaném [vytvoření správce oddělení Azure EA](ea-portal-get-started.md#add-a-department-administrator).

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

Role správce smlouvy Enterprise a zásady zobrazování nákladů se nastavují na webu Enterprise Portal. Roli Azure je možné aktualizovat na webu Azure Portal. Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).



## <a name="next-steps"></a>Další kroky

- [Správa přístupu k fakturačním údajům v Azure](manage-billing-access.md)
- [Přidání nebo odebrání přiřazení rolí Azure pomocí portálu Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Předdefinované role v Azure](../../role-based-access-control/built-in-roles.md)
