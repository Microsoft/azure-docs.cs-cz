---
title: Zobrazení a přiřazení oprávnění role správce – Azure AD | Microsoft Docs
description: Na portálu teď můžete zobrazit a spravovat členy role správce Azure AD. Pro ty, kteří často spravují přiřazení rolí.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9330c6d34dcf5a799d65ebba176565ff031cb956
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376778"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Zobrazení a přiřazování rolí správce ve službě Azure Active Directory

V portálu Azure Active Directory nyní můžete zobrazit a spravovat všechny členy rolí správce. Pokud často spravujete přiřazení rolí, budete pravděpodobně chtít vyzkoušet toto prostředí. A pokud jste někdy přemýšleli "Jaké aškrtněte tyto role skutečně dělají?", můžete zobrazit podrobný seznam oprávnění pro každou roli správce Azure AD.

## <a name="view-all-roles"></a>Zobrazit všechny role

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte **Azure Active Directory**.

1. Výběrem **rolí a správců** zobrazíte seznam všech dostupných rolí.

1. Vyberte tři tečky na pravé straně každého řádku, abyste viděli oprávnění pro danou roli. Vyberte roli pro zobrazení uživatelů přiřazených k této roli. Pokud se vám zobrazí něco jiného než na následujícím obrázku, přečtěte si poznámku v tématu [zobrazení přiřazení privilegovaných rolí](#view-assignments-for-privileged-roles) a ověřte, jestli jste v PRIVILEGED Identity Management (PIM).

    ![seznam rolí na portálu Azure AD](./media/manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Zobrazení mých rolí

Je snadné zobrazit také vlastní oprávnění. Výběrem **role** na stránce **role a správci** zobrazíte aktuálně přiřazené role.

## <a name="view-assignments-for-privileged-roles"></a>Zobrazení přiřazení privilegovaných rolí

Pro další možnosti správy můžete vybrat **spravovat v PIM** . Správci privilegovaných rolí můžou změnit přiřazení "trvalé" (vždy aktivní v roli) přiřazení na "opravňující" (v roli jenom při zvýšeném oprávnění). Pokud nemáte Privileged Identity Management, můžete přesto vybrat **spravovat v PIM** , abyste se mohli zaregistrovat ke zkušební verzi. Privileged Identity Management vyžaduje [plán licencí Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![seznam členů role správce](./media/manage-roles-portal/member-list.png)

Pokud jste globální správce nebo správce privilegovaných rolí, můžete snadno přidávat nebo odebírat členy, filtrovat seznam nebo vybrat člena a zobrazit jejich aktivní přiřazené role.

> [!Note]
> Pokud máte licenci Azure AD Premium P2 a už používáte Privileged Identity Management, provedou se všechny úlohy správy rolí ve správě identit oprávnění, nikoli v Azure AD.
>
> ![Role Azure AD spravované v PIM pro uživatele, kteří už používají PIM a mají licenci Premium P2](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>Zobrazit oprávnění role uživatele

Při prohlížení členů role vyberte možnost **Popis** a zobrazí se úplný seznam oprávnění udělených přiřazením role. Stránka obsahuje odkazy na příslušnou dokumentaci, která vám pomůže s tím, jak spravovat role adresáře.

![Snímek obrazovky zobrazující stránku Globální správce – popis](./media/manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>Stažení přiřazení rolí

Pokud chcete stáhnout všechna přiřazení pro určitou roli, na stránce **role a správci** vyberte roli a pak vyberte **Stáhnout přiřazení rolí**. Stáhne se soubor CSV se seznamem přiřazení všech oborů pro danou roli.

![Stáhnout všechna přiřazení pro roli](./media/manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>Přiřazení role

1. Přihlaste se k [Azure Portal](https://portal.azure.com) s oprávněními správce globálního správce nebo Privileged role a vyberte **Azure Active Directory**.

1. Výběrem **rolí a správců** zobrazíte seznam všech dostupných rolí.

1. Výběrem role zobrazíte její přiřazení.

    ![Snímek obrazovky zobrazující stránku správce uživatele – přiřazení se zvolenou akcí spravovat v PIM.](./media/manage-roles-portal/member-list.png)

1. Vyberte **Přidat přiřazení** a vyberte role, které chcete přiřadit. Pro další možnosti správy můžete vybrat **spravovat v PIM** . Pokud se vám zobrazí něco jiného než na následujícím obrázku, přečtěte si poznámku v tématu [zobrazení přiřazení privilegovaných rolí](#view-assignments-for-privileged-roles) a ověřte, jestli jste v PIM.

    ![seznam oprávnění pro roli správce](./media/manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>Další kroky

* Můžete s námi sdílet na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích a přiřazení rolí správců najdete v tématu [přiřazení rolí správce](permissions-reference.md).
* Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md).
