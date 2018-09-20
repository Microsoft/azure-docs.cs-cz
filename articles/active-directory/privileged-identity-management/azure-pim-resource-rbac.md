---
title: Zobrazení, který má role prostředků Azure v PIM | Dokumentace Microsoftu
description: Zobrazení, který má role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: ce7c96d92938c4e3b4cc0b53271df48350083754
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465227"
---
# <a name="view-who-has-azure-resource-roles-in-pim"></a>Zobrazení, který má role prostředků Azure v PIM

S Azure Active Directory Privileged Identity Management (PIM), můžete spravovat, řídit a monitorovat přístup k prostředkům Azure v rámci vaší organizace. To zahrnuje předplatná, skupiny prostředků a dokonce i virtuální počítače. Prostředek na portálu Azure portal, který využívá funkce řízení přístupu na základě rolí Azure můžete využít výhod zabezpečení a životního cyklu možnosti správy Azure AD PIM. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>PIM pro prostředky Azure pomáhá správcům prostředků

- Zobrazit, kteří uživatelé a skupiny jsou přiřazené role prostředků Azure, které spravujete
- Povolit přístup "just in time" ke správě prostředků, jako jsou předplatná, skupiny prostředků a další na vyžádání
- Vypršení platnosti přístupu k prostředkům přiřazené uživatele nebo skupiny automaticky s novými nastaveními časově omezenou přiřazení
- Přiřazení přístupu k prostředkům dočasný pro rychlé úkoly nebo plány na volání
- Vynutit Vícefaktorové ověřování pro přístup k prostředkům v libovolné vestavěné i vlastní role 
- Získání sestav o aktivita prostředku korelační přístup k prostředku během aktivní uživatelské relace
- Dostávat upozornění, když nové uživatele nebo skupiny jsou přiřazeny přístup k prostředkům, a při aktivaci oprávněnými přiřazeními

## <a name="view-activation-and-azure-resource-activity"></a>Zobrazit aktivace a aktivita prostředku Azure

V případě, že budete muset které akce trvala konkrétního uživatele na různé prostředky naleznete v tématu, můžete zkontrolovat prostředků Azure aktivity spojené s danou aktivační období (pro oprávněné uživatele). Začněte tím, že výběr uživatele ze zobrazení členů nebo ze seznamu členů v konkrétní roli. Výsledek zobrazí grafické zobrazení akcí uživatele pro prostředky Azure podle data, a poslední aktivace rolí za stejné období čas.

![](media/azure-pim-resource-rbac/user-details.png)

Výběr aktivaci konkrétní role se zobrazí podrobnosti o aktivaci role a odpovídající aktivity prostředků Azure, ke které došlo během tento uživatel byl aktivní.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="review-who-has-access-in-a-subscription"></a>Přezkoumejte, kdo má přístup v rámci předplatného

O kontrolu přiřazení role v rámci vašeho předplatného, vyberte kartu členy z levého navigačního panelu, nebo vybrat role a zvolte konkrétní role ke kontrole členy. 

Kontrola vyberte z panelu akcí vyberte možnost přidat k vytvoření nové kontroly a zobrazovat existující kontrolu přístupu.

![](media/azure-pim-resource-rbac/owner.png)

[Další informace o kontrolách přístupu](pim-how-to-perform-security-review.md)

>[!NOTE]
Kontroly jsou podporovány pouze pro typy prostředků předplatného v tuto chvíli.

## <a name="next-steps"></a>Další postup

- [Přiřazení role prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
- [Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM](pim-resource-roles-approval-workflow.md)
- [Předdefinované role v Azure](../../role-based-access-control/built-in-roles.md)
