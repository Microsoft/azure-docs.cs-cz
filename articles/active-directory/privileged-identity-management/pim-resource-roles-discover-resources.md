---
title: Zjištění prostředků Azure pro správu v PIM-Azure Active Directory | Microsoft Docs
description: Naučte se zjišťovat prostředky Azure pro správu v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1f1712ab7474747829c8744a39e74b78777668
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804160"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Zjištění prostředků Azure pro správu v PIM

Pomocí služby Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete zlepšit ochranu vašich prostředků Azure. To je užitečné pro organizace, které už používají PIM k ochraně rolí Azure AD, a ke skupině pro správu a vlastníky předplatného, které hledají informace o zabezpečení produkčních prostředků.

Při nastavování PIM pro prostředky Azure je třeba zjistit a vybrat prostředky pro ochranu pomocí PIM. Neexistuje žádné omezení počtu prostředků, které můžete spravovat pomocí PIM. Doporučujeme však začít s vašimi nejdůležitějšími (provozními) prostředky.

## <a name="discover-resources"></a>Zjistit prostředky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **prostředky Azure**.

    Pokud použijete PIM pro prostředky Azure, zobrazí se podokno vyhledat prostředky.

    ![Vyhledat podokno prostředků bez prostředků uvedených v prvním časovém intervalu](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Pokud jiný správce prostředků nebo adresářů ve vaší organizaci už spravuje prostředky Azure v PIM, zobrazí se seznam prostředků, které se v tuto chvíli spravují.

    ![V podokně vyhledat prostředky výpis aktuálně spravovaných prostředků](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Kliknutím na **Vyhledat prostředky** spusťte zjišťování.

    ![Podokno zjišťování se seznamem prostředků, které se dají spravovat, jako jsou předplatná a skupiny pro správu](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. V podokně zjišťování použijte **Filtr stavu prostředku** a **Vyberte typ prostředku** pro filtrování skupin pro správu nebo odběrů, ke kterým máte oprávnění k zápisu. Je pravděpodobné, že začne začít se **všemi** počátečními.

    Můžete vyhledat a vybrat skupinu pro správu nebo prostředky předplatného, které chcete spravovat pomocí PIM. Když spravujete skupinu pro správu nebo předplatné v PIM, můžete také spravovat její podřízené prostředky.

1. Přidejte zaškrtnutí vedle všech nespravovaných prostředků, které chcete spravovat.

1. Pokud chcete začít spravovat vybrané prostředky, klikněte na **Spravovat prostředek** .

    > [!NOTE]
    > Jakmile je skupina pro správu nebo předplatné nastavené na spravované, nemůže být nespravované. Tím se zabrání dalšímu správci prostředků v odebrání nastavení PIM.

    ![Podokno zjišťování se zvýrazněným prostředkem a vybranou možností spravovat prostředek](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Pokud se zobrazí zpráva, abyste potvrdili připojování vybraného prostředku ke správě, klikněte na **Ano**.

    ![Zpráva potvrzující, že se má připojit vybrané prostředky pro správu](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)
- [Přiřazení rolí prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
