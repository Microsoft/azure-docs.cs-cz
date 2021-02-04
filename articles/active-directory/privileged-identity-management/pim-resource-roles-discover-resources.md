---
title: Zjištění prostředků Azure pro správu v PIM – Azure AD | Microsoft Docs
description: Naučte se zjišťovat prostředky Azure pro správu v Azure AD Privileged Identity Management (PIM).
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
ms.date: 09/29/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d38990289169412f06b0c7e4bcbdf67f688da7
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539005"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Zjištění prostředků Azure pro správu v Privileged Identity Management

Pomocí služby Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete zlepšit ochranu vašich prostředků Azure. To je užitečné pro:

- Organizace, které už používají Privileged Identity Management k ochraně rolí Azure AD
- Skupina pro správu a vlastníci předplatného, kteří se pokoušejí zabezpečit produkční prostředky

Když nakonfigurujete Privileged Identity Management pro prostředky Azure, je potřeba vyhledat a vybrat prostředky, které chcete chránit pomocí Privileged Identity Management. Neexistuje žádné omezení počtu prostředků, které můžete spravovat pomocí Privileged Identity Management. Doporučujeme však začít s nejdůležitějšími provozními prostředky.

## <a name="discover-resources"></a>Zjišťování prostředků

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **prostředky Azure**.

    Pokud Privileged Identity Management pro prostředky Azure používáte poprvé, zobrazí se stránka **zjistit prostředky** .

    ![Vyhledat podokno prostředků bez prostředků uvedených v prvním časovém intervalu](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Pokud už jiný správce ve vaší organizaci spravuje prostředky Azure v Privileged Identity Management, zobrazí se seznam prostředků, které se v tuto chvíli spravují.

    ![V podokně vyhledat prostředky výpis aktuálně spravovaných prostředků](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Výběrem možnosti **Vyhledat prostředky** spusťte zjišťování.

    ![Podokno zjišťování obsahuje seznam prostředků, které se dají spravovat, jako jsou předplatná a skupiny pro správu.](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Na stránce **zjišťování** použijte **Filtr stavu prostředku** a **Vyberte typ prostředku** pro filtrování skupin pro správu nebo odběrů, ke kterým máte oprávnění k zápisu. Je pravděpodobné, že začne začít se **všemi** počátečními.

   Můžete vyhledat a vybrat skupinu pro správu nebo prostředky předplatného, které chcete spravovat v Privileged Identity Management. Když spravujete skupinu pro správu nebo předplatné v Privileged Identity Management, můžete také spravovat její podřízené prostředky.

   > [!Note]
   > Když přidáte nový podřízený prostředek Azure do skupiny pro správu spravovaná pomocí PIM, můžete podřízený prostředek přenést do správy tak, že ho vyhledáte v PIM.

1. Vyberte všechny nespravované prostředky, které chcete spravovat.

1. Vyberte **Spravovat prostředek** a začněte spravovat vybrané prostředky.

    > [!NOTE]
    > Po správě skupiny pro správu nebo předplatného je nepůjde nespravované. Tím se zabrání jinému správci prostředků v odebrání nastavení Privileged Identity Management.

    ![Podokno zjišťování se zvýrazněným prostředkem a vybranou možností spravovat prostředek](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Pokud se zobrazí zpráva, abyste potvrdili připojování vybraného prostředku ke správě, vyberte **Ano**. PIM se pak nakonfiguruje tak, aby spravoval všechny nové a existující podřízené objekty v rámci prostředků.

    ![Zpráva potvrzující, že se má připojit vybrané prostředky pro správu](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role prostředků Azure v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Přiřazení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-assign-roles.md)
