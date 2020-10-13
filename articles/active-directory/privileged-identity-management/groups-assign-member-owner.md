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
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48c4473cfafce1215219251c47bce1d5730645fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534416"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Přiřazení nároku na skupinu privilegovaného přístupu (Preview) v Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vám může pomáhat spravovat způsobilost a aktivaci přiřazení k privilegovaným skupinám přístupu v Azure AD. Můžete přiřadit nárok členům nebo vlastníkům skupiny.

>[!NOTE]
>Každý uživatel, který má nárok na členství v nebo vlastnictví skupiny privilegovaný přístup, musí mít licenci Azure AD Premium P2. Další informace najdete v tématu [licenční požadavky pro použití Privileged Identity Management](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Přiřazení vlastníka nebo člena skupiny

Pomocí těchto kroků můžete uživateli nastavit, aby byl členem skupiny privilegovaných přístupových oprávnění nebo jeho vlastníkem.

1. [Přihlaste se ke službě Azure AD](https://aad.portal.azure.com) s oprávněními globálního správce nebo vlastníka skupiny.
1. Vyberte **skupiny** a pak vyberte skupinu s přiřazením role, kterou chcete spravovat. Seznam můžete vyhledat nebo filtrovat.

    ![vyhledání skupiny s možností přiřazení rolím ke správě v PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Otevřete skupinu a vyberte **privilegovaný přístup (Preview)**.

    ![Otevření Privileged Identity Managementho prostředí](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Vyberte **Přidat přiřazení**.

    ![Podokno nového přiřazení](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Vyberte členy nebo vlastníky, které chcete nastavit jako oprávněné pro skupinu privilegovaný přístup.

    ![Snímek obrazovky zobrazující stránku přidat přiřazení, která obsahuje podokno vybrat člena nebo skupinu otevřené a zvýrazněné tlačítko vybrat](./media/groups-assign-member-owner/add-assignments.png)

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

1. [Přihlaste se ke službě Azure AD](https://aad.portal.azure.com) s oprávněními globálního správce nebo vlastníka skupiny.
1. Vyberte **skupiny** a pak vyberte skupinu s přiřazením role, kterou chcete spravovat. Seznam můžete vyhledat nebo filtrovat.

    ![vyhledání skupiny s možností přiřazení rolím ke správě v PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Otevřete skupinu a vyberte **privilegovaný přístup (Preview)**.

    ![Otevření Privileged Identity Managementho prostředí](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Vyberte roli, kterou chcete aktualizovat nebo odebrat.

1. Vyhledejte přiřazení role na kartách **opravňující role** nebo **aktivní role** .

    ![Aktualizovat nebo odebrat přiřazení role](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. Pokud chcete aktualizovat nebo odebrat přiřazení role, vyberte **aktualizovat** nebo **Odebrat** .

    Informace o rozšíření přiřazení role najdete v tématu [rozšíření nebo obnovení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Další kroky

- [Rozšiřování nebo obnovení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Konfigurace nastavení role prostředků Azure v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
