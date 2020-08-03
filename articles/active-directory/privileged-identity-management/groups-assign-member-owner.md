---
title: Přiřazení oprávněných vlastníků a členů pro privilegovaný přístup skupiny – Azure Active Directory
description: Přečtěte si, jak přiřadit oprávněným vlastníkům nebo členům skupiny přiřazení role v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1dcc98366e37455f462fe1a0740aa161201912f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506222"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Přiřazení nároku na skupinu privilegovaného přístupu (Preview) v Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vám může pomáhat spravovat způsobilost a aktivaci přiřazení k privilegovaným skupinám přístupu v Azure AD. Můžete přiřadit nárok členům nebo vlastníkům skupiny.

>[!NOTE]
>Každý uživatel, který má nárok na členství v nebo vlastnictví skupiny privilegovaný přístup, musí mít licenci Azure AD Premium P2. Další informace najdete v tématu [licenční požadavky pro použití Privileged Identity Management](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Přiřazení vlastníka nebo člena skupiny

Pomocí těchto kroků můžete uživateli nastavit, aby byl členem skupiny privilegovaných přístupových oprávnění nebo jeho vlastníkem.

1. Přihlaste se k [Privileged Identity Management](https://portal.azure.com/) v Azure Portal s oprávněními role [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Informace o tom, jak udělit přístup jinému správci ke správě Privileged Identity Management, najdete v tématu [udělení přístupu jiným správcům pro správu Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **privilegovaný přístup (Preview)**.

1. Můžete vyhledat název skupiny a pomocí **typu skupiny** vyfiltrovat seznam a vybrat skupinu, kterou chcete spravovat.

    ![Seznam privilegovaných přístupových skupin, které se mají spravovat](./media/groups-assign-member-owner/privileged-access-list.png)

1. V části **Spravovat**vyberte **přiřazení**.

1. Vyberte **Přidat přiřazení**.

    ![Podokno nového přiřazení](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Vyberte členy nebo vlastníky, které chcete nastavit jako oprávněné pro skupinu privilegovaný přístup.

    ![Vybrat člena nebo podokno skupiny](./media/groups-assign-member-owner/add-assignments.png)

1. Výběrem **Další** nastavte dobu trvání členství nebo vlastnictví.

    ![Vybrat člena nebo podokno skupiny](./media/groups-assign-member-owner/assignment-duration.png)

1. V seznamu **Typ přiřazení** vyberte možnost **oprávněné** nebo **aktivní**. Skupiny privilegovaného přístupu poskytují dva odlišné typy přiřazení:

    - **Způsobilá** přiřazení vyžadují, aby člen role prováděl akci pro použití role. Akce můžou zahrnovat provádění kontroly vícefaktorového ověřování (MFA), poskytování obchodního odůvodnění nebo žádosti o schválení od určených schvalovatelů.

    - **Aktivní** přiřazení nevyžadují, aby člen prováděl žádnou akci pro použití role. Členové přiřazení jako aktivní mají vždy přiřazená oprávnění k této roli.

1. Pokud má být přiřazení trvalé (trvale oprávněné nebo trvale přiřazené), zaškrtněte políčko **trvale** . V závislosti na nastaveních vaší organizace se nemusí toto zaškrtávací políčko zobrazovat nebo nemusí být editovatelné.

1. Po dokončení vyberte **přiřadit**.

1. Chcete-li vytvořit nové přiřazení role, vyberte **Přidat**. Zobrazí se oznámení o stavu.

    ![Nové přiřazení – oznámení](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizovat nebo odebrat existující přiřazení role

Pomocí těchto kroků aktualizujete nebo odeberete existující přiřazení role.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **privilegovaný přístup (Preview)**.

1. Můžete vyhledat název skupiny a pomocí **typu skupiny** vyfiltrovat seznam a vybrat skupinu, kterou chcete spravovat.

    ![Seznam privilegovaných přístupových skupin, které se mají spravovat](./media/groups-assign-member-owner/privileged-access-list.png)

1. V části **Spravovat**vyberte **přiřazení**.

1. Vyberte roli, kterou chcete aktualizovat nebo odebrat.

1. Vyhledejte přiřazení role na kartách **opravňující role** nebo **aktivní role** .

    ![Aktualizovat nebo odebrat přiřazení role](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Pokud chcete aktualizovat nebo odebrat přiřazení role, vyberte **aktualizovat** nebo **Odebrat** .

    Informace o rozšíření přiřazení role najdete v tématu [rozšíření nebo obnovení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Další kroky

- [Rozšiřování nebo obnovení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Konfigurace nastavení role prostředků Azure v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
