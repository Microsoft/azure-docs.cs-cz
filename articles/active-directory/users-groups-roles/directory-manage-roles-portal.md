---
title: Zobrazení a přiřazení oprávnění role správce – Azure AD | Dokumenty společnosti Microsoft
description: Teď můžete zobrazit a spravovat členy role správce Azure AD na portálu. Pro ty, kteří často spravují přiřazení rolí.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2815b496545ca5f920c00df7b2b5b7efe15c12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900910"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Zobrazení a přiřazení rolí správce ve službě Azure Active Directory

Teď můžete zobrazit a spravovat všechny členy rolí správce na portálu Azure Active Directory. Pokud často spravujete přiřazení rolí, budete pravděpodobně upřednostňovat toto prostředí. A pokud jste někdy přemýšleli" Co sakra tyto role opravdu dělat?", můžete zobrazit podrobný seznam oprávnění pro každou z rolí správce Azure AD.

## <a name="view-all-roles"></a>Zobrazit všechny role

Vyhledejte a vyberte **Azure Active Directory**. Výběrem **rolí a správců** zobrazíte seznam všech dostupných rolí.

Kliknutím na tři tečky vpravo od každého řádku otevřete podrobný popis role.

![seznam rolí na portálu Azure AD](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Zobrazit moje role

Je také snadné zobrazit vlastní oprávnění. Vyberte **svou roli** na stránce **Role a správci,** abyste viděli role, které jsou vám aktuálně přiřazeny.

## <a name="view-assignments-for-a-role"></a>Zobrazení přiřazení pro roli

Kliknutím na roli zobrazíte uživatele přiřazené k roli. Můžete vybrat **Spravovat v PIM** pro další možnosti správy. Správci privilegovaných rolí mohou změnit přiřazení "Trvalé" (vždy aktivní v roli) na "Způsobilé" (v roli pouze při zvýšení oprávnění). Pokud nemáte PIM, můžete stále vybrat **Spravovat v PIM** se zaregistrovat ke zkušební verzi. Správa privilegovaných identit vyžaduje [licenční plán Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![seznam členů role správce](./media/directory-manage-roles-portal/member-list.png)

Pokud jste globální správce nebo správce privilegovaných rolí, můžete snadno přidávat nebo odebírat členy, filtrovat seznam nebo vybrat člena, aby se zobcely jeho aktivní přiřazené role.

## <a name="view-a-users-role-permissions"></a>Zobrazení oprávnění role uživatele

Při prohlížení členů role vyberte **Popis,** chcete-li zobrazit úplný seznam oprávnění udělených přiřazením role. Stránka obsahuje odkazy na příslušnou dokumentaci, které vám pomohou se správou rolí adresářů.

![seznam oprávnění pro roli správce](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Další kroky

* Neváhejte se s námi podělit na [fóru rolí azure ad pro správu](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích a přiřazení rolí správce najdete v tématu [Přiřazení rolí správce](directory-assign-admin-roles.md).
* Výchozí uživatelská oprávnění naleznete v [porovnání výchozích oprávnění hosta a člena uživatele](../fundamentals/users-default-permissions.md).
