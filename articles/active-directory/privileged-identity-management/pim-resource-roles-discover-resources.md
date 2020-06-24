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
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4852f841fc9ac8ebea586d5e24967a1db20b8044
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84743707"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Zjištění prostředků Azure pro správu v Privileged Identity Management

Pomocí služby Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete zlepšit ochranu vašich prostředků Azure. To je užitečné pro organizace, které už používají Privileged Identity Management k ochraně rolí Azure AD a ke skupině pro správu a vlastníkům předplatného, které hledají informace o zabezpečení produkčních prostředků.

Když nakonfigurujete Privileged Identity Management pro prostředky Azure, je potřeba vyhledat a vybrat prostředky, které chcete chránit pomocí Privileged Identity Management. Neexistuje žádné omezení počtu prostředků, které můžete spravovat pomocí Privileged Identity Management. Doporučujeme však začít s vašimi nejdůležitějšími (provozními) prostředky.

## <a name="discover-resources"></a>Zjišťování prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **prostředky Azure**.

    Pokud Privileged Identity Management pro prostředky Azure používáte poprvé, zobrazí se stránka **zjistit prostředky** .

    ![Vyhledat podokno prostředků bez prostředků uvedených v prvním časovém intervalu](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Pokud už jiný správce ve vaší organizaci spravuje prostředky Azure v Privileged Identity Management, zobrazí se seznam prostředků, které se v tuto chvíli spravují.

    ![V podokně vyhledat prostředky výpis aktuálně spravovaných prostředků](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Výběrem možnosti **Vyhledat prostředky** spusťte zjišťování.

    ![Podokno zjišťování se seznamem prostředků, které se dají spravovat, jako jsou předplatná a skupiny pro správu](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Na stránce **zjišťování** použijte **Filtr stavu prostředku** a **Vyberte typ prostředku** pro filtrování skupin pro správu nebo odběrů, ke kterým máte oprávnění k zápisu. Je pravděpodobné, že začne začít se **všemi** počátečními.

    Můžete hledat a vybírat prostředky skupiny pro správu nebo předplatného, které se dají spravovat pomocí Privileged Identity Management. Když spravujete skupinu pro správu nebo předplatné v Privileged Identity Management, můžete také spravovat její podřízené prostředky.

1. Zaškrtněte políčko vedle všech nespravovaných prostředků, které chcete spravovat.

1. Vyberte **Spravovat prostředek** a začněte spravovat vybrané prostředky.

    > [!NOTE]
    > Po správě skupiny pro správu nebo předplatného je nepůjde nespravované. Tím se zabrání jinému správci prostředků v odebrání nastavení Privileged Identity Management.

    ![Podokno zjišťování se zvýrazněným prostředkem a vybranou možností spravovat prostředek](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Pokud se zobrazí zpráva, abyste potvrdili připojování vybraného prostředku ke správě, vyberte **Ano**.

    ![Zpráva potvrzující, že se má připojit vybrané prostředky pro správu](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role prostředků Azure v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Přiřazení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-assign-roles.md)
