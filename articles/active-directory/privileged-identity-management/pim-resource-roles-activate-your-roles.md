---
title: Aktivovat Moje role prostředků Azure v PIM | Dokumentace Microsoftu
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
ms.component: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 59bce2c61db5838bb21a29757d4e354311ecffd5
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666243"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Aktivovat Moje role prostředků Azure v PIM

Pomocí Azure AD Privileged Identity Management (PIM), členy oprávněné role prostředků Azure můžete naplánovat aktivace pro budoucí datum a čas. Může také vybrat dobu trvání konkrétní aktivace v rámci maximální (ve Správci nakonfigurované je).

Tento článek je určená pro členy, kteří se chtějí aktivovat svoje role prostředků Azure v PIM.

## <a name="activate-a-role"></a>Aktivovat roli

Když budete potřebovat k převzetí role prostředků Azure, můžete žádost o aktivaci, s použitím **Moje role** možnost navigace v PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**. Informace o tom, jak přidat dlaždici PIM do řídicího panelu, naleznete v tématu [začít používat PIM](pim-getting-started.md).

1. Klikněte na tlačítko **Moje role** zobrazíte seznam vašich oprávněných rolí adresáře Azure AD a role prostředků Azure.

    ![Role adresáře Azure AD a role prostředků Azure – Moje role](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. V **role prostředků Azure** seznam, vyhledání role, kterou chcete aktivovat.

    ![Role prostředků Azure – Moje seznam rolí](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Klikněte na tlačítko **aktivovat** a otevřete tak podokno aktivovat.

1. Pokud vaše role se vyžaduje vícefaktorové ověřování (MFA), klikněte na tlačítko **ověřte nejdříve svoji identitu**. Stačí jenom jednou za relace ověřování.

    ![Ověření pomocí vícefaktorového ověřování před aktivaci role](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Klikněte na tlačítko **ověřit moji identitu** a postupujte podle pokynů a poskytují dodatečné ověření zabezpečení.

    ![Další ověření zabezpečení](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Pokud chcete určit obor nižší, klikněte na tlačítko **oboru** a otevřete tak podokno filtru prostředků.

    Je osvědčeným postupem je pouze žádat o přístup k prostředkům, které potřebujete. V podokně filtru prostředků můžete zadat skupiny prostředků nebo prostředky, které potřebují přístup k.

    ![Aktivovat - filtr prostředků](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. V případě potřeby zadejte čas zahájení vlastní aktivace. Člen by byl aktivován po vybraném čase.

1. V **důvod** zadejte důvod pro žádost o aktivaci.

    ![Dokončené aktivovat podokno](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Klikněte na tlačítko **aktivovat**.

    Pokud roli nevyžaduje schválení, se teď aktivuje a role se zobrazí v seznamu aktivních rolí. Pokud [role vyžaduje schválení](pim-resource-roles-approval-workflow.md) k aktivaci, zobrazí se oznámení v pravém horním rohu prohlížeče informování, žádost čeká na schválení.

    ![Žádost čeká na oznámení](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Zobrazit stav vašich požadavků

Můžete zobrazit stav vaší žádosti čekající na aktivaci.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **Moje žádosti** vyžádá seznam role adresáře Azure AD a role prostředků Azure.

    ![Role adresáře Azure AD a role prostředků Azure – Moje žádosti](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Posuňte doprava, abyste viděli **stav žádosti o** sloupce.

## <a name="use-a-role-immediately-after-activation"></a>Použijte roli okamžitě po aktivaci

Z důvodu ukládání do mezipaměti, počet aktivací nedojde okamžitě na webu Azure Portal bez její aktualizaci. Pokud potřebujete omezit možnost zpoždění až po dokončení aktivace rolí, můžete použít **přístupu k aplikacím** stránky na portálu. Nové přiřazení role aplikace k němu přistupovat z této stránky vyhledat okamžitě.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **přístupu k aplikacím** stránky.

    ![Přístup k aplikaci PIM – snímek obrazovky](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Klikněte na tlačítko **prostředky Azure** znovu otevřít na portálu na **všechny prostředky** stránky.

    Po kliknutí na tento odkaz můžete vynutit aktualizaci a je vyhledat nové přiřazení role prostředků Azure.

## <a name="cancel-a-pending-request"></a>Zrušení čekající žádosti o

Pokud nechcete, aby aktivace role, která vyžaduje schválení, můžete kdykoli zrušit čekající žádosti.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **Moje žádosti**.

1. Pro roli, kterou chcete zrušit, klikněte na tlačítko **zrušit** odkaz.

    Po klepnutí na tlačítko Storno, požadavek bude zrušen. Chcete-li aktivovat roli znovu, budete muset odeslat novou žádost o aktivaci.

   ![Zrušení čekajícího požadavku](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="next-steps"></a>Další postup

- [Rozšíření nebo obnovení role prostředků Azure v PIM](pim-resource-roles-renew-extend.md)
- [Aktivovat Moje role adresáře Azure AD v PIM](pim-how-to-activate-role.md)