---
title: Přiřazení rolí prostředků Azure ve službě Správa privilegovaných identit – Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, jak přiřadit role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266556"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Přiřazení rolí prostředků Azure ve správě privilegovaných identit

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete spravovat integrované role prostředků Azure, stejně jako vlastní role, včetně (ale bez omezení na):

- Vlastník
- Správce přístupu uživatelů
- Přispěvatel
- Správce zabezpečení
- Správce zabezpečení

> [!NOTE]
> Uživatelé nebo členové skupiny přiřazené k rolím předplatného vlastníka nebo správce přístupu uživatelů a správci Azure AD Global, kteří povolují správu předplatného ve službě Azure AD, mají ve výchozím nastavení oprávnění správce prostředků. Tito správci můžou přiřazovat role, konfigurovat nastavení rolí a kontrolovat přístup pomocí prostředků Privileged Identity Management for Azure. Uživatel nemůže spravovat správu privilegovaných identit pro prostředky bez oprávnění správce prostředků. Zobrazení seznamu [předdefinovaných rolí pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Přiřazení role

Postupujte podle následujících kroků, aby uživatel nárok na roli prostředku Azure.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) s uživatelem, který je členem role [správce privilegované role.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Informace o udělení přístupu jinému správci ke správě privilegovaných identit naleznete v [tématu Udělení přístupu ostatním správcům ke správě privilegovaných identit](pim-how-to-give-access-to-pim.md).

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **prostředky Azure**.

1. Pomocí **filtru Zdroje** můžete filtrovat seznam spravovaných prostředků.

    ![Seznam prostředků Azure ke správě](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Vyberte prostředek, který chcete spravovat, například předplatné nebo skupinu pro správu.

1. V části Správa vyberte **Role,** chcete-li zobrazit seznam rolí pro prostředky Azure.

    ![Role prostředků Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Výběrem **možnosti Přidat člena** otevřete podokno Nové přiřazení.

1. Výběrem **možnosti Vybrat roli** otevřete podokno Vybrat roli.

    ![Nové podokno přiřazení](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Vyberte roli, kterou chcete přiřadit, a klepněte na tlačítko **Vybrat**.

    Otevře se podokno Vybrat člena nebo skupinu.

1. Vyberte člena nebo skupinu, kterou chcete roli přiřadit, a klepněte na tlačítko **Vybrat**.

    ![Výběr podokna člena nebo skupiny](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Otevře se podokno Nastavení členství.

1. V seznamu **Typ přiřazení** vyberte **Možnost způsobilé** nebo **Aktivní**.

    ![Podokno Nastavení členství](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Správa privilegovaných identit pro prostředky Azure poskytuje dva odlišné typy přiřazení:

    - **Způsobilá** přiřazení vyžadují, aby člen role provedl akci k použití role. Akce mohou zahrnovat provedení kontroly vícefaktorového ověřování (MFA), poskytnutí obchodního odůvodnění nebo vyžádání schválení od určených schvalovatelů.

    - **Aktivní** přiřazení nevyžadují, aby člen provedl žádnou akci k použití role. Členům přiřazeným jako aktivní mají oprávnění přiřazená roli po celou dobu.

1. Pokud by přiřazení mělo být trvalé (trvale způsobilé nebo trvale přiřazené), zaškrtněte políčko **Trvale.**

    V závislosti na nastavení role se zaškrtávací políčko nemusí zobrazit nebo může být neupravitelné.

1. Chcete-li určit určitou dobu trvání přiřazení, zrušte zaškrtnutí políčka a upravte pole počáteční ho nebo koncového data a času.

    ![Nastavení členství - datum a čas](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Po dokončení vyberte **Hotovo**.

    ![Nové přiřazení - Přidat](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Chcete-li vytvořit nové přiřazení role, vyberte **přidat**. Zobrazí se oznámení o stavu.

    ![Nové přiřazení - oznámení](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizace nebo odebrání existujícího přiřazení role

Podle těchto kroků aktualizujte nebo odeberte existující přiřazení role.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **prostředky Azure**.

1. Vyberte prostředek, který chcete spravovat, například předplatné nebo skupinu pro správu.

1. V části Správa vyberte **Role,** chcete-li zobrazit seznam rolí pro prostředky Azure.

    ![Role prostředků Azure – vybrat roli](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Vyberte roli, kterou chcete aktualizovat nebo odebrat.

1. Najděte přiřazení role na kartách **Způsobilé role** nebo **Aktivní role.**

    ![Aktualizace nebo odebrání přiřazení role](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Chcete-li aktualizovat nebo odebrat, vyberte **aktualizovat** nebo **odebrat** přiřazení role.

    Informace o rozšíření přiřazení role najdete v tématu [Rozšíření nebo obnovení rolí prostředků Azure v privilegované správě identit](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Další kroky

- [Rozšíření nebo obnovení rolí prostředků Azure ve správě privilegovaných identit](pim-resource-roles-renew-extend.md)
- [Konfigurace nastavení role prostředků Azure ve správě privilegovaných identit](pim-resource-roles-configure-role-settings.md)
- [Přiřazení rolí Azure AD ve správě privilegovaných identit](pim-how-to-add-role-to-user.md)
