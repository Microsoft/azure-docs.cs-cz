---
title: Aktivovat Moje role prostředků Azure v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak aktivovat role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdce060006f65f2b0fb08023066ee504578bc552
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501665"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Aktivovat Moje role prostředků Azure v PIM

Pomocí Azure Active Directory (Azure AD) Privileged Identity Management (PIM), členové role vhodné pro prostředky Azure můžete naplánovat aktivace pro budoucí datum a čas. Může také vybrat dobu trvání konkrétní aktivace v rámci maximální (ve Správci nakonfigurované je).

Tento článek je určená pro členy, kteří se chtějí aktivovat svoje role prostředků Azure v PIM.

## <a name="activate-a-role"></a>Aktivovat roli

Když budete potřebovat k převzetí role prostředků Azure, můžete žádost o aktivaci, s použitím **Moje role** možnost navigace v PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**. Informace o tom, jak přidat dlaždici PIM do řídicího panelu, naleznete v tématu [začít používat PIM](pim-getting-started.md).

1. Klikněte na tlačítko **Moje role**.

    ![Můžete aktivovat Moje role stránky zobrazující role](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Klikněte na tlačítko **role prostředků Azure** zobrazíte seznam vaše role oprávněné prostředků Azure.

   ![Moje role – stránka role prostředků Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. V **role prostředků Azure** seznam, vyhledání role, kterou chcete aktivovat.

    ![Role prostředků Azure – Moje seznam oprávněné role](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Klikněte na tlačítko **aktivovat** a otevřete tak podokno aktivovat.

1. Pokud vaše role se vyžaduje vícefaktorové ověřování (MFA), klikněte na tlačítko **ověřte nejdříve svoji identitu**. Stačí jenom jednou za relace ověřování.

    ![Ověřit moji identitu pomocí MFA před aktivaci role](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Klikněte na tlačítko **ověřit moji identitu** a postupujte podle pokynů a poskytují dodatečné ověření zabezpečení.

    ![Obrazovky poskytnout ověření zabezpečení, jako je kód PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Pokud chcete určit obor nižší, klikněte na tlačítko **oboru** a otevřete tak podokno filtru prostředků.

    Je osvědčeným postupem je pouze žádat o přístup k prostředkům, které potřebujete. V podokně filtru prostředků můžete zadat skupiny prostředků nebo prostředky, které potřebují přístup k.

    ![Aktivovat – podokno filtru prostředků k určení oboru](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. V případě potřeby zadejte čas zahájení vlastní aktivace. Člen by byl aktivován po vybraném čase.

1. V **důvod** zadejte důvod pro žádost o aktivaci.

    ![Dokončené aktivovat podokna s oboru, počáteční čas, doba trvání a důvod](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Klikněte na tlačítko **aktivovat**.

    Pokud roli nevyžaduje schválení, je aktivováno a přidán do seznamu aktivních rolí. Pokud chcete použít roli, postupujte podle kroků v další části.

    Pokud [role vyžaduje schválení](pim-resource-roles-approval-workflow.md) k aktivaci, zobrazí se oznámení v pravém horním rohu prohlížeče informování, žádost čeká na schválení.

    ![Žádost o aktivaci, je oznámení čeká na schválení](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Použijte roli okamžitě po aktivaci

V případě jakéhokoli zpoždění po aktivaci podle těchto kroků po aktivaci okamžitě používat vaše role prostředků Azure.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **Moje role** zobrazíte seznam vašich oprávněných rolí Azure AD a role prostředků Azure.

1. Klikněte na tlačítko **role prostředků Azure**.

1. Klikněte na tlačítko **aktivních rolí** kartu.

1. Jakmile roli je aktivní, odhlaste se z portálu a znovu nepřihlásí.

    Role by měla být nyní k dispozici pro použití.

## <a name="view-the-status-of-your-requests"></a>Zobrazit stav vašich požadavků

Můžete zobrazit stav vaší žádosti čekající na aktivaci.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **Moje žádosti** vyžádá seznam role Azure AD a role prostředků Azure.

    ![Moje žádosti – prostředek Azure stránky zobrazující vaše žádosti čekající na vyřízení](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Posuňte doprava, abyste viděli **stav žádosti o** sloupce.

## <a name="cancel-a-pending-request"></a>Zrušení čekající žádosti o

Pokud nechcete, aby aktivace role, která vyžaduje schválení, můžete kdykoli zrušit čekající žádosti.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **Moje žádosti**.

1. Pro roli, kterou chcete zrušit, klikněte na tlačítko **zrušit** odkaz.

    Po klepnutí na tlačítko Storno, požadavek bude zrušen. Chcete-li aktivovat roli znovu, budete muset odeslat novou žádost o aktivaci.

   ![Moje žádosti o seznam s zrušit akci zvýrazní](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Řešení potíží

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Nejsou udělena oprávnění po aktivaci role

Při aktivaci role v PIM aktivace nemůže okamžitě rozšíří do všech portály, které vyžadují privilegovaných rolí. V některých případech i v případě, že je změna rozšířena, ukládání do mezipaměti webového portálu může způsobit změnu není okamžitou platností. Pokud je zpožděno s aktivací, zde je, co byste měli dělat.

1. Odhlaste se z webu Azure portal a pak zase přihlásit.

    Při aktivaci role prostředku Azure, zobrazí se v jednotlivých fázích s aktivací. Po dokončení všech fázích se zobrazí **Odhlásit** odkaz. Tento odkaz můžete odhlásit se. Tím se vyřeší většinu případů pro prodlevu aktivace.

1. V PIM ověřte, že jsou uvedeny jako člen role.

## <a name="next-steps"></a>Další postup

- [Rozšíření nebo obnovení role prostředků Azure v PIM](pim-resource-roles-renew-extend.md)
- [Aktivovat Moje role Azure AD v PIM](pim-how-to-activate-role.md)
