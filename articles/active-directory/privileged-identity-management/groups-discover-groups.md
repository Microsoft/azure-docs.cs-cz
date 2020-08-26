---
title: Správa rolí – přiřazení skupin jako privilegovaných přístupových skupin – Azure AD | Microsoft Docs
description: Vyjádření souhlasu se skupinami přiřazovatelné role pro správu jako privilegovaných skupin přístupu v Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/17/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: a707c66f2562fe442c58ce6292e51c4e67a20ae2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869523"
---
# <a name="manage-privileged-access-groups-preview-in-privileged-identity-management"></a>Správa privilegovaných přístupových skupin (Preview) v Privileged Identity Management

V Azure Active Directory (Azure AD) můžete přiřazovat předdefinované role Azure AD do skupin cloudu a zjednodušit tak správu přiřazení rolí. K ochraně rolí Azure AD a zabezpečení přístupu teď můžete pomocí Privileged Identity Management (PIM) spravovat přístup za běhu pro členy nebo vlastníky těchto skupin. Pokud chcete spravovat roli Azure AD – přiřadit skupinu jako privilegovaný přístupovou skupinu v Privileged Identity Management, je nutné ji přenést pod správu PIM.

## <a name="identify-groups-to-manage"></a>Určení skupin pro správu

Ve službě Azure AD můžete vytvořit skupinu s přiřazením rolí, jak je popsáno v tématu [Vytvoření skupiny s přiřazením rolí v Azure Active Directory](../users-groups-roles/roles-groups-create-eligible.md). Musíte být vlastníkem skupiny, aby ji bylo možné spravovat Privileged Identity Management.

1. [Přihlaste se k Azure AD](https://aad.portal.azure.com) s oprávněními správce privilegované role.
1. Vyberte **skupiny** a pak vyberte skupinu s přiřazením role, kterou chcete spravovat. Seznam můžete vyhledat nebo filtrovat.

    ![vyhledání skupiny s možností přiřazení rolím ke správě v PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Otevřete skupinu a vyberte **privilegovaný přístup (Preview)**.

    ![Otevření Privileged Identity Managementho prostředí](./media/groups-discover-groups/groups-discover-groups.png)

1. Pokud se vaše skupiny ještě nedostaly do správy v PIM, vyberte **Povolit privilegovaný přístup** pro souhlas se správou. Tento souhlas může udělit pouze globální správce nebo vlastník skupiny.

    ![souhlas se správou skupiny v Privileged Identity Management v případě potřeby](./media/groups-discover-groups/consent-page.png)

1. Zahájení správy přiřazení v PIM

    ![Správa přiřazení v Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Po správě privilegovaného přístupového ovládacího skupin ho nejde spravovat. Tím se zabrání jinému správci v odebrání nastavení Privileged Identity Management.

## <a name="next-steps"></a>Další kroky

- [Konfigurace přiřazení privilegovaných skupin přístupu v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Přiřazení privilegovaných skupin přístupu v Privileged Identity Management](pim-resource-roles-assign-roles.md)
