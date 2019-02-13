---
title: Aktivovat Moje role adresáře Azure AD v PIM | Dokumentace Microsoftu
description: Zjistěte, jak aktivovat role adresáře Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0023ac374ef70593d0ab2d9589c99d0f37e19ff8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189595"
---
# <a name="activate-my-azure-ad-directory-roles-in-pim"></a>Aktivovat Moje role adresáře Azure AD v PIM

Privileged Identity Management (PIM) ve Azure Active Directory (Azure AD) zjednodušuje, jak podniky spravovat privilegovaný přístup k prostředkům v Azure AD a dalších online službách Microsoftu jako Office 365 nebo Microsoft Intune.  

Pokud jste se provedly oprávněné pro roli správce, znamená to, že můžete aktivovat tuto roli, když budete potřebovat k provedení privilegovaných akcí. Například pokud někdy budete spravovat funkce Office 365, správci privilegovaných rolí vaší organizace nemusí mít je trvalé globální správce, protože tato role má vliv jiné služby, příliš. Místo toho využívají je vhodné pro role Azure AD, jako je například správce Exchange Online. Můžete požádat o danou roli aktivovat, když potřebujete jeho oprávnění a potom budete mít ovládací prvek správce předem určené časové období.

Tento článek je určený pro správce, kteří muset aktivovat svoje role Azure AD directory v PIM.

## <a name="activate-a-role"></a>Aktivovat roli

Pokud potřebujete provést pro role adresáře Azure AD, můžete žádost o aktivaci, s použitím **Moje role** možnost navigace v PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**. Informace o tom, jak přidat dlaždici PIM do řídicího panelu, naleznete v tématu [začít používat PIM](pim-getting-started.md).

1. Klikněte na tlačítko **role adresáře Azure AD**.

1. Klikněte na tlačítko **Moje role** zobrazíte seznam vašich oprávněných rolí adresáře Azure AD.

    ![Role adresáře Azure AD – Moje role](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Vyhledání role, kterou chcete aktivovat.

    ![Role adresáře Azure AD – seznam rolí](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Klikněte na tlačítko **aktivovat** a otevřete tak podokno Podrobnosti o aktivaci Role.

1. Pokud vaše role se vyžaduje vícefaktorové ověřování (MFA), klikněte na tlačítko **ověřte nejdříve svoji identitu**. Stačí jenom jednou za relace ověřování.

    ![Ověření pomocí vícefaktorového ověřování před aktivaci role](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Klikněte na tlačítko **ověřit moji identitu** a postupujte podle pokynů a poskytují dodatečné ověření zabezpečení.

    ![Ověření pro další úroveň zabezpečení](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Klikněte na tlačítko **aktivovat** a otevřete tak podokno aktivace.

    ![Podokno aktivace](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. V případě potřeby zadejte čas zahájení vlastní aktivace.

1. Zadejte dobu trvání aktivace.

1. V **důvod aktivace** zadejte důvod pro žádost o aktivaci. Některé role vyžadují, abyste zadat číslo lístku potíže.

    ![Dokončené podokně aktivace](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Klikněte na tlačítko **aktivovat**.

    Pokud roli nevyžaduje schválení, je aktivováno a přidán do seznamu aktivních rolí. Pokud chcete okamžitě použít roli, postupujte podle kroků v další části.

    Pokud [role vyžaduje schválení](./azure-ad-pim-approval-workflow.md) k aktivaci, zobrazí se oznámení v pravém horním rohu prohlížeče informování, žádost čeká na schválení.

    ![Žádost čeká na oznámení](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Použijte roli okamžitě po aktivaci

Při aktivaci role v PIM trvá minimálně 10 minut, než můžete přístup k portálu pro správu požadované nebo provádění funkcí v rámci konkrétní úlohy správy. Chcete-li vynutit aktualizaci oprávnění, použijte **přístupu k aplikacím** stránce, jak je popsáno v následujících krocích.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **přístupu k aplikacím** stránky.

    ![Přístup k aplikaci PIM](./media/pim-how-to-activate-role/pim-application-access.png)

1. Klikněte na tlačítko **Azure Active Directory** odkaz pro znovuotevření na portálu **všichni uživatelé** stránky.

    Po kliknutí na tento odkaz, platnost aktuálního tokenu a na webu Azure portal získat nový token, který by měl obsahovat aktualizované oprávnění vynutit.

## <a name="view-the-status-of-your-requests"></a>Zobrazit stav vašich požadavků

Můžete zobrazit stav vaší žádosti čekající na aktivaci.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **role adresáře Azure AD**.

1. Klikněte na tlačítko **Moje žádosti** chcete zobrazit seznam žádostí.

    ![Role adresáře Azure AD – Moje žádosti](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Deaktivace role

Po aktivaci role automaticky deaktivuje při dosažení jeho časový limit (doba trvání způsobilosti).

Pokud vaše úkoly správce dokončeny včas, můžete také deaktivace role v Azure AD Privileged Identity Management ručně.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **role adresáře Azure AD**.

1. Klikněte na tlačítko **Moje role**.

1. Klikněte na tlačítko **aktivních rolí** zobrazíte seznam aktivních rolí.

1. Najít roli, budete mít pomocí a potom klikněte na tlačítko **deaktivovat**.

## <a name="cancel-a-pending-request"></a>Zrušení čekající žádosti o

Pokud nechcete, aby aktivace role, která vyžaduje schválení, můžete kdykoli zrušit čekající žádosti.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **role adresáře Azure AD**.

1. Klikněte na tlačítko **Moje žádosti**.

1. Pro roli, kterou chcete zrušit, klikněte na tlačítko **zrušit** tlačítko.

    Po klepnutí na tlačítko Storno, požadavek bude zrušen. Chcete-li aktivovat roli znovu, budete muset odeslat novou žádost o aktivaci.

   ![Zrušení čekajícího požadavku](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Řešení potíží

### <a name="permissions-not-granted-after-activating-a-role"></a>Oprávnění se neudělilo až po dokončení aktivace role

Při aktivaci role v PIM trvá minimálně 10 minut, než můžete přístup k portálu pro správu požadované nebo provádění funkcí v rámci konkrétní úlohy správy. Chcete-li vynutit aktualizaci oprávnění, použijte **přístupu k aplikacím** stránce, jak je popsáno výše v [použít roli okamžitě po aktivaci](#use-a-role-immediately-after-activation).

Další postup řešení potíží najdete v tématu [řešení potíží s zvýšenou úroveň oprávnění](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx).

## <a name="next-steps"></a>Další postup

- [Aktivovat Moje role prostředků Azure v PIM](pim-resource-roles-activate-your-roles.md)
