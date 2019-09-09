---
title: Aktivace mých rolí Azure AD v Azure Active Directory PIM | Microsoft Docs
description: Naučte se aktivovat role Azure AD v Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: f9d9b2aff1d196d8b2987d77046831e7200ee2fe
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804471"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktivace mých rolí Azure AD v PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) zjednodušuje způsob, jakým podniky spravují privilegovaný přístup k prostředkům v Azure AD a dalších online služby Microsoftu, jako je například sada Office 365 nebo Microsoft Intune.  

Pokud jste nastavili nárok na roli správce, to znamená, že tuto roli můžete aktivovat, když potřebujete provést privilegované akce. Pokud například občas spravujete funkce sady Office 365, správci privilegovaných rolí vaší organizace nemůžou mít trvalého globálního správce, protože tato role má dopad i na jiné služby. Místo toho vám budou mít nárok na role Azure AD, jako je třeba správce Exchange Online. Můžete požádat o aktivaci této role, když budete potřebovat její oprávnění, a pak budete mít kontrolu nad tím, jak bude mít správce na předstanovenou dobu.

Tento článek je určen pro správce, kteří potřebují aktivovat roli Azure AD v PIM.

## <a name="activate-a-role"></a>Aktivace role

Pokud potřebujete převzít roli Azure AD, můžete požádat o aktivaci pomocí možnosti navigace **Moje role** v PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**. Informace o tom, jak přidat dlaždici PIM na řídicí panel, najdete v tématu [Začínáme používat PIM](pim-getting-started.md).

1. Klikněte na **role Azure AD**.

1. Kliknutím na **Moje role** zobrazíte seznam oprávněných rolí Azure AD.

    ![Role Azure AD – moje role zobrazující seznam oprávněných a aktivních rolí](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Najděte roli, kterou chcete aktivovat.

    ![Role Azure AD – seznam oprávněných rolí zobrazuje odkaz aktivovat](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Kliknutím na **aktivovat** otevřete podokno Podrobnosti aktivace role.

1. Pokud vaše role vyžaduje službu Multi-Factor Authentication (MFA), **před pokračováním klikněte na ověřit vaši identitu**. Stačí provést ověření pouze jednou za každou relaci.

    ![Před aktivací role ověřte podokno identita s MFA.](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Klikněte na **ověřit identitu** a postupujte podle pokynů a poskytněte další ověření zabezpečení.

    ![Stránka pro další ověření zabezpečení s dotazem, jak vás kontaktovat](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Kliknutím na **aktivovat** otevřete podokno aktivace.

    ![Podokno aktivace, které určuje čas zahájení, dobu trvání, lístek a důvod](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. V případě potřeby zadejte vlastní čas zahájení aktivace.

1. Zadejte dobu trvání aktivace.

1. Do pole **důvod aktivace** zadejte důvod žádosti o aktivaci. Některé role vyžadují, abyste zadali číslo lístku pro řešení problémů.

    ![Podokno aktivace bylo dokončeno s vlastním časem zahájení, dobou trvání, lístkem a důvodem.](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Klikněte na tlačítko **aktivovat**.

    Pokud role nevyžaduje schválení, zobrazí se podokno **stav aktivace** , ve kterém se zobrazí stav aktivace.

    ![Stránka stav aktivace zobrazující tři fáze aktivace](./media/pim-how-to-activate-role/activation-status.png)

    Po dokončení všech fází klikněte na odkaz **Odhlásit** se a odhlaste se od Azure Portal. Když se znovu přihlásíte na portál, můžete teď roli použít.

    Pokud [role vyžaduje schválení](./azure-ad-pim-approval-workflow.md) , v pravém horním rohu prohlížeče se zobrazí oznámení o tom, že žádost čeká na schválení.

    ![Žádost o aktivaci čeká na oznámení o schválení.](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Zobrazení stavu vašich žádostí

Stav vašich nevyřízených žádostí můžete zobrazit a aktivovat.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na **role Azure AD**.

1. Kliknutím na **Moje žádosti** zobrazíte seznam vašich žádostí.

    ![Role Azure AD – seznam Moje žádosti](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Deaktivace role

Po aktivaci role se po dosažení časového limitu (způsobilá doba trvání) automaticky deaktivuje.

Pokud dokončujete úlohy správce na začátku, můžete roli také ručně deaktivovat v Azure AD Privileged Identity Management.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na **role Azure AD**.

1. Klikněte na **Moje role**.

1. Kliknutím na **aktivní role** zobrazíte seznam aktivních rolí.

1. Najděte roli, kterou jste dokončili, a potom klikněte na **deaktivovat**.

## <a name="cancel-a-pending-request"></a>Zrušení žádosti, která čeká na vyřízení

Pokud nepotřebujete aktivovat roli, která vyžaduje schválení, můžete žádost kdykoli zrušit.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na **role Azure AD**.

1. Klikněte na **Moje žádosti**.

1. Pro roli, kterou chcete zrušit, klikněte na tlačítko **Storno** .

    Když kliknete na zrušit, požadavek se zruší. Chcete-li znovu aktivovat roli, budete muset Odeslat novou žádost o aktivaci.

   ![Seznam Moje žádosti s zvýrazněným tlačítkem Storno](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Řešení potíží

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Po aktivaci role nedojde k udělení oprávnění

Když aktivujete roli v PIM, aktivace se nemusí okamžitě rozšířit na všechny portály, které vyžadují privilegovanou roli. Někdy se může stát, že i přes rozšíření změny může kvůli webovému ukládání do mezipaměti na portálu dojít k tomu, že se změna neuplatní okamžitě. Pokud je vaše aktivace zpožděná, tady je to, co byste měli dělat.

1. Odhlaste se z webu Azure Portal a pak se znovu přihlaste.

    Při aktivaci role Azure AD se zobrazí fáze aktivace. Po dokončení všech fází se zobrazí odkaz **Odhlásit se**. Pomocí tohoto odkazu se můžete odhlásit. Tím se vyřeší většina případů zpoždění aktivace.

1. V části PIM ověřte, že jste uvedeni jako člen role.

## <a name="next-steps"></a>Další kroky

- [Aktivace mých rolí prostředků Azure v PIM](pim-resource-roles-activate-your-roles.md)
