---
title: Přiřazení rolí prostředků Azure v PIM-Azure Active Directory | Microsoft Docs
description: Naučte se přiřazovat role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5919c9ab53761649c55e67ceacec0409174e9120
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617030"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Přiřazení rolí prostředků Azure v PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) může spravovat předdefinované role prostředků Azure i vlastní role, včetně (ale ne omezení):

- Owner
- Správce přístupu uživatelů
- Přispěvatel
- Správce zabezpečení
- Správce zabezpečení a další

> [!NOTE]
> Uživatelé nebo členové skupiny přiřazení rolí vlastník nebo správce přístupu uživatelů a globální správci, kteří umožňují správu předplatného ve službě Azure AD, jsou správci prostředků. Tito správci mohou přiřazovat role, konfigurovat nastavení rolí a kontrolovat přístup pomocí PIM pro prostředky Azure. To znamená, že účet nebude mít práva pro správu PIM pro prostředky, pokud uživatel nemá roli správce prostředků. Prohlédněte si seznam [předdefinovaných rolí pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Přiřazení role

Pomocí těchto kroků můžete uživateli poskytnout oprávnění k roli prostředku Azure.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je členem role [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Informace o tom, jak udělit přístup jinému správci ke správě PIM, najdete v tématu [udělení přístupu jiným správcům pro správu PIM](pim-how-to-give-access-to-pim.md).

1. Otevřete **Azure AD Privileged Identity Management**.

    Pokud jste v Azure Portal ještě nezahájili PIM, pokračujte na [začátek používání PIM](pim-getting-started.md).

1. Klikněte na **prostředky Azure**.

1. Pomocí **filtru prostředků** vyfiltrujte seznam spravovaných prostředků.

    ![Seznam prostředků Azure, které se mají spravovat](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Klikněte na prostředek, který chcete spravovat, jako je například předplatné nebo skupina pro správu.

1. V části Spravovat klikněte na **role** . zobrazí se seznam rolí pro prostředky Azure.

    ![Role prostředků Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Kliknutím na **Přidat člena** otevřete nové podokno přiřazení.

1. Kliknutím na **Vybrat roli** otevřete podokno vybrat roli.

    ![Podokno nového přiřazení](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Klikněte na roli, kterou chcete přiřadit, a potom klikněte na **Vybrat**.

    Otevře se podokno vybrat člena nebo skupinu.

1. Klikněte na člena nebo skupinu, které chcete přiřadit k roli, a potom klikněte na **Vybrat**.

    ![Vybrat člena nebo podokno skupiny](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Otevře se podokno nastavení členství.

1. V seznamu **Typ přiřazení** vyberte možnost **oprávněné** nebo **aktivní**.

    ![Podokno nastavení členství](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    PIM pro prostředky Azure nabízí dva odlišné typy přiřazení:

    - **Způsobilá** přiřazení vyžadují, aby člen role prováděl akci pro použití role. Akce můžou zahrnovat provádění kontroly vícefaktorového ověřování (MFA), poskytování obchodního odůvodnění nebo žádosti o schválení od určených schvalovatelů.

    - **Aktivní** přiřazení nevyžadují, aby člen prováděl žádnou akci pro použití role. Členové přiřazení jako aktivní mají vždy přiřazená oprávnění k této roli.

1. Pokud má být přiřazení trvalé (trvale oprávněné nebo trvale přiřazené), zaškrtněte políčko **trvale** .

    V závislosti na nastavení role se nemusí toto zaškrtávací políčko zobrazovat nebo může být neměnitelné.

1. Chcete-li zadat konkrétní dobu trvání přiřazení, zrušte zaškrtnutí políčka a upravte pole počáteční a/nebo koncové datum a čas.

    ![Nastavení členství – datum a čas](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Jakmile budete hotovi, klikněte na **Hotovo**.

    ![Nové přiřazení – přidat](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Chcete-li vytvořit nové přiřazení role, klikněte na tlačítko **Přidat**. Zobrazí se oznámení o stavu.

    ![Nové přiřazení – oznámení](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizovat nebo odebrat existující přiřazení role

Pomocí těchto kroků aktualizujete nebo odeberete existující přiřazení role.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **prostředky Azure**.

1. Klikněte na prostředek, který chcete spravovat, jako je například předplatné nebo skupina pro správu.

1. V části Spravovat klikněte na **role** . zobrazí se seznam rolí pro prostředky Azure.

    ![Role prostředků Azure – vybrat roli](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Klikněte na roli, kterou chcete aktualizovat nebo odebrat.

1. Vyhledejte přiřazení role na kartách **opravňující role** nebo **aktivní role** .

    ![Aktualizovat nebo odebrat přiřazení role](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Kliknutím na **aktualizovat** nebo **Odebrat** můžete aktualizovat nebo odebrat přiřazení role.

    Informace o rozšíření přiřazení role najdete v tématu [rozšíření a obnovení rolí prostředků Azure v PIM](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Další postup

- [Rozšiřování nebo obnovení rolí prostředků Azure v PIM](pim-resource-roles-renew-extend.md)
- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)
- [Přiřazení rolí Azure AD v PIM](pim-how-to-add-role-to-user.md)
