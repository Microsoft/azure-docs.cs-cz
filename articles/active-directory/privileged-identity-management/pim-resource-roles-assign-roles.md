---
title: Přiřazení rolí prostředků Azure v Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Naučte se přiřazovat role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 011b50449f0a02b128bc000535f93e1809fd9777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673878"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Přiřazení rolí prostředků Azure v Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) může spravovat předdefinované role prostředků Azure i vlastní role, včetně (ale ne omezení):

- Vlastník
- Správce uživatelských přístupů
- Přispěvatel
- Správce zabezpečení
- Správce zabezpečení

> [!NOTE]
> Uživatelé nebo členové skupiny, kteří jsou přiřazeni k rolím předplatného vlastník nebo správce přístupu uživatelů a globálním správcům služby Azure AD, kteří umožňují správu předplatného ve službě Azure AD, mají ve výchozím nastavení oprávnění správce prostředků. Tito správci můžou přiřadit role, konfigurovat nastavení rolí a kontrolovat přístup pomocí Privileged Identity Management pro prostředky Azure. Uživatel nemůže spravovat Privileged Identity Management pro prostředky bez oprávnění správce prostředků. Prohlédněte si seznam [předdefinovaných rolí Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Přiřazení role

Pomocí těchto kroků můžete uživateli poskytnout oprávnění k roli prostředku Azure.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s oprávněním vlastníka nebo role správce přístupu uživatele.

    Informace o tom, jak udělit přístup jinému správci ke správě Privileged Identity Management, najdete v tématu [udělení přístupu jiným správcům pro správu Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **prostředky Azure**.

1. Pomocí filtru prostředků Najděte spravované prostředky, které hledáte.

    ![Seznam prostředků Azure, které se mají spravovat](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Vyberte prostředek, který chcete spravovat, a otevřete stránku Přehled prostředků.

1. V části **Spravovat** vyberte **role** , abyste viděli seznam rolí pro prostředky Azure.

    ![Role prostředků Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Výběrem **Přidat přiřazení** otevřete podokno **Přidat přiřazení** .

1. Vyberte **Vybrat roli** a otevřete stránku **Vybrat roli** .

    ![Podokno nového přiřazení](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Vyberte roli, kterou chcete přiřadit, a potom klikněte na **Vybrat**.

    Otevře se podokno **Vybrat člena nebo skupinu** .

1. Vyberte člena nebo skupinu, které chcete přiřadit k roli, a potom klikněte na **Vybrat**.

    ![Vybrat člena nebo podokno skupiny](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. Na kartě **Nastavení** v seznamu **Typ přiřazení** vyberte možnost **oprávněné** nebo **aktivní**.

    ![Podokno nastavení členství](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privileged Identity Management pro prostředky Azure nabízí dva odlišné typy přiřazení:

    - **Způsobilá** přiřazení vyžadují, aby člen role prováděl akci pro použití role. Akce můžou zahrnovat provádění kontroly vícefaktorového ověřování (MFA), poskytování obchodního odůvodnění nebo žádosti o schválení od určených schvalovatelů.

    - **Aktivní** přiřazení nevyžadují, aby člen prováděl žádnou akci pro použití role. Členové přiřazení jako aktivní mají vždy přiřazená oprávnění k této roli.

1. Chcete-li zadat konkrétní dobu trvání přiřazení, změňte počáteční a koncové datum a čas.

1. Po dokončení vyberte **přiřadit**.

1. Po vytvoření nového přiřazení role se zobrazí oznámení o stavu.

    ![Nové přiřazení – oznámení](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizovat nebo odebrat existující přiřazení role

Pomocí těchto kroků aktualizujete nebo odeberete existující přiřazení role.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **prostředky Azure**.

1. Vyberte prostředek, který chcete spravovat, a otevřete jeho stránku Přehled.

1. V části **Spravovat** vyberte **role** , abyste viděli seznam rolí pro prostředky Azure.

    ![Role prostředků Azure – vybrat roli](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Vyberte roli, kterou chcete aktualizovat nebo odebrat.

1. Vyhledejte přiřazení role na kartách **opravňující role** nebo **aktivní role** .

    ![Aktualizovat nebo odebrat přiřazení role](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Pokud chcete aktualizovat nebo odebrat přiřazení role, vyberte **aktualizovat** nebo **Odebrat** .

    Informace o rozšíření přiřazení role najdete v tématu [rozšíření nebo obnovení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Další kroky

- [Rozšiřování nebo obnovení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Konfigurace nastavení role prostředků Azure v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
