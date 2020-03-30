---
title: Schválení nebo zamítnutí požadavků na role Azure AD v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak schválit nebo odepřít požadavky na role Azure AD v azure ad privilegované správy identit (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ccfd1ad270072989e9b575fda538b94fd8927c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049021"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Schválení nebo zamítnutí požadavků na role Azure AD ve správě privilegovaných identit

Pomocí správy privilegovaných identit Azure Active Directory (Azure AD) můžete nakonfigurovat role tak, aby vyžadovaly schválení pro aktivaci, a jako delegované schvalovatele vybrat jednoho nebo více uživatelů nebo skupin. Delegovaní schvalovatelé mají 24 hodin na schválení žádostí. Pokud žádost nebude schválena do 24 hodin, musí oprávněný uživatel znovu odeslat novou žádost. 24hodinové časové okno schválení není konfigurovatelné.

## <a name="determine-your-version-of-pim"></a>Určení verze PIM

Počínaje listopadem 2019 se část rolí Azure AD privileged identity management aktualizuje na novou verzi, která odpovídá prostředí pro role Azure. Tím se vytvoří další funkce, stejně jako [změny existující rozhraní API](azure-ad-roles-features.md#api-changes). Při zavádění nové verze, které postupy, které budete postupovat v tomto článku, závisí na verzi správy privilegovaných identit, kterou aktuálně máte. Podle pokynů v této části určete, kterou verzi správy privilegovaných identit máte. Poté, co znáte verzi správy privilegovaných identit, můžete vybrat postupy v tomto článku, které odpovídají této verzi.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) s uživatelem, který je v roli [správce privilegované role.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otevřete **správu privilegovaných identit Azure AD**. Pokud máte v horní části stránky přehledu nápis, postupujte podle pokynů na kartě **Nová verze** tohoto článku. V opačném případě postupujte podle pokynů na kartě **Předchozí verze.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Podle kroků v tomto článku schválit nebo odepřít žádosti o role Azure AD.

# <a name="new-version"></a>[Nová verze](#tab/new)

## <a name="view-pending-requests"></a>Zobrazení žádostí čekajících na vyřízení

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek na roli Azure AD čeká na vaše schválení. Tyto nevyřízené požadavky můžete zobrazit ve správě privilegovaných identit.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **schválit požadavky**.

    ![Schválení žádostí – stránka zobrazující žádost o kontrolu rolí Azure AD](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    V části **Žádosti o aktivace rolí** se zobrazí seznam žádostí čekajících na schválení.

## <a name="approve-requests"></a>Schvalování žádostí

1. Vyhledejte a vyberte požadavek, který chcete schválit. Zobrazí se stránka schválení nebo zamítnutí.

    ![Schválit žádosti – schválit nebo odepřít podokno s podrobnostmi a polem Odůvodnění](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Do pole **Odůvodnění** zadejte obchodní odůvodnění.

1. Vyberte **schválit**. Obdržíte oznámení Azure o vašem schválení.

    ![Schválit oznámení zobrazující požadavek byl schválen](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

## <a name="deny-requests"></a>Odepřít požadavky

1. Najděte a vyberte požadavek, který chcete odmítnout. Zobrazí se stránka schválení nebo zamítnutí.

    ![Schválit žádosti – schválit nebo odepřít podokno s podrobnostmi a polem Odůvodnění](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Do pole **Odůvodnění** zadejte obchodní odůvodnění.

1. Vyberte **možnost Odepřít**. Zobrazí se oznámení s vaším odmítnutím.

## <a name="workflow-notifications"></a>Oznámení pracovního postupu

Tady jsou některé informace o oznámeních pracovního postupu:

- Schvalovatelé jsou upozorněni e-mailem, když žádost o roli čeká na kontrolu. E-mailová oznámení obsahují přímý odkaz na žádost, kde může schvalovatel schválit nebo zamítnout.
- Požadavky jsou vyřešeny prvním schvalovatelem, který schvaluje nebo zamítá.
- Když schvalovatel odpoví na žádost, všichni schvalovatelé jsou upozorněni na akci.
- Globální správci a správci privilegovaných rolí jsou upozorněni, když se schválený uživatel stane aktivním ve své roli.

>[!NOTE]
>Globální správce nebo správce privilegovaných rolí, který se domnívá, že schválený uživatel by neměl být aktivní, může odebrat aktivní přiřazení role ve správě privilegovaných identit. Přestože správci nejsou upozorněni na nevyřízené žádosti, pokud nejsou schvalovatelem, mohou zobrazit a zrušit všechny nevyřízené žádosti pro všechny uživatele zobrazením čekajících požadavků v privilegované správě identit.

# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

## <a name="view-pending-requests"></a>Zobrazení žádostí čekajících na vyřízení

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek na roli Azure AD čeká na vaše schválení. Tyto nevyřízené požadavky můžete zobrazit ve správě privilegovaných identit.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Klikněte na **role Azure AD**.

1. Klepněte na **tlačítko Schválit požadavky**.

    ![Role Azure AD – schvalování požadavků](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Zobrazí se seznam žádostí, které čekají na váš souhlas.

## <a name="approve-requests"></a>Schvalování žádostí

1. Vyberte požadavky, které chcete schválit, a kliknutím na **Schválit** otevřete podokno Schválit vybrané požadavky.

    ![Schválit seznam žádostí se zvýrazněnou možností Schválit](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Do pole **Schválit důvod** zadejte důvod.

    ![Podokno Schválení vybraných požadavků s důvodem schválení](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klikněte na **Schválit**.

    Symbol stavu bude aktualizován s vaším souhlasem.

    ![Podokno Schválit vybrané požadavky po kliknutí na tlačítko Schválit](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Odepřít požadavky

1. Vyberte požadavky, které chcete odepřít, a kliknutím na **tlačítko Odepřít** otevřete podokno Odepřít vybrané požadavky.

    ![Schválit seznam žádostí se zvýrazněnou možností Odepřít](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Do pole **Odepřít důvod** zadejte důvod.

    ![Podokno Odepřít vybrané požadavky s důvodem zamítnutí](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Vyberte **možnost Odepřít**.

    Symbol stavu bude aktualizován s vaším odmítnutím.

---

## <a name="next-steps"></a>Další kroky

- [E-mailová oznámení ve správě privilegovaných identit](pim-email-notifications.md)
- [Schválení nebo zamítnutí požadavků na role prostředků Azure v privilegované správě identit](pim-resource-roles-approval-workflow.md)
