---
title: Zobrazte a přiřaďte oprávnění role v Azure Active Directory správce | Dokumentace Microsoftu
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
ms.date: 09/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: cb2e5286eb8e910b555e221242a735f00dff4778
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182820"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Zobrazení a přiřazení rolí správce ve službě Azure Active Directory

Teď můžete zobrazit a spravovat všechny členy rolí správce v portálu Azure Active Directory. Pokud často Spravovat přiřazení rolí, bude pravděpodobně dáváte přednost toto prostředí. A pokud jste už jste někdy přemýšleli "Co zrušíte pracovníci v těchto rolích ve skutečnosti udělat?", zobrazí se podrobný seznam oprávnění pro každou z role správce Azure AD.

Je snadno zjistit také vlastní oprávnění. Klikněte na tlačítko **vaše Role** Získejte rychlý přístup ke stránce uživatele seznam všech aktivních přiřazených rolí. Klikněte na tlačítko se třemi tečkami na pravé straně každého řádku otevřete podrobný popis role.

![seznam rolí na portálu Azure AD](./media/directory-manage-roles-portal/role-list.png)

Vyberte řádek pro roli, kterou chcete zobrazit uživatele, které jsou přiřazeny k roli. Můžete vybrat **spravovat v PIM** pro další možnosti správy. Správce privilegovaných rolí můžete změnit "Trvalé" (vždy aktivní v roli) přiřazení na "Oprávněné" (v roli jenom v případě, že se zvýšenými oprávněními). Pokud nemáte PIM, stále můžete vybrat **spravovat v PIM** si zaregistrovat zkušební verzi. Privileged Identity Management vyžaduje [licenční plán Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![seznam členů role správce](./media/directory-manage-roles-portal/member-list.png)

Pokud jste globální správce nebo správce privilegovaných rolí, můžete snadno přidat nebo odebrat členy, seznam můžete filtrovat nebo vyberte člena zobrazíte jejich aktivní přiřazené role.

## <a name="view-role-permissions"></a>Zobrazení oprávnění role

Při zobrazení členů role, vyberte **popis** zobrazíte úplný seznam všech oprávnění udělená přiřazení role. Stránka obsahuje odkazy na relevantní dokumentaci, která vás provede spravovat role adresáře.

![ze seznamu oprávnění pro roli správce](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Další postup

* Nebojte se budou sdílet s námi na [fórum správní role Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích a přiřazení role správce, naleznete v tématu [přiřazení rolí správce](directory-assign-admin-roles.md).
* Výchozí oprávnění uživatelů, najdete v článku [porovnání výchozí uživatelská oprávnění hostů a člen](../fundamentals/users-default-permissions.md).
