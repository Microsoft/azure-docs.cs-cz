---
title: Přiřazení rolí Azure AD v Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Naučte se přiřazovat role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61efc9d9d12cb67ea9c22e44ba0060db0f224f73
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554309"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Přiřazení rolí Azure AD v Privileged Identity Management

S Azure Active Directory (Azure AD) může globální správce vytvořit **trvalá** přiřazení rolí správce Azure AD. Tato přiřazení rolí se dají vytvořit pomocí [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) nebo pomocí [příkazů PowerShellu](/powershell/module/azuread#directory_roles).

Služba Azure AD Privileged Identity Management (PIM) taky umožňuje správcům privilegovaných rolí vytvářet trvalá přiřazení rolí správce. Správci privilegovaných rolí taky můžou uživatelům, kteří **mají nárok** na role správce Azure AD, provádět oprávnění. Oprávněný správce může roli aktivovat, když ji potřebují, a pak jejich oprávnění vyprší po dokončení.

## <a name="make-a-user-eligible-for-a-role"></a>Nastavit pro roli uživatele, který má nárok

Pomocí těchto kroků můžete uživateli poskytnout oprávnění pro roli správce Azure AD.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je členem role [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Informace o tom, jak udělit přístup jinému správci ke správě Privileged Identity Management, najdete v tématu [udělení přístupu jiným správcům pro správu Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Otevřete **Azure AD Privileged Identity Management**.

    Pokud jste v Azure Portal ještě nezačali Privileged Identity Management, pokračujte na [začátek používání Privileged Identity Management](pim-getting-started.md).

1. Klikněte na **role Azure AD**.

1. Klikněte na **role** nebo **Členové**.

    ![Zvýrazněné možnosti role Azure AD s nabídkami role a členové](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Kliknutím na **Přidat člena** otevřete přidat spravované členy.

1. Klikněte na **Vybrat roli**, klikněte na roli, kterou chcete spravovat, a potom klikněte na **Vybrat**.

    ![Vybrat podokno role s výpisem rolí Azure AD](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Klikněte na **Vybrat členy**, vyberte uživatele, které chcete přiřadit k roli, a pak klikněte na **Vybrat**.

    ![Vybrat podokno členů, kde můžete vybrat uživatele](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. V části Přidat spravované členy kliknutím na tlačítko **OK** přidejte uživatele do role.

1. V seznamu rolí klikněte na roli, kterou jste právě přiřadili, a zobrazte seznam členů.

     Po přiřazení role se uživatel, kterého jste vybrali, zobrazí v seznamu Členové jako **způsobilý** pro danou roli.

    ![Členové role jsou uvedeni spolu s jejich stavem aktivace.](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teď, když má uživatel nárok na roli, dejte jim jistotu, že ho můžou aktivovat podle pokynů v tématu [Aktivace rolí Azure AD v Privileged Identity Management](pim-how-to-activate-role.md).

    Způsobilí správci jsou během aktivace požádáni o registraci k Azure Multi-Factor Authentication (MFA). Pokud se uživatel nemůže zaregistrovat pro MFA nebo používá účet Microsoft (obvykle @outlook.com), musíte je v všech svých rolích nastavit jako trvalé.

## <a name="make-a-role-assignment-permanent"></a>Nastavit trvalé přiřazení role

Ve výchozím nastavení mají noví uživatelé nárok jenom na roli správce Azure AD. Pokud chcete nastavit trvalé přiřazení role, postupujte podle těchto kroků.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **role Azure AD**.

1. Klikněte na tlačítko **Členové**.

    ![Role Azure AD – seznam členů zobrazující role a stav aktivace](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Klikněte na **oprávněnou** roli, kterou chcete nastavit jako trvalou.

1. Klikněte na tlačítko **Další** a potom klikněte na tlačítko **nastavit oprávnění**.

    ![Podokno s výpisem uživatele, který má nárok na roli s dalšími možnostmi nabídky otevřené](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Role je nyní uvedena jako **trvalá**.

    ![Seznam členů znázorňující stav role a aktivace, která je teď trvalá](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Odebrání uživatele z role

Uživatele můžete odebrat z přiřazení rolí, ale ujistěte se, že je vždy aspoň jeden uživatel, který je trvalého globálního správce. Pokud si nejste jistí, kteří uživatelé stále potřebují přiřazení jejich rolí, můžete [pro roli spustit kontrolu přístupu](pim-how-to-start-security-review.md).

Pomocí těchto kroků můžete z role správce Azure AD odebrat konkrétního uživatele.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. Vyberte **členy**.

    ![Role Azure AD – seznam členů s informacemi o rolích a aktivaci](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Vyberte přiřazení role, které chcete odebrat.

1. Vyberte **Další** a potom **odeberte**.

    ![Podokno se seznamem uživatelů s trvalou rolí s otevřenými možnostmi další nabídky](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Až budete vyzváni k potvrzení akce, vyberte **Ano**.

    ![Zpráva s dotazem, zda chcete odebrat člena z role](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Přiřazení role se odebralo.

## <a name="authorization-error-when-assigning-roles"></a>Chyba autorizace při přiřazování rolí

Pokud jste nedávno povolili Privileged Identity Management pro předplatné Azure a při pokusu o vytvoření uživatele s oprávněním pro roli správce Azure AD dojde k chybě autorizace, může to být proto, že instanční objekt MS-PIM ještě nemá příslušné oprávnění. nastaven. K přiřazení rolí musí mít instanční objekt MS-PIM přiřazenou [roli správce přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator) v řízení přístupu na základě role Azure pro přístup k prostředkům Azure (na rozdíl od rolí pro správu Azure AD). Místo čekání na přiřazení role správce přístupu k uživateli přes MS-PIM ho můžete přiřadit ručně.

Následující kroky přiřadí roli správce přístupu uživatele k instančnímu objektu MS-PIM pro předplatné.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce ve vaší organizaci Azure AD.

1. Zvolte **všechny služby** a potom **předplatné**.

1. Zvolte vaše předplatné.

1. Zvolte **Řízení přístupu (IAM)** .

1. Zvolením možnosti **přiřazení rolí** zobrazíte aktuální seznam přiřazení rolí v oboru předplatného.

   ![Okno řízení přístupu (IAM) pro předplatné](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Ověřte, jestli je instančnímu objektu **MS-PIM** přiřazená role **Správce přístupu k uživateli** .

1. Pokud ne, kliknutím na **Přidat přiřazení role** otevřete podokno **Přidat přiřazení role** .

1. V rozevíracím seznamu **role** vyberte roli **Správce přístupu uživatele** .

1. V seznamu **Vybrat** vyhledejte a vyberte instanční objekt **MS-PIM** .

   ![Přidat podokno přiřazení role – přidání oprávnění pro instanční objekt MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Pro přiřazení role klikněte na tlačítko **Uložit** .

   Po chvíli se instančnímu objektu MS-PIM přiřadí role správce přístupu uživatele v oboru předplatného.

   ![Okno řízení přístupu (IAM) zobrazující přiřazení role správce přístupu uživatele pro MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role správce Azure AD v Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Přiřazení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-assign-roles.md)
