---
title: Určení skupiny pro správu v Privileged Identity Management – Azure AD | Microsoft Docs
description: Naučte se integrovat skupiny role – přiřazení skupin pro správu jako privilegovaných přístupových skupin v Privileged Identity Management (PIM).
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
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c9e2580d2a88fbbab755f0c3df2f923bdc45548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688344"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>Převedení privilegovaných přístupových skupin (Preview) do Privileged Identity Management

V Azure Active Directory (Azure AD) můžete přiřazovat předdefinované role Azure AD do skupin cloudu a zjednodušit tak správu přiřazení rolí. K ochraně rolí Azure AD a zabezpečení přístupu teď můžete pomocí Privileged Identity Management (PIM) spravovat přístup za běhu pro členy nebo vlastníky těchto skupin. Pokud chcete spravovat roli Azure AD – přiřadit skupinu jako privilegovaný přístupovou skupinu v Privileged Identity Management, je nutné ji přenést pod správu PIM.

## <a name="identify-groups-to-manage"></a>Určení skupin pro správu

Ve službě Azure AD můžete vytvořit skupinu s přiřazením rolí, jak je popsáno v tématu [Vytvoření skupiny s přiřazením rolí v Azure Active Directory](../roles/groups-create-eligible.md). Musíte být vlastníkem skupiny, aby ji bylo možné spravovat pomocí Privileged Identity Management.

1. [Přihlaste se k Azure AD](https://aad.portal.azure.com) s oprávněními role správce privilegovaných rolí.
1. Vyberte **skupiny** a pak vyberte skupinu role, kterou chcete spravovat v PIM. Seznam můžete vyhledat a filtrovat.

    ![vyhledání skupiny s možností přiřazení rolím ke správě v PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Otevřete skupinu a vyberte **privilegovaný přístup (Preview)**.

    ![Otevření Privileged Identity Managementho prostředí](./media/groups-discover-groups/groups-discover-groups.png)

1. Zahájení správy přiřazení v PIM

    ![Správa přiřazení v Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Po správě privilegovaného přístupového ovládacího skupin ho nejde spravovat. Tím se zabrání jinému správci prostředků v odebrání nastavení Privileged Identity Management.
>

> [!IMPORTANT]
> Pokud se skupina privilegovaných přístup odstraní z Azure Active Directory, může trvat až 24 hodin, než se skupina odebere z okna Privileged Access groups (Preview). 
>


## <a name="next-steps"></a>Další kroky

- [Konfigurace přiřazení privilegovaných skupin přístupu v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Přiřazení privilegovaných skupin přístupu v Privileged Identity Management](pim-resource-roles-assign-roles.md)
