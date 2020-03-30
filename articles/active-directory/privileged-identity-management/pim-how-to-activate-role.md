---
title: Aktivace rolí Azure AD v PIM – Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, jak aktivovat role Azure AD v azure ad privilegované správy identit (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f95a1a08189668e5b6f88941069566b00a73bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499222"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktivace mých rolí Azure AD v PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) zjednodušuje způsob, jakým podniky spravují privilegovaný přístup k prostředkům ve službě Azure AD a dalších online službách Microsoftu, jako je Office 365 nebo Microsoft Intune.  

Pokud jste byli pro roli správce způsobilí, znamená to, že tuto roli můžete aktivovat, když potřebujete provádět privilegované akce. Pokud například příležitostně spravujete funkce Office 365, správci privilegovaných rolí vaší organizace z vás nemusí udělat stálého globálního správce, protože tato role má dopad i na další služby. Místo toho vás učiní způsobilými pro role Azure AD, jako je správce Exchange Online. Můžete požádat o aktivaci této role, když potřebujete její oprávnění, a pak budete mít řízení správce pro předem stanovené časové období.

Tento článek je určen pro správce, kteří potřebují aktivovat svou roli Azure AD ve správě privilegovaných identit.

## <a name="determine-your-version-of-pim"></a>Určení verze PIM

Počínaje listopadem 2019 se část rolí Azure AD privileged identity Management aktualizuje na novou verzi, která odpovídá prostředí pro role prostředků Azure. Tím se vytvoří další funkce, stejně jako [změny existující rozhraní API](azure-ad-roles-features.md#api-changes). Při zavádění nové verze, které postupy, které budete postupovat v tomto článku, závisí na verzi správy privilegovaných identit, kterou aktuálně máte. Podle pokynů v této části určete, kterou verzi správy privilegovaných identit máte. Poté, co znáte verzi správy privilegovaných identit, můžete vybrat postupy v tomto článku, které odpovídají této verzi.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) s rolí [správce privilegované role.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otevřete **správu privilegovaných identit Azure AD**. Pokud máte v horní části stránky přehledu nápis, postupujte podle pokynů na kartě **Nová verze** tohoto článku. V opačném případě postupujte podle pokynů na kartě **Předchozí verze.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nová verze](#tab/new)

## <a name="activate-a-role"></a>Aktivace role

Když potřebujete převzít roli Azure AD, můžete požádat o aktivaci pomocí možnosti navigace **moje role** v privilegované správě identit.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Otevřete **správu privilegovaných identit Azure AD**. Informace o přidání dlaždice Správa privilegovaných identit na řídicí panel najdete v tématu [Začínáme používat správu privilegovaných identit](pim-getting-started.md).

1. Vyberte **Moje role**a pak vyberte **role Azure AD,** abyste zobcebovali seznam vašich způsobilých rolí Azure AD.

    ![Stránka Role zobrazující role, které můžete aktivovat](./media/pim-how-to-activate-role/my-roles.png)

1. V seznamu **rolí Azure AD** najděte roli, kterou chcete aktivovat.

    ![Role Azure AD – seznam způsobilých rolí](./media/pim-how-to-activate-role/activate-link.png)

1. Výběrem **možnosti Aktivovat** otevřete podokno Aktivovat.

    ![Role Azure AD – stránka aktivace obsahuje dobu trvání a obor](./media/pim-how-to-activate-role/activate-page.png)

1. Pokud vaše role vyžaduje vícefaktorové ověřování, vyberte **před pokračováním možnost Ověřit identitu**. Je třeba ověřit pouze jednou za relaci.

    ![Ověření identity pomocí vícefaktorové ověřování před aktivací role](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Vyberte **Ověřit moji identitu** a podle pokynů zajistěte další ověření zabezpečení.

    ![Obrazovka pro ověření zabezpečení, například KÓD PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Pokud chcete zadat omezený obor, otevřete podokno filtru výběrem **možnosti Obor.** V podokně filtrů můžete určit prostředky Azure AD, ke kterým potřebujete mít přístup. Je osvědčeným postupem požádat o přístup pouze k prostředkům, které potřebujete.

1. V případě potřeby zadejte vlastní čas zahájení aktivace. Role Azure AD by se aktivovala po vybraném čase.

1. Do pole **Důvod** zadejte důvod žádosti o aktivaci.

1. Vyberte **Aktivovat**.

    Pokud role nevyžaduje schválení, je aktivována a přidána do seznamu aktivních rolí. Pokud chcete roli použít, postupujte podle kroků v další části.

    ![Podokno Dokončeno Aktivace s rozsahem, časem zahájení, dobou trvání a důvodem](./media/pim-how-to-activate-role/azure-ad-activation-status.png)

    Pokud [role vyžaduje schválení](pim-resource-roles-approval-workflow.md) k aktivaci, zobrazí se v pravém horním rohu prohlížeče oznámení, které vás informuje, že žádost čeká na schválení.

    ![Žádost o aktivaci čeká na oznámení o schválení.](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Použití role ihned po aktivaci

V případě zpoždění po aktivaci postupujte podle těchto kroků po aktivaci okamžitě použít vaše role Azure AD.

1. Otevřete správu privilegovaných identit Azure AD.

1. Výběrem **možnosti Moje role** zobrazíte seznam způsobilých rolí Azure AD a rolí prostředků Azure.

1. Vyberte **role Azure AD**.

1. Vyberte kartu **Aktivní role.**

1. Jakmile je role aktivní, odhlaste se z portálu a znovu se přihlaste.

    Role by nyní měla být k dispozici pro použití.

## <a name="view-the-status-of-your-requests"></a>Zobrazení stavu vašich požadavků

Můžete zobrazit stav nevyřízených žádostí o aktivaci.

1. Otevřete správu privilegovaných identit Azure AD.

1. Vyberte **Moje požadavky** zobrazíte seznam vaší role Azure AD a požadavky na role prostředků Azure.

    ![Moje požadavky – stránka Azure AD zobrazující vaše čekající požadavky](./media/pim-how-to-activate-role/my-requests-page.png)

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

    Když aktivujete roli Azure AD, uvidíte fáze aktivace. Po dokončení všech fází se zobrazí odkaz **Odhlásit se**. Pomocí tohoto odkazu se můžete odhlásit. Tím se vyřeší většina případů zpoždění aktivace.

1. V části Správa privilegovaných identit ověřte, zda jste uvedeni jako člen role.

# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

## <a name="activate-a-role"></a>Aktivace role

Když potřebujete převzít roli Azure AD, můžete požádat o aktivaci pomocí možnosti navigace **moje role** v privilegované správě identit.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Otevřete **správu privilegovaných identit Azure AD**. Informace o přidání dlaždice Správa privilegovaných identit na řídicí panel najdete v tématu [Začínáme používat správu privilegovaných identit](pim-getting-started.md).

1. Klikněte na **role Azure AD**.

1. Kliknutím na **Moje role** zobrazíte seznam vhodných rolí Azure AD.

    ![Role Azure AD – moje role zobrazující seznam způsobilých nebo aktivních rolí](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Najděte roli, kterou chcete aktivovat.

    ![Role Azure AD – seznam způsobilých rolí zobrazující odkaz Aktivovat](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Kliknutím na **Aktivovat** otevřete podokno podrobností o aktivaci role.

1. Pokud vaše role vyžaduje vícefaktorové ověřování (MFA), klikněte před pokračováním na **Ověřit identitu**. Je třeba ověřit pouze jednou za relaci.

    ![Ověření podokna identity pomocí vícefaktorového faktoru před aktivací role](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Klikněte na **Ověřit moji identitu** a podle pokynů zajistěte další ověření zabezpečení.

    ![Další stránka pro ověření zabezpečení s dotazem, jak vás kontaktovat](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Klepnutím na **tlačítko Aktivovat** otevřete podokno Aktivace.

    ![Aktivační podokno pro určení času zahájení, doby trvání, lístku a důvodu](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. V případě potřeby zadejte vlastní čas zahájení aktivace.

1. Zadejte dobu trvání aktivace.

1. Do pole **Důvod aktivace** zadejte důvod žádosti o aktivaci. Některé role vyžadují zadání čísla chybovacího lístku.

    ![Podokno Dokončená aktivace s vlastním časem zahájení, dobou trvání, lístkem a důvodem](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Klepněte na **tlačítko Aktivovat**.

    Pokud role nevyžaduje schválení, zobrazí se stavové podokno **Aktivace,** které zobrazuje stav aktivace.

    ![Stránka stavu aktivace zobrazující tři fáze aktivace](./media/pim-how-to-activate-role/activation-status.png)

    Po dokončení všech fází klikněte na odkaz **Odhlásit** se a odhlaste se z webu Azure Portal. Když se přihlásíte zpět k portálu, můžete nyní použít roli.

    Pokud [role vyžaduje schválení](./azure-ad-pim-approval-workflow.md) k aktivaci, zobrazí se v pravém horním rohu prohlížeče oznámení o tom, že žádost čeká na schválení.

## <a name="view-the-status-of-your-requests"></a>Zobrazení stavu vašich požadavků

Můžete zobrazit stav nevyřízených žádostí o aktivaci.

1. Otevřete správu privilegovaných identit Azure AD.

1. Klikněte na **role Azure AD**.

1. Kliknutím na **Moje požadavky** zobrazíte seznam vašich žádostí.

    ![Role Azure AD – seznam mých požadavků](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Deaktivace role

Jakmile je role aktivována, automaticky se deaktivuje, když je dosaženo jejího časového limitu (způsobilé doby trvání).

Pokud dokončíte úlohy správce brzy, můžete také deaktivovat roli ručně v Azure AD Privilegované Správy identit.

1. Otevřete správu privilegovaných identit Azure AD.

1. Klikněte na **role Azure AD**.

1. Klikněte na **Moje role**.

1. Kliknutím na **Aktivní role** zobrazíte seznam aktivních rolí.

1. Najděte roli, kterou jste použili, a klikněte na **Deaktivovat**.

## <a name="cancel-a-pending-request"></a>Zrušení nevyřízené žádosti

Pokud nepotřebujete aktivaci role, která vyžaduje schválení, můžete žádost čekající na vyřízení kdykoli zrušit.

1. Otevřete správu privilegovaných identit Azure AD.

1. Klikněte na **role Azure AD**.

1. Klepněte na **položku Moje požadavky**.

1. U role, kterou chcete zrušit, klikněte na tlačítko **Storno.**

    Po klepnutí na tlačítko Storno bude požadavek zrušen. Chcete-li roli znovu aktivovat, budete muset odeslat novou žádost o aktivaci.

   ![Seznam mých požadavků se zvýrazněným tlačítkem Storno](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Řešení potíží

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Po aktivaci role nedojde k udělení oprávnění

Když aktivujete roli ve správě privilegovaných identit, aktivace se nemusí okamžitě šířit na všechny portály, které vyžadují privilegovanou roli. Někdy se může stát, že i přes rozšíření změny může kvůli webovému ukládání do mezipaměti na portálu dojít k tomu, že se změna neuplatní okamžitě. Pokud je aktivace zpožděna, měli byste udělat toto:

1. Odhlaste se z webu Azure Portal a pak se znovu přihlaste.

    Když aktivujete roli Azure AD, uvidíte fáze aktivace. Po dokončení všech fází se zobrazí odkaz **Odhlásit se**. Pomocí tohoto odkazu se můžete odhlásit. Tím se vyřeší většina případů zpoždění aktivace.

1. V části Správa privilegovaných identit ověřte, zda jste uvedeni jako člen role.

 ---

## <a name="next-steps"></a>Další kroky

- [Aktivace rolí Azure AD ve správě privilegovaných identit](pim-how-to-activate-role.md)
