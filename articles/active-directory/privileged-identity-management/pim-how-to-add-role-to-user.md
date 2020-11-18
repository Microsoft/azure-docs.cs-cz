---
title: Přiřazení rolí Azure AD v PIM-Azure Active Directory | Microsoft Docs
description: Naučte se přiřazovat role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/16/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0e230a975748fe2f737c4b8fe8491887351d387
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835369"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Přiřazení rolí Azure AD v Privileged Identity Management

S Azure Active Directory (Azure AD) může globální správce vytvořit **trvalá** přiřazení rolí správce Azure AD. Tato přiřazení rolí se dají vytvořit pomocí [Azure Portal](../roles/permissions-reference.md) nebo pomocí [příkazů PowerShellu](/powershell/module/azuread#directory_roles).

Služba Azure AD Privileged Identity Management (PIM) taky umožňuje správcům privilegovaných rolí vytvářet trvalá přiřazení rolí správce. Správci privilegovaných rolí taky můžou uživatelům, kteří **mají nárok** na role správce Azure AD, provádět oprávnění. Oprávněný správce může roli aktivovat, když ji potřebují, a pak jejich oprávnění vyprší po dokončení.

## <a name="determine-your-version-of-pim"></a>Určení vaší verze PIM

Od listopadu 2019 se v části Privileged Identity Management role Azure AD aktualizuje na novou verzi, která se shoduje s prostředími pro role prostředků Azure. Tím se vytvoří další funkce a také [změny stávajícího rozhraní API](azure-ad-roles-features.md#api-changes). I když je nová verze zahrnuta, postupy, které provedete v tomto článku, závisí na verzi Privileged Identity Management, kterou máte v současnosti k dispozici. Podle pokynů v této části určete, kterou verzi Privileged Identity Management máte. Po zjištění vaší verze Privileged Identity Management můžete vybrat postupy v tomto článku, které odpovídají této verzi.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí uživatele, který je v roli [správce privilegované role](../roles/permissions-reference.md#privileged-role-administrator) .
1. Otevřete **Azure AD Privileged Identity Management**. Pokud máte banner v horní části stránky s přehledem, postupujte podle pokynů na kartě **Nová verze** v tomto článku. Jinak postupujte podle pokynů na kartě **předchozí verze** .

  [![Vyberte Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nová verze](#tab/new)

## <a name="assign-a-role"></a>Přiřazení role

Pomocí těchto kroků můžete uživateli poskytnout oprávnění pro roli správce Azure AD.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je členem role [správce privilegovaných rolí](../roles/permissions-reference.md#privileged-role-administrator) .

    Informace o tom, jak udělit přístup jinému správci ke správě Privileged Identity Management, najdete v tématu [udělení přístupu jiným správcům pro správu Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. Vyberte **role** , abyste viděli seznam rolí pro oprávnění Azure AD.

    ![Snímek obrazovky se stránkou role se zvolenou akcí přidat přiřazení](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Výběrem **Přidat přiřazení** otevřete stránku **Přidat přiřazení** .

1. Vyberte **Vybrat roli** a otevřete stránku **Vybrat roli** .

    ![Podokno nového přiřazení](./media/pim-how-to-add-role-to-user/select-role.png)

1. Vyberte roli, kterou chcete přiřadit, vyberte člena, kterému chcete přiřadit roli, a pak vyberte **Další**.

1. V seznamu **Typ přiřazení** v podokně **nastavení členství** vyberte možnost **oprávněné** nebo **aktivní**.

    - **Způsobilá** přiřazení vyžadují, aby člen role prováděl akci pro použití role. Akce můžou zahrnovat provádění kontroly vícefaktorového ověřování (MFA), poskytování obchodního odůvodnění nebo žádosti o schválení od určených schvalovatelů.

    - **Aktivní** přiřazení nevyžadují, aby člen prováděl žádnou akci pro použití role. Členové přiřazení jako aktivní mají vždy přiřazená oprávnění k této roli.

1. Chcete-li zadat konkrétní dobu trvání přiřazení, přidejte pole počáteční a koncové datum a čas. Až budete hotovi, vyberte **přiřadit** a vytvořte nové přiřazení role.

    ![Nastavení členství – datum a čas](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Po přiřazení role se zobrazí oznámení o stavu přiřazení.

    ![Nové přiřazení – oznámení](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Přiřazení role s omezeným rozsahem

U některých rolí může být obor udělených oprávnění omezený na jednu jednotku správce, instanční objekt nebo aplikaci. Tento postup je příkladem přiřazení role s oborem jednotky pro správu. Seznam rolí, které podporují rozsah prostřednictvím jednotky pro správu, najdete v tématu [přiřazení vymezených rolí k jednotce pro správu](../roles/admin-units-assign-roles.md). Tato funkce se v tuto chvíli zavádí do organizací Azure AD.

1. Přihlaste se k [centru pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí oprávnění správce privilegovaných rolí.

1. Vyberte **Azure Active Directory**  >  **role a správci**.

1. Vyberte **správce uživatele**.

    ![Příkaz Přidat přiřazení je k dispozici při otevření role na portálu.](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Vyberte **Přidat přiřazení**.

    ![Když role podporuje rozsah, můžete vybrat obor.](./media/pim-how-to-add-role-to-user/add-scope.png)

1. Na stránce **Přidat přiřazení** můžete:

   - Vyberte uživatele nebo skupinu, které chcete přiřadit roli.
   - Vyberte obor role (v tomto případě jednotky pro správu).
   - Vybrat jednotku pro správu pro obor

Další informace o vytváření jednotek pro správu najdete v tématu [Přidání a odebrání jednotek pro správu](../roles/admin-units-manage.md).

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizovat nebo odebrat existující přiřazení role

Pomocí těchto kroků aktualizujete nebo odeberete existující přiřazení role.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. Vyberte **role** , abyste viděli seznam rolí pro Azure AD.

1. Vyberte roli, kterou chcete aktualizovat nebo odebrat.

1. Vyhledejte přiřazení role na kartách **opravňující role** nebo **aktivní role** .

    ![Aktualizovat nebo odebrat přiřazení role](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Pokud chcete aktualizovat nebo odebrat přiřazení role, vyberte **aktualizovat** nebo **Odebrat** .

# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Nastavit pro roli uživatele, který má nárok

Pomocí těchto kroků můžete uživateli poskytnout oprávnění pro roli správce Azure AD.

1. Vyberte **role** nebo **členy**.

    ![otevřít role Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Kliknutím na **Přidat člena** otevřete **Přidat spravované členy**.

1. Vyberte **Vybrat roli**, vyberte roli, kterou chcete spravovat, a pak vyberte **Vybrat**.

    ![Vybrat roli](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Vyberte **Vybrat členy**, vyberte uživatele, které chcete roli přiřadit, a pak vyberte **Vybrat**.

    ![Vyberte uživatele nebo skupinu, které chcete přiřadit.](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. V části **Přidat spravované členy** vyberte **OK** a přidejte uživatele do role.

1. V seznamu rolí vyberte roli, kterou jste právě přiřadili, a zobrazte seznam členů.

     Po přiřazení role se uživatel, kterého jste vybrali, zobrazí v seznamu Členové jako **způsobilý** pro danou roli.

    ![Uživatel má nárok na roli.](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teď, když má uživatel nárok na roli, dejte jim jistotu, že ho můžou aktivovat podle pokynů v tématu [Aktivace rolí Azure AD v Privileged Identity Management](pim-how-to-activate-role.md).

    Způsobilí správci jsou během aktivace vyzváni k registraci pro Multi-Factor Authentication Azure AD. Pokud se uživatel nemůže zaregistrovat pro MFA nebo používá účet Microsoft (například @outlook.com ), musíte je v všech svých rolích nastavit jako trvalé.

## <a name="make-a-role-assignment-permanent"></a>Nastavit trvalé přiřazení role

Ve výchozím nastavení mají noví uživatelé *nárok* jenom na roli správce Azure AD. Pokud chcete nastavit trvalé přiřazení role, postupujte podle těchto kroků.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. Vyberte možnost **Členové**.

    ![Seznam členů](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Vyberte **oprávněnou** roli, kterou chcete nastavit jako trvalou.

1. Vyberte **Další** a pak vyberte **nastavit oprávnění**.

    ![Nastavit přiřazení role jako trvalé](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Role je nyní uvedena jako **trvalá**.

    ![Seznam členů s trvalou změnou](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Odebrání uživatele z role

Uživatele můžete odebrat z přiřazení rolí, ale ujistěte se, že je vždy aspoň jeden uživatel, který je trvalého globálního správce. Pokud si nejste jistí, kteří uživatelé stále potřebují přiřazení jejich rolí, můžete [pro roli spustit kontrolu přístupu](pim-how-to-start-security-review.md).

Pomocí těchto kroků můžete z role správce Azure AD odebrat konkrétního uživatele.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. Vyberte možnost **Členové**.

    ![Seznam členů](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Vyberte přiřazení role, které chcete odebrat.

1. Vyberte **Další** a pak vyberte **Odebrat**.

    ![Odebrání role](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Ve zprávě, která vás vyzve k potvrzení, vyberte **Ano**.

    ![Potvrzení odebrání](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Přiřazení role se odebralo.

## <a name="authorization-error-when-assigning-roles"></a>Chyba autorizace při přiřazování rolí

Pokud jste nedávno povolili Privileged Identity Management pro předplatné a při pokusu o vytvoření uživatele s oprávněním pro roli správce Azure AD dojde k chybě autorizace, může to být způsobeno tím, že instanční objekt MS-PIM ještě nemá příslušná oprávnění. Instanční objekt MS-PIM musí mít roli [Správce přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator) , aby mohl přiřadit role jiným uživatelům. Místo čekání na přiřazení role správce přístupu k uživateli přes MS-PIM ho můžete přiřadit ručně.

Pomocí těchto kroků přiřaďte roli správce přístupu uživatele k instančnímu objektu MS-PIM pro předplatné.

1. Přihlaste se k Azure Portal jako globální správce.

1. Zvolte **všechny služby** a potom **předplatné**.

1. Zvolte vaše předplatné.

1. Zvolte **Řízení přístupu (IAM)**.

1. Zvolením možnosti **přiřazení rolí** zobrazíte aktuální seznam přiřazení rolí v oboru předplatného.

   ![Okno řízení přístupu (IAM) pro předplatné](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Ověřte, jestli je instančnímu objektu **MS-PIM** přiřazená role **Správce přístupu k uživateli** .

1. Pokud ne, kliknutím na **Přidat přiřazení role** otevřete podokno **Přidat přiřazení role** .

1. V rozevíracím seznamu **role** vyberte roli **Správce přístupu uživatele** .

1. V seznamu **Vybrat** vyhledejte a vyberte instanční objekt **MS-PIM** .

   ![Přidat podokno přiřazení role – přidání oprávnění pro instanční objekt MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Pro přiřazení role klikněte na tlačítko **Uložit** .

   Po chvíli se instančnímu objektu MS-PIM přiřadí role správce přístupu uživatele v oboru předplatného.

   ![Stránka řízení přístupu zobrazující přiřazení role správce přístupu uživatele pro instanční objekt MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role správce Azure AD v Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Přiřazení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-assign-roles.md)