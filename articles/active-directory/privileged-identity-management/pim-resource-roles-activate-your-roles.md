---
title: Aktivace rolí prostředků Azure v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak aktivovat role prostředků Azure v azure ad privilegované správy identit (PIM).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023151"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Aktivace rolí prostředků Azure ve správě privilegovaných identit

Pomocí správy privilegovaných identit (PIM) povolte členům způsobilých rolí pro prostředky Azure naplánovat aktivaci pro budoucí datum a čas. Mohou také vybrat konkrétní dobu aktivace v rámci maximální (nakonfigurované správci).

Tento článek je určen pro členy, kteří potřebují aktivovat svou roli prostředků Azure ve správě privilegovaných identit.

## <a name="activate-a-role"></a>Aktivace role

Když potřebujete převzít roli prostředku Azure, můžete požádat o aktivaci pomocí možnosti navigace **moje role** v privilegované správě identit.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Otevřete **správu privilegovaných identit Azure AD**. Informace o přidání dlaždice Správa privilegovaných identit na řídicí panel najdete v tématu [Začínáme používat správu privilegovaných identit](pim-getting-started.md).

1. Vyberte **možnost Moje role**.

    ![Stránka Role zobrazující role, které můžete aktivovat](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Výběrem **rolí prostředků Azure** zobrazíte seznam způsobilých rolí prostředků Azure.

   ![Moje role – stránka role prostředků Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. V seznamu **rolí prostředků Azure** najděte roli, kterou chcete aktivovat.

    ![Role prostředků Azure – seznam způsobilých rolí](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Výběrem **možnosti Aktivovat** otevřete podokno Aktivovat.

1. Pokud vaše role vyžaduje vícefaktorové ověřování, vyberte **před pokračováním možnost Ověřit identitu**. Je třeba ověřit pouze jednou za relaci.

    ![Ověření identity pomocí vícefaktorové ověřování před aktivací role](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Vyberte **Ověřit moji identitu** a podle pokynů zajistěte další ověření zabezpečení.

    ![Obrazovka pro ověření zabezpečení, například KÓD PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Pokud chcete zadat omezený obor, vyberte **Obor,** chcete-li otevřít podokno filtru prostředků.

    Je osvědčeným postupem požádat pouze o přístup k prostředkům, které potřebujete. V podokně filtru prostředků můžete určit skupiny prostředků nebo prostředky, ke kterým potřebujete mít přístup.

    ![Aktivovat podokno filtru prostředků pro určení oboru](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. V případě potřeby zadejte vlastní čas zahájení aktivace. Člen bude aktivován po vybraném čase.

1. Do pole **Důvod** zadejte důvod žádosti o aktivaci.

    ![Podokno Dokončeno Aktivace s rozsahem, časem zahájení, dobou trvání a důvodem](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Vyberte **Aktivovat**.

    Pokud role nevyžaduje schválení, je aktivována a přidána do seznamu aktivních rolí. Pokud chcete roli použít, postupujte podle kroků v další části.

    Pokud [role vyžaduje schválení](pim-resource-roles-approval-workflow.md) k aktivaci, zobrazí se v pravém horním rohu prohlížeče oznámení, které vás informuje, že žádost čeká na schválení.

    ![Žádost o aktivaci čeká na oznámení o schválení.](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Použití role ihned po aktivaci

V případě jakéhokoli zpoždění po aktivaci postupujte po aktivaci, abyste okamžitě použili role prostředků Azure.

1. Otevřete správu privilegovaných identit Azure AD.

1. Výběrem **možnosti Moje role** zobrazíte seznam způsobilých rolí Azure AD a rolí prostředků Azure.

1. Vyberte **role prostředků Azure**.

1. Vyberte kartu **Aktivní role.**

1. Jakmile je role aktivní, odhlaste se z portálu a znovu se přihlaste.

    Role by nyní měla být k dispozici pro použití.

## <a name="view-the-status-of-your-requests"></a>Zobrazení stavu vašich požadavků

Můžete zobrazit stav nevyřízených žádostí o aktivaci.

1. Otevřete správu privilegovaných identit Azure AD.

1. Vyberte **Moje požadavky** zobrazíte seznam vaší role Azure AD a požadavky na role prostředků Azure.

    ![Moje požadavky – stránka prostředků Azure zobrazující vaše čekající požadavky](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Posunutím doprava zobrazíte sloupec **Stav požadavku.**

## <a name="cancel-a-pending-request"></a>Zrušení nevyřízené žádosti

Pokud nepotřebujete aktivaci role, která vyžaduje schválení, můžete žádost čekající na vyřízení kdykoli zrušit.

1. Otevřete správu privilegovaných identit Azure AD.

1. Vyberte **možnost Moje požadavky**.

1. Chcete-li roli zrušit, vyberte odkaz **Zrušit.**

    Když vyberete Zrušit, požadavek bude zrušen. Chcete-li roli znovu aktivovat, budete muset odeslat novou žádost o aktivaci.

   ![Můj seznam žádostí se zvýrazněnou akcí Zrušit](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Řešení potíží

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Po aktivaci role nedojde k udělení oprávnění

Když aktivujete roli ve správě privilegovaných identit, aktivace se nemusí okamžitě šířit na všechny portály, které vyžadují privilegovanou roli. Někdy se může stát, že i přes rozšíření změny může kvůli webovému ukládání do mezipaměti na portálu dojít k tomu, že se změna neuplatní okamžitě. Pokud je aktivace zpožděna, měli byste udělat toto:

1. Odhlaste se z webu Azure Portal a pak se znovu přihlaste.

    Když aktivujete roli prostředku Azure, uvidíte fáze aktivace. Po dokončení všech fází se zobrazí odkaz **Odhlásit se**. Pomocí tohoto odkazu se můžete odhlásit. Tím se vyřeší většina případů zpoždění aktivace.

1. V části Správa privilegovaných identit ověřte, zda jste uvedeni jako člen role.

## <a name="next-steps"></a>Další kroky

- [Rozšíření nebo obnovení rolí prostředků Azure ve správě privilegovaných identit](pim-resource-roles-renew-extend.md)
- [Aktivace rolí Azure AD ve správě privilegovaných identit](pim-how-to-activate-role.md)
