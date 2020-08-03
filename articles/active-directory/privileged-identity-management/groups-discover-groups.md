---
title: Zjišťování rolí – přiřazení skupin pro správu v PIM – Azure AD | Microsoft Docs
description: Naučte se zjišťovat skupiny s možností přiřazení rolí pro správu jako privilegovaných skupin přístupu v Privileged Identity Management (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b66a41f0f1aa48433dac77fca56d5e2877989a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506001"
---
# <a name="discover-privileged-access-groups-preview-to-manage-in-privileged-identity-management"></a>Seznamte se s privilegovanými přístupovými skupinami (Preview) pro správu v Privileged Identity Management

V Azure Active Directory (Azure AD) můžete přiřadit předdefinované role Azure AD ke skupinám cloudu a zjednodušit tak správu přiřazení rolí. Nyní můžete použít Privileged Identity Management (PIM) k přiřazení nároků na členství nebo vlastnictví těchto skupin, k ochraně rolí Azure AD a k zabezpečení přístupu. Než budete moct spravovat roli Azure AD – přiřadit skupinu jako privilegovaný přístupovou skupinu v Privileged Identity Management, musíte ji zjistit a přenést pod správu v PIM.

## <a name="discover-resources"></a>Zjišťování prostředků

1. [Přihlaste se k Azure AD](https://aad.portal.azure.com) s oprávněními role správce privilegovaných rolí.
1. Vytvořte skupinu s přiřazením role v Azure AD. Abyste mohli zjistit a spravovat pomocí Privileged Identity Management, musíte být vlastníkem skupiny.
1. Otevřete **Privileged Identity Management**.
1. Vyberte **privilegovaný přístup (Preview)**.

    ![Vyhledat skupiny – příkaz pro první spuštění](./media/groups-discover-groups/groups-discover-groups.png)

1. Vyberte **zjišťování skupin**.
1. Hledat podle názvu skupiny
1. Vyberte skupinu a vyberte **Spravovat skupiny** , abyste ji mohli přenést pod správu PIM.

    ![Zjišťování skupin bez prostředků uvedených v prvním časovém intervalu](./media/groups-discover-groups/groups-bring-under-management.png)

    > [!NOTE]
    > Po správě privilegovaného přístupového ovládacího skupin ho nejde spravovat. Tím se zabrání jinému správci prostředků v odebrání nastavení Privileged Identity Management.

1. Pokud se zobrazí zpráva, abyste potvrdili připojování vybraného prostředku ke správě, vyberte **Ano**.

## <a name="next-steps"></a>Další kroky

- [Konfigurace přiřazení privilegovaných skupin přístupu v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Přiřazení privilegovaných skupin přístupu v Privileged Identity Management](pim-resource-roles-assign-roles.md)
