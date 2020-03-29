---
title: Přiřazení rolí Azure AD v PIM – Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, jak přiřadit role Azure AD v azure ad privilegované správy identit (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253270"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Přiřazení rolí Azure AD ve správě privilegovaných identit

Pomocí Služby Azure Active Directory (Azure AD) může globální správce vytvořit **trvalá** přiřazení rolí správce Azure AD. Tato přiřazení rolí lze vytvořit pomocí [portálu Azure nebo](../users-groups-roles/directory-assign-admin-roles.md) pomocí [příkazů Prostředí PowerShell](/powershell/module/azuread#directory_roles).

Služba Azure AD Privileged Identity Management (PIM) také umožňuje správcům privilegovaných rolí provádět přiřazení rolí trvalého správce. Správci privilegovaných rolí navíc můžou uživatelům **způsobit, že budou mít nárok** na role správce Azure AD. Oprávněný správce může roli aktivovat, když ji potřebuje, a po dokončení vyprší platnost jejich oprávnění.

## <a name="determine-your-version-of-pim"></a>Určení verze PIM

Počínaje listopadem 2019 se část rolí Azure AD privileged identity Management aktualizuje na novou verzi, která odpovídá prostředí pro role prostředků Azure. Tím se vytvoří další funkce, stejně jako [změny existující rozhraní API](azure-ad-roles-features.md#api-changes). Při zavádění nové verze, které postupy, které budete postupovat v tomto článku, závisí na verzi správy privilegovaných identit, kterou aktuálně máte. Podle pokynů v této části určete, kterou verzi správy privilegovaných identit máte. Poté, co znáte verzi správy privilegovaných identit, můžete vybrat postupy v tomto článku, které odpovídají této verzi.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) s uživatelem, který je v roli [správce privilegované role.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otevřete **správu privilegovaných identit Azure AD**. Pokud máte v horní části stránky přehledu nápis, postupujte podle pokynů na kartě **Nová verze** tohoto článku. V opačném případě postupujte podle pokynů na kartě **Předchozí verze.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nová verze](#tab/new)

## <a name="assign-a-role"></a>Přiřazení role

Postupujte podle následujících kroků, aby uživatel nárok na roli správce Azure AD.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) s uživatelem, který je členem role [správce privilegované role.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Informace o udělení přístupu jinému správci ke správě privilegovaných identit naleznete v [tématu Udělení přístupu ostatním správcům ke správě privilegovaných identit](pim-how-to-give-access-to-pim.md).

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **role Azure AD**.

1. Výběrem **role** zobrazíte seznam rolí pro oprávnění Azure AD.

    ![Role Azure AD](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Výběrem **možnosti Přidat člena** otevřete stránku **Nové přiřazení.**

1. Vyberte **Vybrat roli,** chcete-li otevřít stránku Vybrat roli.

    ![Nové podokno přiřazení](./media/pim-how-to-add-role-to-user/select-role.png)

1. Vyberte roli, kterou chcete přiřadit, a klepněte na tlačítko **Vybrat**.

1. Vyberte člena, kterému chcete roli přiřadit, a pak vyberte **Vybrat**.

1. V seznamu **Typ přiřazení** v podokně **Nastavení členství** vyberte **Možnost způsobilé** nebo **Aktivní**.

    - **Způsobilá** přiřazení vyžadují, aby člen role provedl akci k použití role. Akce mohou zahrnovat provedení kontroly vícefaktorového ověřování (MFA), poskytnutí obchodního odůvodnění nebo vyžádání schválení od určených schvalovatelů.

    - **Aktivní** přiřazení nevyžadují, aby člen provedl žádnou akci k použití role. Členům přiřazeným jako aktivní mají oprávnění přiřazená roli po celou dobu.

1. Pokud by přiřazení mělo být trvalé (trvale způsobilé nebo trvale přiřazené), zaškrtněte políčko **Trvale.**

    V závislosti na nastavení role se zaškrtávací políčko nemusí zobrazit nebo může být neupravitelné.

1. Chcete-li určit určitou dobu trvání přiřazení, zrušte zaškrtnutí políčka a upravte pole počáteční ho nebo koncového data a času. Po dokončení vyberte **Hotovo**.

    ![Nastavení členství - datum a čas](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Chcete-li vytvořit nové přiřazení role, vyberte **přidat**. Zobrazí se oznámení o stavu.

    ![Nové přiřazení - oznámení](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizace nebo odebrání existujícího přiřazení role

Podle těchto kroků aktualizujte nebo odeberte existující přiřazení role.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **role Azure AD**.

1. Výběrem **role** zobrazíte seznam rolí pro Azure AD.

1. Vyberte roli, kterou chcete aktualizovat nebo odebrat.

1. Najděte přiřazení role na kartách **Způsobilé role** nebo **Aktivní role.**

    ![Aktualizace nebo odebrání přiřazení role](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Chcete-li aktualizovat nebo odebrat, vyberte **aktualizovat** nebo **odebrat** přiřazení role.

# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Nastavení uživatele pro roli

Postupujte podle následujících kroků, aby uživatel nárok na roli správce Azure AD.

1. Vyberte **role** nebo **členy**.

    ![Role Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Vyberte **Přidat člena,** chcete-li otevřít **možnost Přidat spravované členy**.

1. Vyberte **Vybrat roli**, vyberte roli, kterou chcete spravovat, a pak vyberte **Vybrat**.

    ![Vybrat roli](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Vyberte **Vybrat členy**, vyberte uživatele, které chcete roli přiřadit, a pak vyberte **Vybrat**.

    ![Vybrat roli](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. V **části Přidat spravované členy**vyberte **OK,** chcete-li přidat uživatele do role.

1. V seznamu rolí vyberte roli, kterou jste právě přiřadili, abyste viděli seznam členů.

     Po přiřazení role se vybraný uživatel zobrazí v seznamu členů jako **Způsobilý** pro roli.

    ![Uživatel, který má nárok na roli](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teď, když má uživatel nárok na roli, dejte mu vědět, že ji může aktivovat podle pokynů v [části Aktivovat role Azure AD v privilegované správě identit](pim-how-to-activate-role.md).

    Oprávnění správci jsou požádáni o registraci pro Azure Multi-Factor Authentication (MFA) během aktivace. Pokud se uživatel nemůže zaregistrovat pro vícefaktorové ověřování @outlook.comnebo používá účet Microsoft (například ), je třeba, aby byl trvalý ve všech jeho rolích.

## <a name="make-a-role-assignment-permanent"></a>Nastavení přiřazení role jako trvalého

Ve výchozím nastavení mají noví uživatelé *nárok jenom* pro roli správce Azure AD. Pokud chcete přiřazení role nařídit jako trvalé, postupujte takto.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **role Azure AD**.

1. Vyberte možnost **Členové**.

    ![Seznam členů](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Vyberte **způsobilou** roli, kterou chcete vytvořit trvalou.

1. Vyberte **Další** a pak **vyberte Vytvořit perm**.

    ![Nastavení trvalého přiřazení role](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Role je nyní uvedena jako **trvalá**.

    ![Seznam členů s trvalou změnou](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Odebrání uživatele z role

Uživatele můžete odebrat z přiřazení rolí, ale ujistěte se, že vždy existuje alespoň jeden uživatel, který je trvalým globálním správcem. Pokud si nejste jisti, kteří uživatelé stále potřebují přiřazení rolí, můžete [zahájit kontrolu přístupu k roli](pim-how-to-start-security-review.md).

Podle těchto kroků odeberte konkrétního uživatele z role správce Azure AD.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **role Azure AD**.

1. Vyberte možnost **Členové**.

    ![Seznam členů](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Vyberte přiřazení role, které chcete odebrat.

1. Vyberte **Další** a pak vyberte **Odebrat**.

    ![Odebrání role](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Ve zprávě, která vás vyzve k potvrzení, vyberte **Ano**.

    ![Odebrání role](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Přiřazení role je odebráno.

## <a name="authorization-error-when-assigning-roles"></a>Chyba autorizace při přiřazování rolí

Pokud jste nedávno povolili oprávnění Správa privilegovaných identit pro předplatné a při pokusu o to, aby uživatel měl nárok na roli správce Azure AD, dojde k chybě autorizace, může to být způsobeno tím, že hlavní uživatel služby MS-PIM ještě nemá příslušná oprávnění. Instanční objekt MS-PIM musí mít roli [Správce přístupu uživatelů,](../../role-based-access-control/built-in-roles.md#user-access-administrator) aby mohl přiřadit role ostatním uživatelům. Místo čekání na přiřazení role Správce přístupu uživatele k systému MS-PIM ji můžete přiřadit ručně.

Následujícím postupem přiřaďte roli Správce přístupu uživatelů k instančnímu objektu služby MS-PIM pro předplatné.

1. Přihlaste se k portálu Azure jako globální správce.

1. Zvolte **Všechny služby** a potom **Předplatná**.

1. Zvolte vaše předplatné.

1. Zvolte **Řízení přístupu (IAM)**.

1. Zvolte **přiřazení rolí,** chcete-li zobrazit aktuální seznam přiřazení rolí v oboru předplatného.

   ![Okno řízení přístupu (IAM) pro předplatné](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Zkontrolujte, zda je instančnímu objektu **služby MS-PIM** přiřazena role **Správce přístupu uživatelů.**

1. Pokud ne, zvolte **Přidat přiřazení role** a otevřete podokno Přidat přiřazení **role.**

1. V rozevíracím seznamu **Role** vyberte roli **Správce přístupu uživatelů.**

1. V seznamu **Vybrat** vyhledejte a vyberte instanční objekt **služby MS-PIM.**

   ![Podokno přiřazení rolí – přidání oprávnění pro instanční objekt MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Chcete-li roli přiřadit, zvolte **Uložit.**

   Po několika okamžicích je instančnímu objektu služby MS-PIM přiřazena role Správce přístupu uživatelů v oboru předplatného.

   ![Stránka řízení přístupu zobrazující přiřazení role správce přístupu uživatele pro instanční objekt MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role správce Azure AD v privilegované správě identit](pim-how-to-change-default-settings.md)
- [Přiřazení rolí prostředků Azure ve správě privilegovaných identit](pim-resource-roles-assign-roles.md)