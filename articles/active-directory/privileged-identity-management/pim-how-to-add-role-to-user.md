---
title: Přiřazení role adresáře Azure AD v PIM | Dokumentace Microsoftu
description: Zjistěte, jak přiřadit role adresáře Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 10/30/2018
ms.author: rolyon
ms.openlocfilehash: 2b099e1377536b46229b75f25d04ab2c1beb5c11
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724940"
---
# <a name="assign-azure-ad-directory-roles-in-pim"></a>Přiřazení role adresáře Azure AD v PIM

S Azure Active Directory (Azure AD), globální správce může nastavit **trvalé** přiřazení rolí adresáře. Tato přiřazení rolí můžete vytvořit pomocí [webu Azure portal](../users-groups-roles/directory-assign-admin-roles.md) nebo pomocí [příkazy prostředí PowerShell](/powershell/module/azuread#directory_roles).

Služba Azure AD Privileged Identity Management (PIM) také umožňuje správcům privilegovaných rolí adresáře trvalé přiřazení rolí. Kromě toho správci privilegovaných rolí, můžete nastavit uživatele **oprávněné** pro role adresáře. Správce může aktivovat roli, když ho potřebují, a potom jejich oprávnění vyprší po jejich dokončení. Informace o rolích, které můžete spravovat pomocí PIM, naleznete v tématu [role adresáře Azure AD můžete spravovat v PIM](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Nastavit jako oprávněné pro roli uživatele

Následujícím postupem nastavit uživatele jako oprávněné pro role adresáře Azure AD.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako uživatel, který je členem skupiny [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) role.

    Informace o tom, jak udělit jiný přístup správce ke správě PIM, naleznete v tématu [udělit přístup na jiné správce ke správě PIM](pim-how-to-give-access-to-pim.md).

1. Otevřít **Azure AD Privileged Identity Management**.

    Pokud jste nezahájili PIM na portálu Azure portal ještě, přejděte na [začít používat PIM](pim-getting-started.md).

1. Klikněte na tlačítko **role adresáře Azure AD**.

1. Klikněte na tlačítko **role** nebo **členy**.

    ![Role adresáře Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Klikněte na tlačítko **přidat člena** otevřete přidat spravované členy.

1. Klikněte na tlačítko **vybrat roli**, klikněte na roli, kterou chcete spravovat a pak klikněte na **vyberte**.

    ![Vyberte roli](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Klikněte na tlačítko **výběr členů**, vyberte uživatele, kterou chcete přiřadit k roli a potom klikněte na tlačítko **vyberte**.

    ![Vyberte roli](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. V přidat kliknutím na spravované členy **OK** přidejte uživatele k roli.

1. V seznamu rolí vyberte roli, kterou právě přiřazené zobrazíte seznam členů.

     Pokud se role přiřadí, uživatele, který jste vybrali se zobrazí v seznamu členů jako **oprávněné** pro danou roli.

    ![Oprávněné pro roli uživatele](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teď, když uživatel není oprávněný pro roli, informujte je, že si ji můžou aktivovat podle pokynů v [aktivovat Moje role adresáře Azure AD v PIM](pim-how-to-activate-role.md).

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

## <a name="authorization-error-when-assigning-roles"></a>Chyba autorizace při přiřazování role

Pokud jste nedávno povolili PIM pro předplatné a dojde k chybě autorizace při pokusu o nastavit jako oprávněné pro role adresáře uživatele, může to být způsobeno instančnímu objektu MS-PIM ještě nemá příslušná oprávnění. Musí mít instančnímu objektu MS-PIM [správce uživatelských přístupů](../../role-based-access-control/built-in-roles.md#user-access-administrator) roli přiřadit role jiným uživatelům. Namísto čekání, dokud MS-PIM má přiřazenou roli správce uživatelských přístupů, ji můžete přiřadit ručně.

Postupujte podle těchto kroků přiřazení role správce přístupu uživatelů k MS PIM instanční objekt pro předplatné.

1. Přihlaste se na webu Azure portal jako globální správce.

1. Zvolte **všechny služby** a potom **předplatná**.

1. Zvolte vaše předplatné.

1. Zvolte **Řízení přístupu (IAM)**.

1. Zvolte **přiřazení rolí** zobrazíte aktuální seznam přiřazení rolí v oboru předplatného.

   ![Okno pro kontrolu (IAM) přístup k předplatnému](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Zkontrolujte, zda **MS-PIM** instančnímu objektu přiřadí **správce uživatelských přístupů** role.

1. Pokud ne, zvolte **přidat přiřazení role** otevřít **přidat přiřazení role** podokně.

1. V **Role** rozevíracího seznamu, vyberte **správce uživatelských přístupů** role.

1. V **vyberte** seznamu vyhledejte a vyberte **MS-PIM** instančního objektu.

   ![Přidání oprávnění pro MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Zvolte **Uložit** přiřazení role.

   Po chvíli MS-PIM instanční objekt je přiřazena role správce uživatelských přístupů v oboru předplatného.

   ![Role správce přístupu uživatelů pro MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role adresáře Azure AD v PIM](pim-how-to-change-default-settings.md)
- [Přiřazení role prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
