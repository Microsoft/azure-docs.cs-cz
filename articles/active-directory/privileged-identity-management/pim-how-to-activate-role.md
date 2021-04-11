---
title: Aktivace mých rolí Azure AD v Azure Active Directory PIM | Microsoft Docs
description: Naučte se aktivovat role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 03/22/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa9861dd1ffbdd33f59abe76c7ebbd1befe3e7a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799424"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktivace mých rolí Azure AD v PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) zjednodušuje způsob, jakým podniky spravují privilegovaný přístup k prostředkům v Azure AD a dalším online služby Microsoftu, jako je Microsoft 365 nebo Microsoft Intune.  

Pokud jste nastavili *nárok* na roli správce, musíte přiřazení role *aktivovat* , když potřebujete provést privilegované akce. Pokud například příležitostně spravujete Microsoft 365 funkce, správci privilegovaných rolí vaší organizace nemusí mít trvalého globálního správce, protože tato role má dopad i na jiné služby. Místo toho by vám způsobilo nárok na role Azure AD, jako je například správce Exchange Online. Můžete požádat o aktivaci této role, když budete potřebovat její oprávnění, a pak budete mít kontrolu nad tím, jak bude mít správce na předstanovenou dobu.

Tento článek je určen pro správce, kteří potřebují aktivovat roli Azure AD v Privileged Identity Management.

## <a name="determine-your-version-of-pim"></a>Určení vaší verze PIM

