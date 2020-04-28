---
title: Zobrazení a přiřazení oprávnění role správce – Azure AD | Microsoft Docs
description: Na portálu teď můžete zobrazit a spravovat členy role správce Azure AD. Pro ty, kteří často spravují přiřazení rolí.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900910"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Zobrazení a přiřazení rolí správce v Azure Active Directory

V portálu Azure Active Directory nyní můžete zobrazit a spravovat všechny členy rolí správce. Pokud často spravujete přiřazení rolí, budete pravděpodobně chtít vyzkoušet toto prostředí. A pokud jste někdy přemýšleli "Jaké aškrtněte tyto role skutečně dělají?", můžete zobrazit podrobný seznam oprávnění pro každou roli správce Azure AD.

## <a name="view-all-roles"></a>Zobrazit všechny role

Vyhledejte a vyberte **Azure Active Directory**. Výběrem **rolí a správců** zobrazíte seznam všech dostupných rolí.

Kliknutím na tři tečky na pravé straně každého řádku otevřete podrobný popis role.

![seznam rolí na portálu Azure AD](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Zobrazení mých rolí

Je snadné zobrazit také vlastní oprávnění. Výběrem **role** na stránce **role a správci** zobrazíte aktuálně přiřazené role.

## <a name="view-assignments-for-a-role"></a>Zobrazení přiřazení pro roli

Kliknutím na roli zobrazíte uživatele přiřazené k této roli. Pro další možnosti správy můžete vybrat **spravovat v PIM** . Správci privilegovaných rolí můžou změnit přiřazení "trvalé" (vždy aktivní v roli) přiřazení na "opravňující" (v roli jenom při zvýšeném oprávnění). Pokud nemáte PIM, můžete si i nadále vybrat **spravovat v PIM** , abyste se mohli zaregistrovat ke zkušební verzi. Privileged Identity Management vyžaduje [plán licencí Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![seznam členů role správce](./media/directory-manage-roles-portal/member-list.png)

Pokud jste globální správce nebo správce privilegovaných rolí, můžete snadno přidávat nebo odebírat členy, filtrovat seznam nebo vybrat člena a zobrazit jejich aktivní přiřazené role.

## <a name="view-a-users-role-permissions"></a>Zobrazit oprávnění role uživatele

Při prohlížení členů role vyberte možnost **Popis** a zobrazí se úplný seznam oprávnění udělených přiřazením role. Stránka obsahuje odkazy na příslušnou dokumentaci, která vám pomůže s tím, jak spravovat role adresáře.

![seznam oprávnění pro roli správce](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Další kroky

* Můžete s námi sdílet na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích a přiřazení rolí správců najdete v tématu [přiřazení rolí správce](directory-assign-admin-roles.md).
* Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md).
