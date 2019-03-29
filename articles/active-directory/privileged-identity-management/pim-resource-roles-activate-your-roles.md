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
ms.date: 03/05/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23fcba94a8f29d0f1bc458dd4779e5a2f0c06f38
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58575757"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Aktivovat Moje role prostředků Azure v PIM

Pomocí Azure Active Directory (Azure AD) Privileged Identity Management (PIM), členové role vhodné pro prostředky Azure můžete naplánovat aktivace pro budoucí datum a čas. Může také vybrat dobu trvání konkrétní aktivace v rámci maximální (ve Správci nakonfigurované je).

Tento článek je určená pro členy, kteří se chtějí aktivovat svoje role prostředků Azure v PIM.

## <a name="activate-a-role"></a>Aktivovat roli

Když budete potřebovat k převzetí role prostředků Azure, můžete žádost o aktivaci, s použitím **Moje role** možnost navigace v PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**. Informace o tom, jak přidat dlaždici PIM do řídicího panelu, naleznete v tématu [začít používat PIM](pim-getting-started.md).

1. Klikněte na tlačítko **Moje role**.

    ![Role Azure AD a role prostředků Azure – Moje role](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Klikněte na tlačítko **role prostředků Azure** zobrazíte seznam vaše role oprávněné prostředků Azure.

   ![Role prostředků Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. V **role prostředků Azure** seznam, vyhledání role, kterou chcete aktivovat.

    ![Role prostředků Azure – Moje seznam rolí](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Klikněte na tlačítko **aktivovat** a otevřete tak podokno aktivovat.

1. Pokud vaše role se vyžaduje vícefaktorové ověřování (MFA), klikněte na tlačítko **ověřte nejdříve svoji identitu**. Stačí jenom jednou za relace ověřování.

    ![Ověření pomocí vícefaktorového ověřování před aktivaci role](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Klikněte na tlačítko **ověřit moji identitu** a postupujte podle pokynů a poskytují dodatečné ověření zabezpečení.

    ![Ověření pro další úroveň zabezpečení](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Pokud chcete určit obor nižší, klikněte na tlačítko **oboru** a otevřete tak podokno filtru prostředků.

    Je osvědčeným postupem je pouze žádat o přístup k prostředkům, které potřebujete. V podokně filtru prostředků můžete zadat skupiny prostředků nebo prostředky, které potřebují přístup k.

    ![Aktivovat - filtr prostředků](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. V případě potřeby zadejte čas zahájení vlastní aktivace. Člen by byl aktivován po vybraném čase.

1. V **důvod** zadejte důvod pro žádost o aktivaci.

    ![Dokončené aktivovat podokno](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Klikněte na tlačítko **aktivovat**.

    Pokud roli nevyžaduje schválení, je aktivováno a přidán do seznamu aktivních rolí. Pokud chcete použít roli, postupujte podle kroků v další části.

    Pokud [role vyžaduje schválení](pim-resource-roles-approval-workflow.md) k aktivaci, zobrazí se oznámení v pravém horním rohu prohlížeče informování, žádost čeká na schválení.

    ![Žádost čeká na oznámení](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

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

    ![Role Azure AD a role prostředků Azure – Moje žádosti](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Posuňte doprava, abyste viděli **stav žádosti o** sloupce.

## <a name="cancel-a-pending-request"></a>Zrušení čekající žádosti o

Pokud nechcete, aby aktivace role, která vyžaduje schválení, můžete kdykoli zrušit čekající žádosti.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **Moje žádosti**.

1. Pro roli, kterou chcete zrušit, klikněte na tlačítko **zrušit** odkaz.

    Po klepnutí na tlačítko Storno, požadavek bude zrušen. Chcete-li aktivovat roli znovu, budete muset odeslat novou žádost o aktivaci.

   ![Zrušení čekajícího požadavku](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Řešení potíží

### <a name="permissions-not-granted-after-activating-a-role"></a>Oprávnění se neudělilo až po dokončení aktivace role

Při aktivaci role v PIM trvá minimálně 10 minut, než můžete přístup k portálu pro správu požadované nebo provádění funkcí v rámci konkrétní úlohy správy. Po dokončení aktivace odhlásit z webu Azure portal a znovu se přihlaste k použití nově aktivovaného role.

Další postup řešení potíží najdete v tématu [řešení potíží s zvýšenou úroveň oprávnění](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx).

### <a name="cannot-activate-a-role-due-to-a-resource-lock"></a>Nelze aktivovat roli z důvodu uzamčení prostředků

Pokud se zobrazí zpráva, že je uzamčen prostředek Azure při pokusu o aktivaci role, může to být způsobeno prostředků v rámci oboru přiřazení role má zámek prostředku. Zámky prostředků zabránit náhodnému odstranění nebo neočekávaným změnám. Zámek také zabrání PIM odebrat přiřazení role u daného prostředku na konci období aktivace. Protože PIM není schopen správně fungovat, když je nastavený zámek, PIM zabrání uživatelům aktivace role u daného prostředku. Že tento problém můžete vyřešit dvěma způsoby:

- Odstranit zámek, jak je popsáno v [zamknutí prostředků, aby se zabránilo neočekávaným změnám](../../azure-resource-manager/resource-group-lock-resources.md).
- Pokud chcete zachovat zámek, trvalé přiřazení role nebo použití konec pohotovostní účet.

## <a name="next-steps"></a>Další postup

- [Rozšíření nebo obnovení role prostředků Azure v PIM](pim-resource-roles-renew-extend.md)
- [Aktivovat Moje role Azure AD v PIM](pim-how-to-activate-role.md)
