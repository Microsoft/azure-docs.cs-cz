---
title: Principy rolí správců pro Enterprise v Azure | Microsoft Docs
description: Přečtěte si informace o rolích podnikového správce v Azure.
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
ms.author: banders
ms.openlocfilehash: 39526defb8f41ddacb0a26d7ad852f820ca6ea77
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034529"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Principy rolí pro správu Azure smlouva Enterprise v Azure

Zákazníkům Azure s smlouva Enterprise (EA) se může při správě využívání a útraty vaší organizace přiřadit pět různých administrativních rolí:

- Podnikový správce
- Podnikový správce (jen pro čtení)
- Správce oddělení
- Správce oddělení (jen pro čtení)
- Vlastník účtu
 
Tyto role jsou specifické pro správu smluv Azure Enterprise a navíc k předdefinovaným rolím Azure musí řídit přístup k prostředkům. Další informace najdete v tématu [předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md).

V následujících částech jsou popsána omezení a možnosti jednotlivých rolí.

## <a name="user-limit-for-admin-roles"></a>Omezení počtu uživatelů pro role správců

|Role| Omezení počtu uživatelů|
|---|---|
|Podnikový správce|Unlimited|
|Podnikový správce (jen pro čtení)|Unlimited|
|Správce oddělení|Unlimited|
|Správce oddělení (jen pro čtení)|Unlimited|
|Vlastník účtu|1 na účet<sup>1</sup>|

<sup>1</sup> každý účet vyžaduje jedinečný účet Microsoft nebo pracovní nebo školní účet.

## <a name="organization-structure-and-permissions-by-role"></a>Organizační struktura a oprávnění podle role

|Úlohy| Podnikový správce|Podnikový správce (jen pro čtení)|Správce oddělení|Správce oddělení (jen pro čtení)|Vlastník účtu|
|---|---|---|---|---|---|
|Zobrazit podnikové správce|✔|✔|✘|✘|✘|
|Přidání nebo odebrání podnikových správců|✔|✘|✘|✘|✘|
|Zobrazit kontakty oznámení<sup>2</sup> |✔|✔|✘|✘|✘|
|Přidat nebo odebrat kontakty oznámení<sup>2</sup> |✔|✘|✘|✘|✘|
|Vytváření a Správa oddělení |✔|✘|✘|✘|✘|
|Zobrazit Správce oddělení|✔|✔|✔|✔|✘|
|Přidat nebo odebrat správce oddělení|✔|✘|✔|✘|✘|
|Zobrazit účty v registraci |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Přidání účtů k registraci a změna vlastníka účtu|✔|✘|✔<sup>3</sup>|✘|✘|
|Vytváření a Správa předplatných a oprávnění k předplatnému|✘|✘|✘|✘|✔|

- <sup>2</sup> kontaktní oznámení odesílají e-mailovou komunikaci o smlouva Enterprise Azure.
- <sup>3</sup> úloha je omezená na účty ve vašem oddělení.


## <a name="usage-and-costs-access-by-role"></a>Přístup k využití a náklady podle role

|Úlohy| Podnikový správce|Podnikový správce (jen pro čtení)|Správce oddělení|Správce oddělení (jen pro čtení) |Vlastník účtu|
|---|---|---|---|---|---|
|Zobrazení zůstatku kreditu včetně peněžního závazku|✔|✔|✘|✘|✘|
|Zobrazit kvóty útraty oddělení|✔|✔|✘|✘|✘|
|Nastavení kvót útraty pro oddělení|✔|✘|✘|✘|✘|
|Zobrazit Ceník ceny EA organizace|✔|✔|✘|✘|✘|
|Zobrazit podrobnosti o využití a nákladech|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Správa prostředků v Azure Portal|✘|✘|✘|✘|✔|

- <sup>4</sup> vyžaduje, aby podnikový správce na portálu Enterprise povolil zásadu **zobrazení nákladů na zobrazení da** . Správce oddělení pak může zobrazit podrobnosti o nákladech oddělení.
- <sup>5</sup> vyžaduje, aby podnikový správce na portálu Enterprise povolil možnost **Zobrazit poplatky za zobrazení Ao** . Vlastník účtu pak může zobrazit podrobnosti o nákladech účtu.


## <a name="pricing-in-azure-portal"></a>Ceny v Azure Portal

V závislosti na vaší roli správce a způsobu, jakým jsou zásady zobrazení poplatků nastavené podnikovým správcem, se vám můžou zobrazovat různé ceny Azure Portal. Tyto dvě zásady na portálu Enterprise mají vliv na ceny, které vidíte v Azure Portal:

- Poplatky za zobrazení DA
- Poplatky za zobrazení AO

Informace o tom, jak tyto zásady nastavit, najdete v tématu [Správa přístupu k fakturačním informacím pro Azure](billing-manage-access.md).

V následující tabulce je uveden vztah mezi rolemi správce smlouva Enterprise, zásadou zobrazení poplatků, rolí řízení přístupu na základě role (RBAC) v Azure Portal a cenami, které vidíte v Azure Portal. Správce podnikové sítě vždycky uvidí podrobnosti o využití na základě cen EA v organizaci. Správce oddělení a vlastník účtu ale uvidí různé cenové pohledy na základě zásad pro poplatky za zobrazení a jejich role RBAC. Role správce oddělení uvedená v následující tabulce odkazuje na role správce oddělení a správce oddělení (jen pro čtení).

|Role správce smlouva Enterprise|Zobrazit zásady poplatků za roli|Role RBAC|Zobrazení cen|
|---|---|---|---|
|Vlastník účtu nebo správce oddělení|✔ Povoleno|Owner|Ceny smlouvy EA pro organizaci|
|Vlastník účtu nebo správce oddělení|✘ Zakázané|Owner|Maloobchodní ceny|
|Vlastník účtu nebo správce oddělení|✔ Povoleno |žádný|Žádné ceny|
|Vlastník účtu nebo správce oddělení|✘ Zakázané |žádný|Žádné ceny|
|Žádné|Nelze použít |Owner|Maloobchodní ceny|

Role podnikového správce můžete nastavit a zobrazit zásady poplatků na portálu Enterprise. Role RBAC se dá aktualizovat v Azure Portal. Další informace najdete v tématu [Správa přístupu pomocí RBAC a Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další postup

- [Správa přístupu k fakturačních údajů pro Azure](billing-manage-access.md)
- [Správa přístupu pomocí RBAC a portálu Azure Portal](../role-based-access-control/role-assignments-portal.md)
- [Předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md)
