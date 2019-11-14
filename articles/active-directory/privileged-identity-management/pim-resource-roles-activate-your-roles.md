---
title: Aktivace rolí prostředků Azure v PIM – Azure AD | Microsoft Docs
description: Naučte se aktivovat role prostředků Azure v Azure AD Privileged Identity Management (PIM).
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
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35c81f7bb478d91bd207327ea37c80aa1778142
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023151"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Aktivovat moje role prostředků Azure v Privileged Identity Management

Použijte Privileged Identity Management (PIM) a umožněte oprávněným členům role pro prostředky Azure naplánovat aktivaci pro budoucí datum a čas. Můžou také vybrat konkrétní dobu trvání aktivace v rámci maximálního počtu (nakonfigurovaného správci).

Tento článek je určen pro členy, kteří potřebují aktivovat svoji roli prostředků Azure v Privileged Identity Management.

## <a name="activate-a-role"></a>Aktivace role

Pokud potřebujete převzít roli prostředku Azure, můžete požádat o aktivaci pomocí možnosti navigace **Moje role** v Privileged Identity Management.

1. Přihlásit se na [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**. Informace o tom, jak přidat dlaždici Privileged Identity Management do řídicího panelu, najdete v tématu [Začínáme používat Privileged Identity Management](pim-getting-started.md).

1. Vyberte **Moje role**.

    ![Stránka Moje role se zobrazenými rolemi, které můžete aktivovat](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Vyberte **role prostředků Azure** , abyste viděli seznam vašich oprávněných rolí prostředků Azure.

   ![Moje role – stránka role prostředků Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. V seznamu **role prostředků Azure** Najděte roli, kterou chcete aktivovat.

    ![Role prostředků Azure – seznam oprávněných rolí](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Výběrem **aktivovat** otevřete podokno aktivace.

1. Pokud vaše role vyžaduje službu Multi-Factor Authentication, vyberte **před pokračováním ověřit identitu**. Stačí provést ověření pouze jednou za každou relaci.

    ![Před aktivací role ověřte identitu pomocí vícefaktorového ověřování.](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Vyberte **ověřit identitu** a postupujte podle pokynů a poskytněte další ověření zabezpečení.

    ![Obrazovka k poskytnutí ověření zabezpečení, jako je kód PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Pokud chcete zadat omezený rozsah, vyberte **Rozsah** a otevřete tak podokno filtr prostředků.

    Osvědčeným postupem je vyžadovat jenom přístup k potřebným prostředkům. V podokně filtr prostředků můžete zadat skupiny prostředků nebo prostředky, ke kterým potřebujete přístup.

    ![Aktivovat – podokno filtru prostředků k určení oboru](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. V případě potřeby zadejte vlastní čas zahájení aktivace. Člen se aktivuje po zvoleném čase.

1. Do pole **důvod** zadejte důvod žádosti o aktivaci.

    ![Dokončená aktivace podokna s oborem, časem spuštění, dobou trvání a důvodem](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Vyberte **aktivovat**.

    Pokud role nevyžaduje schválení, je aktivována a přidána do seznamu aktivních rolí. Chcete-li použít roli, postupujte podle pokynů v následující části.

    Pokud [role vyžaduje schválení](pim-resource-roles-approval-workflow.md) , v pravém horním rohu prohlížeče se zobrazí oznámení o tom, že žádost čeká na schválení.

    ![Žádost o aktivaci čeká na oznámení o schválení.](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Použití role ihned po aktivaci

V případě jakékoli prodlevy po aktivaci proveďte tyto kroky po aktivaci pro okamžité použití rolí prostředků Azure.

1. Otevřete Azure AD Privileged Identity Management.

1. Výběrem **Moje role** zobrazíte seznam oprávněných rolí Azure AD a rolí prostředků Azure.

1. Vyberte **role prostředků Azure**.

1. Vyberte kartu **aktivní role** .

1. Jakmile je role aktivní, odhlaste se z portálu a znovu se přihlaste.

    Role by teď měla být dostupná pro použití.

## <a name="view-the-status-of-your-requests"></a>Zobrazení stavu vašich žádostí

Stav vašich nevyřízených žádostí můžete zobrazit a aktivovat.

1. Otevřete Azure AD Privileged Identity Management.

1. Výběrem **Moje žádosti** zobrazíte seznam vašich rolí Azure AD a žádostí o role prostředků Azure.

    ![Moje žádosti – stránka prostředků Azure zobrazující vaše nedokončené žádosti](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Posunutím doprava zobrazíte sloupec **stav žádosti** .

## <a name="cancel-a-pending-request"></a>Zrušení žádosti, která čeká na vyřízení

Pokud nepotřebujete aktivovat roli, která vyžaduje schválení, můžete žádost kdykoli zrušit.

1. Otevřete Azure AD Privileged Identity Management.

1. Vyberte **Moje žádosti**.

1. U role, kterou chcete zrušit, vyberte odkaz **Zrušit** .

    Když vyberete zrušit, požadavek se zruší. Chcete-li znovu aktivovat roli, budete muset Odeslat novou žádost o aktivaci.

   ![Seznam mých žádostí s zvýrazněnou akcí zrušit](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Řešení potíží

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Po aktivaci role nedojde k udělení oprávnění

Když v Privileged Identity Management aktivujete roli, aktivace se nemusí okamžitě rozšířit na všechny portály, které vyžadují privilegovanou roli. Někdy se může stát, že i přes rozšíření změny může kvůli webovému ukládání do mezipaměti na portálu dojít k tomu, že se změna neuplatní okamžitě. Pokud je vaše aktivace zpožděná, tady je to, co byste měli dělat.

1. Odhlaste se z webu Azure Portal a pak se znovu přihlaste.

    Při aktivaci role prostředku Azure se zobrazí fáze aktivace. Po dokončení všech fází se zobrazí odkaz **Odhlásit se**. Tento odkaz můžete použít k odhlášení. Tato akce vyřeší většinu případů zpoždění aktivace.

1. V Privileged Identity Management ověřte, že jste uvedeni jako člen role.

## <a name="next-steps"></a>Další kroky

- [Rozšiřování nebo obnovení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Aktivace mých rolí Azure AD v Privileged Identity Management](pim-how-to-activate-role.md)
