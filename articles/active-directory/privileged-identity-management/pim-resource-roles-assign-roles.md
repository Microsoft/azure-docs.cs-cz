---
title: Přiřazení role prostředků Azure v PIM | Dokumentace Microsoftu
description: Zjistěte, jak přiřadit role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7b82be6cdc8b64595c11eef84e8071fad9c07191
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665459"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Přiřazení role prostředků Azure v PIM

Azure AD PIM můžete spravovat role integrované prostředků Azure, jakož i vlastní role, včetně (ale nikoli výhradně):

- Vlastník
- Správce přístupu uživatelů
- Přispěvatel
- Správce zabezpečení
- Správce zabezpečení a další

>[!NOTE]
Uživatele nebo členy skupiny přiřadit role vlastník nebo správce přístupu uživatelů a globální správci, který povolení správy předplatného ve službě Azure AD jsou správci prostředků. Tyto správce může přiřadit role, konfigurace nastavení role a kontrolovat přístup pomocí PIM pro prostředky Azure. Zobrazí se seznam [předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Přiřazení role

Následujícím postupem nastavit uživatele jako oprávněné pro roli prostředků Azure.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako uživatel, který je členem skupiny [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) role.

    Informace o tom, jak udělit jiný přístup správce ke správě PIM, naleznete v tématu [udělit přístup na jiné správce ke správě PIM](pim-how-to-give-access-to-pim.md).

1. Otevřít **Azure AD Privileged Identity Management**.

    Pokud jste nezahájili PIM na portálu Azure portal ještě, přejděte na [začít používat PIM](pim-getting-started.md).

1. Klikněte na tlačítko **prostředky Azure**.

1. Použití **filtr prostředků** filtrovat seznam spravovaných prostředků.

    ![Seznam prostředků Azure pro správu](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Klikněte na prostředek, který chcete spravovat, například předplatné nebo skupinu pro správu.

1. V části Správa, klikněte na tlačítko **role** zobrazíte seznam rolí pro prostředky Azure.

    ![Role prostředků Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Klikněte na tlačítko **přidat člena** a otevřete tak podokno nové přiřazení.

1. Klikněte na tlačítko **vybrat roli** a otevřete tak podokno role Select.

    ![Nové přiřazení podokno](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Klikněte na roli, kterou chcete přiřadit a potom klikněte na tlačítko **vyberte**.

    Vyberte člena nebo skupinu podokně otevře.

1. Klikněte na člena nebo skupinu, kterou chcete přiřadit k roli a potom klikněte na **vyberte**.

    ![Vyberte člena nebo skupiny podokno](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Otevře se podokno nastavení členství.

1. V **typ přiřazení** seznamu vyberte **oprávněné** nebo **aktivní**.

    ![Podokno nastavení členství ve skupinách](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    PIM pro prostředky Azure poskytuje dva typy různých přiřazení:

    - **Oprávněné** přiřazení vyžadovat členem role provádět akci, kterou chcete použít roli. Akce může zahrnovat provedení kontroly ověřování službou Multi-Factor Authentication (MFA), poskytuje obchodní odůvodnění, nebo z určených schvalovatelů odesílání žádostí o schválení.

    - **Aktivní** přiřazení nevyžadují člen podnikat žádné kroky, chcete-li použít roli. Členové přiřazeno jako aktivní mají oprávnění přiřazená k roli za všech okolností.

1. Pokud má být trvalé přiřazení (trvalá způsobilost nebo při trvalém přiřazení), vyberte **trvale** zaškrtávací políčko.

    V závislosti na nastavení role zaškrtněte políčko neobjeví, nebo může neupravitelných.

1. Chcete-li určit dobu trvání konkrétní přiřazení, zrušte zaškrtnutí políčka a upravit počáteční a koncové datum a čas polí.

    ![Členství v nastavení - datum a čas](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Jakmile budete hotovi, klikněte na **Hotovo**.

    ![Nové přiřazení – přidat](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Chcete-li vytvořit nové přiřazení role, klikněte na tlačítko **přidat**. Zobrazí se oznámení o stavu.

    ![Nové přiřazení – oznámení](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizace nebo odebrání stávající přiřazení rolí

Postupujte podle těchto kroků k aktualizaci nebo odebrání stávající přiřazení rolí.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **prostředky Azure**.

1. Klikněte na prostředek, který chcete spravovat, například předplatné nebo skupinu pro správu.

1. V části Správa, klikněte na tlačítko **role** zobrazíte seznam rolí pro prostředky Azure.

    ![Role prostředků Azure – vybrat roli](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Klikněte na roli, kterou chcete aktualizovat nebo odebrat.

1. Najít přiřazení role na **oprávněných rolí** nebo **aktivních rolí** karty.

    ![Aktualizace nebo odebrání přiřazení role](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Klikněte na tlačítko **aktualizovat** nebo **odebrat** k aktualizaci nebo odebrání přiřazení role.

    Informace o rozšíření přiřazení rolí, najdete v části [rozšířit nebo obnovení role prostředků Azure v PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Další postup

- [Rozšíření nebo obnovení role prostředků Azure v PIM](pim-resource-roles-renew-extend.md)
- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)
- [Přiřazení role adresáře Azure AD v PIM](pim-how-to-add-role-to-user.md)
