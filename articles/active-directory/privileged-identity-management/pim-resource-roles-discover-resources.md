---
title: Seznamte se s prostředky Azure ke správě v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak zjistit prostředky Azure ke správě v Azure AD Privileged Identity Management (PIM).
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
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022889"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Seznamte se s prostředky Azure, které chcete spravovat v privilegované správě identit

Pomocí Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete zlepšit ochranu prostředků Azure. To je užitečné pro organizace, které již používají privilegovanou správu identit k ochraně rolí Azure AD, a pro vlastníky skupin pro správu a předplatné, kteří chtějí zabezpečit produkční prostředky.

Při prvním nastavení privilegované správy identit pro prostředky Azure je potřeba zjistit a vybrat prostředky, které chcete chránit pomocí správy privilegovaných identit. Počet prostředků, které můžete spravovat pomocí správy privilegovaných identit, není nijak omezen. Doporučujeme však začít s nejkritičtějšími (produkčními) prostředky.

## <a name="discover-resources"></a>Zjišťování prostředků

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **prostředky Azure**.

    Pokud je to poprvé, co používáte privilegované správy identit pro prostředky Azure, zobrazí se stránka **Zjistit prostředky.**

    ![Zjišťování podokna prostředků bez prostředků uvedených při prvním prostředí](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Pokud jiný správce ve vaší organizaci už spravuje prostředky Azure ve službě Privileged Identity Management, zobrazí se seznam prostředků, které se aktuálně spravují.

    ![Zjišťování podokna prostředků se seznamem prostředků, které jsou aktuálně spravovány](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Vyberte **Zjistit prostředky** a spusťte prostředí zjišťování.

    ![Podokno zjišťování se seznamem prostředků, které lze spravovat, jako jsou předplatná a skupiny pro správu](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Na stránce **Zjišťování** použijte **filtr Stav prostředků** a **Vyberte typ prostředku** k filtrování skupin pro správu nebo odběrů, ke které máte oprávnění k zápisu. Je to asi nejjednodušší začít s **All** zpočátku.

    Můžete vyhledávat a vybírat prostředky skupiny pro správu nebo předplatné pro správu pomocí správy privilegovaných identit. Když spravujete skupinu pro správu nebo předplatné v privilegované správě identit, můžete také spravovat její podřízené prostředky.

1. Zaškrtněte políčko vedle všech nespravovaných prostředků, které chcete spravovat.

1. Chcete-li začít spravovat vybrané zdroje, vyberte **možnost Spravovat zdroj.**

    > [!NOTE]
    > Jakmile je spravovaná skupina nebo předplatné pro správu, nelze ji nespravovat. To zabrání jinému správci prostředků v odebrání nastavení správy privilegovaných identit.

    ![Podokno zjišťování s vybraným zdrojem a zvýrazněnou možností Spravovat zdroj](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Pokud se zobrazí zpráva o potvrzení registrace vybraného zdroje pro správu, vyberte **ano**.

    ![Zpráva potvrzující na palubě vybraných zdrojů pro správu](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role prostředků Azure ve správě privilegovaných identit](pim-resource-roles-configure-role-settings.md)
- [Přiřazení rolí prostředků Azure ve správě privilegovaných identit](pim-resource-roles-assign-roles.md)
