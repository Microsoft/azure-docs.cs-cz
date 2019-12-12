---
title: Principy rolí správce smlouvy Enterprise v Azure | Microsoft Docs
description: Přečtěte si informace o rolích správce smlouvy Enterprise v Azure.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2019
ms.author: banders
ms.openlocfilehash: 62a16a6a96fbfdfbb4ed2d70b74470e29efaa844
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849849"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Principy rolí pro správu smlouvy Azure Enterprise v Azure

Zákazníci Azure se smlouvou Enterprise (EA) můžou přiřazovat pět různých rolí správce, které jim v organizaci usnadní spravovat využívání a výdaje:

- Podnikový správce
- Podnikový správce (jen pro čtení)
- Správce oddělení
- Správce oddělení (jen pro čtení)
- Vlastník účtu
 
Tyto role jsou specifické pro správu smluv Azure Enterprise a doplňují předdefinované role, pomocí kterých Azure řídí přístup k prostředkům. Další informace najdete v tématu [Předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md).

Následující části popisují omezení a možnosti jednotlivých rolí.

## <a name="user-limit-for-admin-roles"></a>Limit počtu uživatelů pro role správce

|Role| Limit počtu uživatelů|
|---|---|
|Podnikový správce|Unlimited|
|Podnikový správce (jen pro čtení)|Unlimited|
|Správce oddělení|Unlimited|
|Správce oddělení (jen pro čtení)|Unlimited|
|Vlastník účtu|1 na účet<sup>1</sup>|

<sup>1</sup> Každý účet vyžaduje jedinečný účet Microsoft nebo pracovní či školní účet.

## <a name="organization-structure-and-permissions-by-role"></a>Organizační struktura a oprávnění podle role

|Úlohy| Podnikový správce|Podnikový správce (jen pro čtení)|Správce oddělení|Správce oddělení (jen pro čtení)|Vlastník účtu|
|---|---|---|---|---|---|
|Zobrazení podnikových správců|✔|✔|✘|✘|✘|
|Přidání nebo odebrání podnikových správců|✔|✘|✘|✘|✘|
|Zobrazení kontaktů pro oznámení<sup>2</sup> |✔|✔|✘|✘|✘|
|Přidání nebo odebrání kontaktů pro oznámení<sup>2</sup> |✔|✘|✘|✘|✘|
|Vytvoření a správa oddělení |✔|✘|✘|✘|✘|
|Zobrazení správců oddělení|✔|✔|✔|✔|✘|
|Přidání nebo odebrání správců oddělení|✔|✘|✔|✘|✘|
|Zobrazení účtů v registraci |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Přidání účtů do registrace a změna vlastníka účtu|✔|✘|✔<sup>3</sup>|✘|✘|
|Vytvoření a správa předplatných a oprávnění předplatných|✘|✘|✘|✘|✔|

- <sup>2</sup> Kontaktům pro oznámení se odesílá e-mailová komunikace týkající se smlouvy Azure Enterprise.
- <sup>3</sup> Úloha je omezená na účty ve vašem oddělení.


## <a name="usage-and-costs-access-by-role"></a>Přístup k využití a nákladům podle role

|Úlohy| Podnikový správce|Podnikový správce (jen pro čtení)|Správce oddělení|Správce oddělení (jen pro čtení) |Vlastník účtu|
|---|---|---|---|---|---|
|Zobrazení zůstatku kreditu včetně peněžního závazku|✔|✔|✘|✘|✘|
|Zobrazení kvót útraty oddělení|✔|✔|✘|✘|✘|
|Nastavení kvót útraty oddělení|✔|✘|✘|✘|✘|
|Zobrazení ceníku EA v organizaci|✔|✔|✘|✘|✘|
|Zobrazení podrobností o využití a nákladech|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Správa prostředků na webu Azure Portal|✘|✘|✘|✘|✔|

- <sup>4</sup> Podnikový správce musí na webu Enterprise Portal povolit zásadu **DA view charges** (Správce oddělení může zobrazit náklady). Správce oddělení pak může zobrazit podrobnosti o nákladech daného oddělení.
- <sup>5</sup> Podnikový správce musí na webu Enterprise Portal povolit zásadu **AO view charges** (Vlastník účtu může zobrazit náklady). Vlastník účtu pak může zobrazit podrobnosti o nákladech daného účtu.


## <a name="pricing-in-azure-portal"></a>Ceny na webu Azure Portal

V závislosti na vaší roli pro správu a na tom, jak podnikový správce nastavil zásady zobrazování nákladů, se vám můžou na webu Azure Portal zobrazovat různé ceny. Na zobrazení cen na webu Azure Portal má vliv nastavení těchto dvou zásad na webu Enterprise Portal:

- DA view charges (Správce oddělení může zobrazit náklady)
- AO view charges (Vlastník účtu může zobrazit náklady)

Informace o tom, jak tyto zásady nastavit, najdete v tématu [Správa přístupu k fakturačním informacím v Azure](billing-manage-access.md).

V následující tabulce je uveden vztah mezi rolemi správce smlouvy Enterprise, zásadami zobrazování nákladů, rolí řízení přístupu na základě role (RBAC) na webu Azure Portal a cenami, které se zobrazují na webu Azure Portal. Podnikovému správci se vždycky zobrazují podrobnosti o využití na základě cen EA v organizaci. Správci oddělení a vlastníkovi účtu se ale zobrazují různé ceny podle nastavení zásad zobrazování nákladů a jejich role RBAC. Role Správce oddělení uvedená v následující tabulce se týká role Správce oddělení i role Správce oddělení (jen pro čtení).

|Role správce smlouvy Enterprise|Zásady zobrazování nákladů pro roli|Role RBAC|Zobrazení cen|
|---|---|---|---|
|Vlastník účtu nebo Správce oddělení|✔ Povoleno|Vlastník|Ceny EA v organizaci|
|Vlastník účtu nebo Správce oddělení|✘ Zakázáno|Vlastník|Maloobchodní ceny|
|Vlastník účtu nebo Správce oddělení|✔ Povoleno |Žádná|Žádné ceny|
|Vlastník účtu nebo Správce oddělení|✘ Zakázáno |Žádná|Žádné ceny|
|Žádný|Neuvedeno |Vlastník|Maloobchodní ceny|

Role správce smlouvy Enterprise a zásady zobrazování nákladů se nastavují na webu Enterprise Portal. Role RBAC se dá aktualizovat na webu Azure Portal. Další informace najdete v tématu [Správa přístupu pomocí RBAC a webu Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky

- [Správa přístupu k fakturačním údajům v Azure](billing-manage-access.md)
- [Správa přístupu pomocí RBAC a portálu Azure Portal](../role-based-access-control/role-assignments-portal.md)
- [Předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md)
