---
title: Přiřadit role adresáře uživatele, kteří používají Azure AD PIM | Dokumentace Microsoftu
description: Zjistěte, jak přiřadit role adresáře uživatele, kteří používají Azure Active Directory Privileged Identity Management a webu Azure portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 07/23/2018
ms.author: rolyon
ms.openlocfilehash: 1834addb4e51030afda43a2d7acad5d7ffc1889a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226692"
---
# <a name="assign-directory-roles-to-users-using-azure-ad-pim"></a>Přiřadit role adresáře uživatele, kteří používají Azure AD PIM

S Azure Active Directory (Azure AD), globální správce může nastavit **trvalé** přiřazení rolí adresáře. Tato přiřazení rolí můžete vytvořit pomocí [webu Azure portal](../users-groups-roles/directory-assign-admin-roles.md) nebo pomocí [příkazy prostředí PowerShell](/powershell/module/azuread#directory_roles).

Služba Azure AD Privileged Identity Management (PIM) také umožňuje správcům privilegovaných rolí adresáře trvalé přiřazení rolí. Kromě toho správci privilegovaných rolí, můžete nastavit uživatele **oprávněné** pro role adresáře. Správce může aktivovat roli, když ho potřebují, a potom jejich oprávnění vyprší po jejich dokončení. Informace o rolích, které můžete spravovat pomocí PIM, naleznete v tématu [role adresáře, které můžete spravovat pomocí Azure AD PIM](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Nastavit jako oprávněné pro roli uživatele

Následujícím postupem nastavit uživatele jako oprávněné pro role adresáře Azure AD.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako uživatel, který je členem skupiny [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) role.

    Informace o tom, jak udělit jiný uživatel přístup ke správě PIM, naleznete v tématu [způsob poskytnutí přístupu k PIM](pim-how-to-give-access-to-pim.md).

1. Otevřít **Azure AD Privileged Identity Management**.

    Pokud jste nepovolili PIM na portálu Azure portal ještě, přejděte na [Začínáme s Azure AD PIM](pim-getting-started.md).

1. Klikněte na tlačítko **role adresáře Azure AD**.

1. Klikněte na tlačítko **Role (preview)** nebo **členy**.

    ![Role adresáře Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Klikněte na tlačítko **přidat člena** otevřete přidat spravované členy.

1. Klikněte na tlačítko **vybrat roli**, klikněte na roli, kterou chcete spravovat a pak klikněte na **vyberte**.

    ![Vyberte roli](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Klikněte na tlačítko **výběr členů**, vyberte uživatele, kterou chcete přiřadit k roli a potom klikněte na tlačítko **vyberte**.

    ![Vyberte roli](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. V přidat kliknutím na spravované členy **OK** přidejte uživatele k roli.

     Pokud se role přiřadí, uživatele, který jste vybrali se zobrazí v seznamu členů jako **oprávněné** pro danou roli.

    ![Oprávněné pro roli uživatele](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teď, když uživatel není oprávněný pro roli, informujte je, že si ji můžou aktivovat podle pokynů v [postup aktivace nebo deaktivace role](pim-how-to-activate-role.md).

    Oprávnění správce se zobrazí výzva k registraci pro Azure Multi-Factor Authentication (MFA) při aktivaci. Pokud uživatele nelze zaregistrovat pro vícefaktorové ověřování, nebo se pomocí účtu Microsoft (obvykle @outlook.com), je třeba provést je trvalé v jejich role.

## <a name="make-a-role-assignment-permanent"></a>Trvalé přiřazení role

Ve výchozím nastavení noví uživatelé mají nárok jenom roli adresáře. Pokud chcete, aby byly trvalé přiřazení role, postupujte podle těchto kroků.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **role adresáře Azure AD**.

1. Klikněte na tlačítko **členy**.

    ![Seznam členů](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Klikněte na tlačítko **oprávněné** role, která má být trvalé.

1. Klikněte na tlačítko **Další** a potom klikněte na tlačítko **zkontrolujte oprávnění**.

    ![Trvalé přiřazení role](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Role je nyní uveden jako **trvalé**.

    ![Seznam členů s trvalou změnu](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Odebrání uživatele z role

Odebrat uživatele z přiřazení rolí, ale ujistěte se, že je vždy alespoň jeden uživatel, který je globální správce je trvalý. Pokud si nejste jistí, které uživatelé stále potřebují svá přiřazení rolí, můžete si [zahájení kontroly přístupu pro roli](pim-how-to-start-security-review.md).

Následujícím postupem můžete odebrat konkrétní uživatele z role adresáře.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **role adresáře Azure AD**.

1. Klikněte na tlačítko **členy**.

    ![Seznam členů](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Klikněte na přiřazení role, kterou chcete odebrat.

1. Klikněte na tlačítko **Další** a potom klikněte na tlačítko **odebrat**.

    ![Odebrání role](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Ve zprávě s žádostí o potvrzení, klikněte na tlačítko **Ano**.

    ![Odebrání role](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Odebrání přiřazení role.

## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
