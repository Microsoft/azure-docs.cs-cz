---
title: Zobrazit členy s rolí správce v Azure Active Directory | Dokumentace Microsoftu
description: Teď můžete zobrazit a spravovat členy role správce Azure AD na portálu. Pro ty, kteří často Spravovat přiřazení rolí.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/02/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 39f7d69482845af48b1dce8c2e51f4acf77bf4fb
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448056"
---
# <a name="view-all-members-of-an-administrator-role-in-azure-active-directory"></a>Zobrazit všechny členy s rolí správce v Azure Active Directory

Teď můžete zobrazit a spravovat všechny členy rolí správce v portálu Azure Active Directory. Pokud často Spravovat přiřazení rolí, bude pravděpodobně dáváte přednost toto prostředí. A pokud jste už jste někdy přemýšleli "Co zrušíte pracovníci v těchto rolích ve skutečnosti udělat?", zobrazí se podrobný seznam oprávnění pro každou z role správce Azure AD.

Je snadno zjistit také vlastní oprávnění. Klikněte na tlačítko **vaši roli** Získejte rychlý přístup ke stránce uživatele seznam všech aktivních přiřazených rolí. Klikněte na tlačítko se třemi tečkami na pravé straně každého řádku otevřete podrobný popis role.

![seznam rolí na portálu Azure AD](./media/directory-manage-roles-portal/role-list.png)

Výběr celého řádku zobrazí seznam přiřazených členů. Můžete vybrat **spravovat v PIM** pro další možnosti správy. Správce privilegovaných rolí můžete změnit "Trvalé" (vždy aktivní v roli) přiřazení na "Oprávněné" (v roli jenom v případě, že se zvýšenými oprávněními). Pokud nemáte PIM, stále můžete vybrat **spravovat v PIM** si zaregistrovat zkušební verzi. Privileged Identity Management vyžaduje [licenční plán Azure AD Premium P2](./privileged-identity-management/subscription-requirements.md).

![seznam členů role správce](./media/directory-manage-roles-portal/member-list.png)

Pokud jste globální správce nebo správce privilegovaných rolí, můžete snadno přidat nebo odebrat členy, seznam můžete filtrovat nebo vyberte členy, aby mohli přejít na stránku uživatele, aby zobrazil jejich aktivní přiřazené role. 

## <a name="role-details-in-the-portal"></a>Podrobné informace o roli na portálu

Při zobrazení členů role, vyberte **popis** zobrazíte úplný seznam všech oprávnění udělená přiřazení role. Stránka obsahuje odkazy na relevantní dokumentaci, která vás provede spravovat role adresáře.

![ze seznamu oprávnění pro roli správce](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Další postup

* Nebojte se budou sdílet s námi na [fórum správní role Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích a přiřazení role správce, naleznete v tématu [přiřazení rolí správce](users-groups-roles/directory-assign-admin-roles.md).
* Výchozí oprávnění uživatelů, najdete v článku [porovnání výchozí uživatelská oprávnění hostů a člen](./fundamentals/users-default-permissions.md).
