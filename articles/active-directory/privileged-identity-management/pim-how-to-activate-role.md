---
title: Postup aktivace nebo deaktivace role | Dokumentace Microsoftu
description: Zjistěte, jak aktivovat role pro privilegované identity pomocí aplikace Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 54e092cb98df6c092e6cf3ed8c365f966587b0db
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091597"
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Postup aktivace nebo deaktivace role v Azure AD Privileged Identity Management
Azure Active Directory (AD) Privileged Identity Management zjednodušuje, jak podniky spravovat privilegovaný přístup k prostředkům v Azure AD a dalších online službách Microsoftu jako Office 365 nebo Microsoft Intune.  

Pokud jste se provedly oprávněné pro roli správce, znamená to, že můžete aktivovat tuto roli, když budete potřebovat k provedení privilegovaných akcí. Například pokud někdy budete spravovat funkce Office 365, správci privilegovaných rolí vaší organizace nemusí mít je trvalé globální správce, protože tato role má vliv jiné služby, příliš. Místo toho využívají je vhodné pro role Azure AD, jako je například správce Exchange Online. Můžete požádat o danou roli aktivovat, když potřebujete jeho oprávnění a potom budete mít řízení správy na předem určenou dobu.

Tento článek je určený pro správce, kteří muset aktivovat svoje role v Azure AD Privileged Identity Management (PIM). Provede vás provede kroky pro aktivaci role, když potřebujete oprávnění a až to budete mít deaktivovat roli. Kromě toho správci privilegovaných rolí můžou k aktivaci vyžadovat schválení role (Preview). Další informace o [pracovních postupů schvalování PIM](./azure-ad-pim-approval-workflow.md) tady.

## <a name="add-the-privileged-identity-management-application"></a>Přidání aplikace Privileged Identity Management
Pomocí aplikace Azure AD Privileged Identity Management [webu Azure portal](https://portal.azure.com/) požádat o aktivaci role, i v případě, že se chystáte provozovat v jiném portal nebo Powershellu. Pokud nemáte aplikaci Azure AD Privileged Identity Management na webu Azure portal, postupujte podle těchto kroků, abyste mohli začít.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vyberte své uživatelské jméno v pravém horním rohu webu Azure portal a vyberte adresář, kde se dozvíte, jak můžete pracovat.

1. Vyberte **Všechny služby** a pomocí textového pole Filtr najděte **Azure AD Privileged Identity Management**.

1. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

## <a name="activate-a-role"></a>Aktivovat roli
Pokud potřebujete provést na roli, můžete požádat o aktivaci, tak, že vyberete **Moje role** možnost navigace v aplikaci Azure AD Privileged Identity Management levým navigačním sloupci.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) a vyberte dlaždici Azure AD Privileged Identity Management.

1. Vyberte **Moje role**. Zobrazí seznam přiřazenou oprávněnou roli v seskupení v horní části stránky.

1. Vyberte roli, kterou chcete aktivovat.

1. Vyberte **aktivovat**. **Žádost o aktivaci role** otevře se okno.

1. Některé role vyžadují Vícefaktorové ověřování (MFA), než budete aktivovat roli. Stačí jenom jednou za relace ověřování.

    ![Ověření pomocí vícefaktorového ověřování před aktivace role – snímek obrazovky](./media/pim-how-to-activate-role/PIM_activation_MFA.png)

1. Zadejte důvod pro žádost o aktivaci v textovém poli.  Některé role vyžadují, abyste zadat číslo lístku potíže.

1. Vyberte **OK**.  Pokud roli nevyžaduje schválení, se teď aktivuje a role se zobrazí v seznamu aktivních rolí (přímo pod seznamem přiřazení oprávněné role). Pokud [role vyžaduje schválení](./azure-ad-pim-approval-workflow.md) Pokud chcete aktivovat, se krátce zobrazí oznámení s informační zprávou v pravém horním rohu prohlížeče informování, žádost čeká na schválení.

    ![Žádost čeká na oznámení – snímek obrazovky](./media/pim-how-to-activate-role/PIM_Request_Pending_Toast2.png)

## <a name="use-a-role-immediately-after-activation"></a>Použijte roli okamžitě po aktivaci

Z důvodu ukládání do mezipaměti, počet aktivací nedojde okamžitě na webu Azure Portal bez její aktualizaci. Pokud potřebujete omezit možnost zpoždění až po dokončení aktivace rolí, můžete použít **přístupu k aplikacím** stránky na portálu. Nové přiřazení role aplikace k němu přistupovat z této stránky vyhledat okamžitě.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **přístupu k aplikacím** stránky.

    ![Přístup k aplikaci PIM – snímek obrazovky](./media/pim-how-to-activate-role/pim-application-access.png)

1. Klikněte na tlačítko **Azure Active Directory** znovu otevřít na portálu na **všichni uživatelé** stránky.

    Při kliknutí na tento odkaz, můžete vynutit aktualizaci a není kontrolu pro nový Azure AD přiřazení rolí.

## <a name="deactivate-a-role"></a>Deaktivace role
Po aktivaci role automaticky deaktivuje při dosažení jeho časový limit (doba trvání způsobilosti).

Pokud jste již v rané fázi dokončení úlohy správy, můžete také deaktivace role ručně v aplikaci Azure AD Privileged Identity Management.  Vyberte **Moje role**, zvolte roli, budete mít pomocí z **aktivní přiřazení rolí** seskupování a vyberte **deaktivovat**.  

## <a name="cancel-a-pending-request"></a>Zrušení čekající žádosti o
V případě, že nechcete, aby aktivace role, která vyžaduje schválení, můžete kdykoli zrušit čekající žádosti. Jednoduše vyberte **Moje role** možnost navigace v aplikaci Azure AD Privileged Identity Management levým navigačním sloupci.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) a vyberte dlaždici Azure AD Privileged Identity Management.

1. Vyberte **Moje role**. Zobrazí seznam přiřazenou oprávněnou roli v seskupení v horní části stránky.

1. Vyberte roli.

1. Vyberte **aktivaci čeká na schválení** úvodní nápis v okně Podrobnosti o aktivaci role.

1. Vyberte **zrušit** v horní části **čekající na schválení** okno.

   ![Zrušení čekající žádosti o snímek obrazovky](./media/pim-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png)

## <a name="next-steps"></a>Další postup
Pokud vás zajímá dostávat další informace o Azure AD Privileged Identity Management, následující odkazy obsahují další informace.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
