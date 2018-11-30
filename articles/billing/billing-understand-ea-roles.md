---
title: Vysvětlení rolí správce pro Enterprise v Azure | Dokumentace Microsoftu
description: Další informace o rolích správce rozlehlé sítě v Azure.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: cwatson
ms.openlocfilehash: 77d061166d599fe7556be7d0237100154890fe19
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500286"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Principy role pro správu smlouvy Azure Enterprise v Azure

Ke správě používání ve vaší organizaci a výdaje, Azure zákazníky se smlouvou Enterprise (EA) můžete přiřadit pět různých pro správu rolí:

- Podnikový správce
- Správce rozlehlé sítě (jen pro čtení)
- Správce oddělení
- Oddělení správce (jen pro čtení)
- Vlastník účtu
 
Tyto role jsou specifické pro správu smlouvy Azure Enterprise a kromě předdefinované role, které Azure nabízí pro řízení přístupu k prostředkům. Další informace najdete v tématu [předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md).

Následující části popisují, omezení a možností jednotlivých rolí.

## <a name="user-limit-for-admin-roles"></a>Limit počtu uživatelů s rolí správce

|Role| Limit počtu uživatelů.|
|---|---|
|Podnikový správce|Unlimited|
|Správce rozlehlé sítě (jen pro čtení)|Unlimited|
|Správce oddělení|Unlimited|
|Oddělení správce (jen pro čtení)|Unlimited|
|Vlastník účtu|1 za účet<sup>1</sup>|

<sup>1</sup> každý účet vyžaduje jedinečný účet Microsoft nebo pracovní nebo školní účet.

## <a name="organization-structure-and-permissions-by-role"></a>Struktura organizace a oprávnění rolí

|Úlohy| Podnikový správce|Správce rozlehlé sítě (jen pro čtení)|Správce oddělení|Oddělení správce (jen pro čtení)|Vlastník účtu|
|---|---|---|---|---|---|
|Zobrazit správce rozlehlé sítě|✔|✔|✘|✘|✘|
|Přidejte nebo odeberte správce organizace|✔|✘|✘|✘|✘|
|Zobrazit kontakty oznámení<sup>2</sup> |✔|✔|✘|✘|✘|
|Přidání nebo odebrání kontaktů oznámení<sup>2</sup> |✔|✘|✘|✘|✘|
|Vytvoření a správa oddělení |✔|✘|✘|✘|✘|
|Správci oddělení zobrazení|✔|✔|✔|✔|✘|
|Přidat nebo odebrat správce oddělení|✔|✔|✔|✘|✘|
|Zobrazení účtů v registraci |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Přidat účty k registraci a změnit vlastníka účtu|✔|✘|✔<sup>3</sup>|✘|✘|
|Vytvoření a správa předplatných a předplatném oprávnění|✘|✘|✘|✘|✔|

- <sup>2</sup> kontakty oznámení se odesílají e-mailové komunikace o smlouvy Azure Enterprise.
- <sup>3</sup> úkolu je omezená na účty v oddělení.


## <a name="usage-and-costs-access-by-role"></a>Využití a nákladů přístupu podle rolí

|Úlohy| Podnikový správce|Správce rozlehlé sítě (jen pro čtení)|Správce oddělení|Oddělení správce (jen pro čtení) |Vlastník účtu|
|---|---|---|---|---|---|
|Zůstatek kreditu zobrazení včetně peněžní závazek|✔|✔|✘|✘|✘|
|Zobrazení oddělení kvóty útraty|✔|✔|✘|✘|✘|
|Nastavit kvóty útraty oddělení|✔|✘|✘|✘|✘|
|Zobrazit organizaci EA ceníku|✔|✔|✘|✘|✘|
|Zobrazit podrobnosti o využití a nákladů|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Správa prostředků na webu Azure portal|✘|✘|✘|✘|✔|

- <sup>4</sup> vyžaduje, aby povolili správce rozlehlé sítě **DA zobrazit náklady** zásady na portálu Enterprise. Správce oddělení pak uvidí nákladů pro oddělení.
- <sup>5</sup> vyžaduje, aby povolili správce rozlehlé sítě **AO zobrazit náklady** zásady na portálu Enterprise. Vlastník účtu můžete se podívat, náklady na Podrobnosti účtu.


## <a name="pricing-in-azure-portal"></a>Ceny na webu Azure portal

Může se zobrazit různé ceny na webu Azure Portal v závislosti na vaší roli pro správu a nastavení správce rozlehlé sítě zobrazit zásady poplatky. Jsou dvě zásady na portálu Enterprise, které mají vliv na ceny, které se zobrazí na webu Azure Portal:

- DA zobrazit náklady
- Žádosti o zobrazit náklady

Zjistěte, jak nastavit tyto zásady, najdete v článku [spravovat přístup k fakturačních údajů pro Azure](billing-manage-access.md).

Následující tabulka znázorňuje vztah mezi role správce smlouvy Enterprise, zobrazit zásadu poplatky, role řízení přístupu na základě role v na webu Azure portal a ceny, který se zobrazí na webu Azure Portal. Správce rozlehlé sítě se vždy zobrazí podrobnosti o použití založené na cenové EA organizace. Oddělení správce a vlastník účtu, ale uvidí různých cenových zobrazení na základě zásad poplatek za zobrazení a jejich role RBAC. Role Správce oddělení, které jsou uvedené v následující tabulce odkazuje na oddělení správce a role Správce oddělení (jen pro čtení).

|Role správce smlouvy Enterprise|Zobrazit zásadu poplatky pro roli|RBAC role|Zobrazit ceny|
|---|---|---|---|
|Vlastník účtu nebo správci oddělení|✔ Povoleno|Vlastník|Ceny za EA organizace.|
|Vlastník účtu nebo správci oddělení|✘ Zakázáno|Vlastník|Maloobchodní cenu|
|Vlastník účtu nebo správci oddělení|✔ Povoleno |žádný|Žádné informace o cenách|
|Vlastník účtu nebo správci oddělení|✘ Zakázáno |žádný|Žádné informace o cenách|
|Žádný|Neuvedeno |Vlastník|Maloobchodní cenu|

Nastavte roli správce rozlehlé sítě a zobrazení účtuje zásady na portálu Enterprise. RBAC role je možné aktualizovat na webu Azure Portal. Další informace najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další postup

- [Správa přístupu k fakturačních údajů pro Azure](billing-manage-access.md)
- [Správa přístupu pomocí RBAC a portálu Azure Portal](../role-based-access-control/role-assignments-portal.md)
- [Předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md)