Od listopadu 2019 se v části Privileged Identity Management role Azure AD aktualizuje na novou verzi, která se shoduje s prostředími pro role prostředků Azure. Tím se vytvoří další funkce a také [změny stávajícího rozhraní API](azure-ad-roles-features.md#api-changes). I když je nová verze zahrnuta, postupy, které provedete v tomto článku, závisí na verzi Privileged Identity Management, kterou máte v současnosti k dispozici. Podle pokynů v této části určete, kterou verzi Privileged Identity Management máte. Po zjištění vaší verze Privileged Identity Management můžete vybrat postupy v tomto článku, které odpovídají této verzi.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí role [správce privilegované role](../roles/permissions-reference.md#privileged-role-administrator) .
1. Otevřete **Azure AD Privileged Identity Management**. Pokud máte banner v horní části stránky s přehledem, postupujte podle pokynů na kartě **Nová verze** v tomto článku. Jinak postupujte podle pokynů na kartě **předchozí verze** .

    [![Vyberte Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nová verze](#tab/new)

## <a name="activate-a-role-for-new-version"></a>Aktivovat roli pro novou verzi

Pokud potřebujete převzít roli Azure AD, můžete požádat o aktivaci otevřením **Moje role** v Privileged Identity Management.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**. Informace o tom, jak přidat dlaždici Privileged Identity Management do řídicího panelu, najdete v tématu [Začínáme používat Privileged Identity Management](pim-getting-started.md).

1. Vyberte **Moje role** a potom vyberte **role Azure AD** , abyste viděli seznam oprávněných rolí Azure AD.

    ![Stránka Moje role se zobrazenými rolemi, které můžete aktivovat](./media/pim-how-to-activate-role/my-roles.png)

1. V seznamu **role Azure AD** Najděte roli, kterou chcete aktivovat.

    ![Role Azure AD – seznam oprávněných rolí](./media/pim-how-to-activate-role/activate-link.png)

1. Výběrem **aktivovat** otevřete podokno aktivace.

    ![Role Azure AD – stránka Aktivace obsahuje dobu trvání a rozsah.](./media/pim-how-to-activate-role/activate-page.png)

1. Vyberte **Další vyžadováno ověření*** * a postupujte podle pokynů pro zajištění dalšího ověření zabezpečení. Pro každou relaci se vyžaduje ověřování jenom jednou.

    ![Obrazovka k poskytnutí ověření zabezpečení, jako je kód PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Po ověření Multi-Factor Authentication vyberte **před pokračováním možnost aktivovat**.

    ![Před aktivací role ověřte identitu s MFA.](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. Pokud chcete zadat omezený rozsah, vyberte **Rozsah** a otevřete tak podokno filtru. V podokně filtru můžete zadat prostředky Azure AD, ke kterým potřebujete přístup. Osvědčeným postupem je požádat o přístup k nejmenším prostředkům, které potřebujete.

1. V případě potřeby zadejte vlastní čas zahájení aktivace. Role Azure AD by se aktivovala po zvoleném čase.

1. Do pole **důvod** zadejte důvod žádosti o aktivaci.

1. Vyberte **aktivovat**.

    Pokud [role vyžaduje schválení](pim-resource-roles-approval-workflow.md) , v pravém horním rohu prohlížeče se zobrazí oznámení o tom, že žádost čeká na schválení.

    ![Žádost o aktivaci čeká na oznámení o schválení.](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests-for-new-version"></a>Zobrazit stav vašich žádostí o novou verzi

Stav vašich nevyřízených žádostí můžete zobrazit a aktivovat.

1. Otevřete Azure AD Privileged Identity Management.

1. Výběrem **Moje žádosti** zobrazíte seznam vašich rolí Azure AD a žádostí o role prostředků Azure.

    ![Moje žádosti – stránka Azure AD zobrazující vaše nevyřízené žádosti](./media/pim-how-to-activate-role/my-requests-page.png)

1. Posunutím doprava zobrazíte sloupec **stav žádosti** .

## <a name="cancel-a-pending-request-for-new-version"></a>Zrušení žádosti o novou verzi, která čeká na vyřízení

Pokud nepotřebujete aktivovat roli, která vyžaduje schválení, můžete žádost kdykoli zrušit.

1. Otevřete Azure AD Privileged Identity Management.

1. Vyberte **Moje žádosti**.

1. U role, kterou chcete zrušit, vyberte odkaz **Zrušit** .

    Když vyberete zrušit, požadavek se zruší. Chcete-li znovu aktivovat roli, budete muset Odeslat novou žádost o aktivaci.

   ![Seznam mých žádostí s zvýrazněnou akcí zrušit](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-for-new-version"></a>Řešení potíží s novou verzí

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Po aktivaci role nedojde k udělení oprávnění

Když v Privileged Identity Management aktivujete roli, aktivace se nemusí okamžitě rozšířit na všechny portály, které vyžadují privilegovanou roli. Někdy se může stát, že i přes rozšíření změny může kvůli webovému ukládání do mezipaměti na portálu dojít k tomu, že se změna neuplatní okamžitě. Pokud je vaše aktivace zpožděná, odhlaste se z portálu, který se pokoušíte provést, a pak se znovu přihlaste. V Azure Portal vás PIM odhlásí a vrátí se automaticky.

# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

## <a name="activate-a-role-previous-version"></a>Aktivace role (předchozí verze)

Pokud potřebujete převzít roli Azure AD, můžete požádat o aktivaci pomocí možnosti navigace **Moje role** v Privileged Identity Management.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**. Informace o tom, jak přidat dlaždici Privileged Identity Management do řídicího panelu, najdete v tématu [Začínáme používat Privileged Identity Management](pim-getting-started.md).

1. Vyberte **role Azure AD**.

1. Pokud chcete zobrazit seznam oprávněných rolí Azure AD, vyberte **Moje role** .

    ![Role Azure AD – moje role zobrazující seznam oprávněných a aktivních rolí](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Najděte roli, kterou chcete aktivovat.

    ![Role Azure AD – seznam oprávněných rolí zobrazuje odkaz aktivovat](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Výběrem **aktivovat** otevřete podokno Podrobnosti aktivace role.

1. Pokud vaše role vyžaduje službu Multi-Factor Authentication (MFA), vyberte **před pokračováním ověřit identitu**. Stačí provést ověření pouze jednou za každou relaci.

    ![Před aktivací role ověřte podokno identita s MFA.](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Vyberte **ověřit identitu** a postupujte podle pokynů a poskytněte další ověření zabezpečení.

    ![Stránka pro další ověření zabezpečení s dotazem, jak vás kontaktovat](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Kliknutím na tlačítko **aktivovat** otevřete podokno aktivace.

    ![Podokno aktivace, které určuje čas zahájení, dobu trvání, lístek a důvod](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. V případě potřeby zadejte vlastní čas zahájení aktivace.

1. Zadejte dobu trvání aktivace.

1. Do pole **důvod aktivace** zadejte důvod žádosti o aktivaci. Některé role vyžadují, abyste zadali číslo lístku pro řešení problémů.

    ![Podokno aktivace bylo dokončeno s vlastním časem zahájení, dobou trvání, lístkem a důvodem.](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Vyberte **aktivovat**.

    Pokud role nevyžaduje schválení, zobrazí se podokno **stav aktivace** , ve kterém se zobrazí stav aktivace.

    ![Stránka stav aktivace zobrazující tři fáze aktivace](./media/pim-how-to-activate-role/activation-status.png)

    Po dokončení všech fází vyberte odkaz **Odhlásit** se a odhlaste se od Azure Portal. Když se znovu přihlásíte na portál, můžete teď roli použít.

    Pokud [role vyžaduje schválení](./azure-ad-pim-approval-workflow.md) , v pravém horním rohu prohlížeče se zobrazí oznámení Azure informující o tom, že žádost čeká na schválení.

## <a name="view-the-status-of-your-requests-previous-version"></a>Zobrazit stav vašich požadavků (předchozí verze)

Stav vašich nevyřízených žádostí můžete zobrazit a aktivovat.

1. Otevřete Azure AD Privileged Identity Management.

1. Vyberte **role Azure AD**.

1. Vyberte **Moje žádosti** a zobrazte seznam vašich žádostí.

    ![Role Azure AD – seznam Moje žádosti](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role-previous-version"></a>Deaktivace role (předchozí verze)

Po aktivaci role se po dosažení časového limitu (způsobilá doba trvání) automaticky deaktivuje.

Pokud dokončujete úlohy správce na začátku, můžete roli také ručně deaktivovat v Azure AD Privileged Identity Management.

1. Otevřete Azure AD Privileged Identity Management.

1. Vyberte **role Azure AD**.

1. Vyberte **Moje role**.

1. Pokud chcete zobrazit seznam aktivních rolí, vyberte **aktivní role** .

1. Najděte roli, kterou jste dokončili, a pak vyberte **deaktivovat**.

## <a name="cancel-a-pending-request-previous-version"></a>Zrušit nevyřízenou žádost (předchozí verze)

Pokud nepotřebujete aktivovat roli, která vyžaduje schválení, můžete žádost kdykoli zrušit.

1. Otevřete Azure AD Privileged Identity Management.

1. Vyberte **role Azure AD**.

1. Vyberte **Moje žádosti**.

1. U role, kterou chcete zrušit, vyberte tlačítko **Storno** .

    Když vyberete **Zrušit**, požadavek se zruší. Chcete-li znovu aktivovat roli, budete muset Odeslat novou žádost o aktivaci.

   ![Seznam Moje žádosti s zvýrazněným tlačítkem Storno](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot-previous-version"></a>Řešení potíží (předchozí verze)

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Po aktivaci role nedojde k udělení oprávnění

Při aktivaci role v Privileged Identity Management se aktivace může zpozdit na jiné portály pro správu, než na Azure Portal, jako je například portál Office 365. Pokud je vaše aktivace zpožděná, odhlaste se z portálu, ve kterém jste, a pak se znovu přihlaste. Pak použijte Privileged Identity Management k ověření, že jste uvedeni jako člen role.

 ---

## <a name="next-steps"></a>Další kroky

- [Aktivace mých rolí Azure AD v Privileged Identity Management](pim-how-to-activate-role.md)
