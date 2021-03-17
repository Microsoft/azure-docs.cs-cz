---
title: Schválení nebo zamítnutí žádostí o role Azure AD v PIM – Azure AD | Microsoft Docs
description: Naučte se schvalovat nebo odmítat žádosti pro role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d74a243ab10d0fffca960dc5149999560a7b54cd
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980582"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Schválení nebo zamítnutí žádostí o role Azure AD v Privileged Identity Management

V případě služby Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete nakonfigurovat role tak, aby vyžadovaly schválení aktivace, a vybrat jednoho nebo více uživatelů nebo skupin jako delegované schvalovatele. Delegovaní schvalovatelé mají ke schválení požadavků 24 hodin. Pokud žádost není schválena do 24 hodin, pak oprávněný uživatel musí znovu odeslat novou žádost. Časový interval schválení 24 hodin nelze konfigurovat.

## <a name="determine-your-version-of-pim"></a>Určení vaší verze PIM

Od listopadu 2019 se v části Privileged Identity Management role Azure AD aktualizuje na novou verzi, která se shoduje s prostředími pro role Azure. Tím se vytvoří další funkce a také [změny stávajícího rozhraní API](azure-ad-roles-features.md#api-changes). I když je nová verze zahrnuta, postupy, které provedete v tomto článku, závisí na verzi Privileged Identity Management, kterou máte v současnosti k dispozici. Podle pokynů v této části určete, kterou verzi Privileged Identity Management máte. Po zjištění vaší verze Privileged Identity Management můžete vybrat postupy v tomto článku, které odpovídají této verzi.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí uživatele, který je v roli [správce privilegované role](../roles/permissions-reference.md#privileged-role-administrator) .
1. Otevřete **Azure AD Privileged Identity Management**. Pokud máte banner v horní části stránky s přehledem, postupujte podle pokynů na kartě **Nová verze** v tomto článku. Jinak postupujte podle pokynů na kartě **předchozí verze** .

    [![Vyberte Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Podle kroků v tomto článku můžete schvalovat nebo odmítat žádosti pro role Azure AD.

## <a name="new-version"></a>[Nová verze](#tab/new)

### <a name="view-pending-requests"></a>Zobrazení žádostí čekajících na vyřízení

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek role Azure AD čeká na vaše schválení. Tyto nedokončené žádosti můžete zobrazit v Privileged Identity Management.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **schvalovat žádosti**.

    ![Schvalovat žádosti – stránka ukazující žádost o revizi rolí Azure AD](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    V části **žádosti o aktivaci rolí** se zobrazí seznam žádostí, které čekají na vaše schválení.

### <a name="approve-requests"></a>Schvalování žádostí

1. Vyhledejte a vyberte žádost, kterou chcete schválit. Zobrazí se stránka schválení nebo zamítnutí.

    ![Snímek obrazovky zobrazující stránku "schvalovat žádosti – role Azure AD".](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Do pole **odůvodnění** zadejte obchodní odůvodnění.

1. Vyberte **schválit**. Obdržíte upozornění Azure na vaše schválení.

    ![Schválit oznámení ukazující, že žádost byla schválena](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

### <a name="deny-requests"></a>Zamítnutí žádostí

1. Vyhledejte a vyberte požadavek, který chcete odepřít. Zobrazí se stránka schválení nebo zamítnutí.

    ![Schválení žádostí – podokno schválení nebo odepření s podrobnostmi a polem odůvodnění](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Do pole **odůvodnění** zadejte obchodní odůvodnění.

1. Vyberte **Odepřít**. Zobrazí se oznámení s odepřením.

### <a name="workflow-notifications"></a>Oznámení pracovního postupu

Tady jsou některé informace o oznámeních pracovního postupu:

- Schvalovatelé jsou upozorňováni e-mailem, když požadavek na roli čeká na jejich kontrolu. E-mailová oznámení zahrnují přímý odkaz na žádost, kde schvalovatel může schválit nebo odepřít.
- Žádosti jsou vyřešeny prvním schvalovatelem, který schválí nebo zamítne.
- Když schvalovatel odpoví na požadavek, všichni schvalovatelé budou upozorněni na akci.
- Správci globálních správců a privilegovaných rolí jsou upozorněni v případě, že se schváleného uživatele ve své roli aktivuje.

>[!NOTE]
>Správce globálního správce nebo privilegované role, který se domnívá, že by neměl být aktivní schválený uživatel, může odebrat přiřazení aktivní role v Privileged Identity Management. I když správci nejsou upozorňováni na nevyřízené žádosti, pokud se nejedná o schvalovatele, mohou zobrazit a zrušit všechny nevyřízené žádosti pro všechny uživatele zobrazením nevyřízených žádostí v Privileged Identity Management.

## <a name="previous-version"></a>[Předchozí verze](#tab/previous)

### <a name="view-pending-requests"></a>Zobrazení žádostí čekajících na vyřízení

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek role Azure AD čeká na vaše schválení. Tyto nedokončené žádosti můžete zobrazit v Privileged Identity Management.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **role Azure AD**.

1. Klikněte na **schvalovat žádosti**.

    ![Role Azure AD – schvalování žádostí](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Zobrazí se seznam žádostí, které čekají na vaše schválení.

### <a name="approve-requests"></a>Schvalování žádostí

1. Vyberte žádosti, které chcete schválit, a kliknutím na **schválit** otevřete podokno schválit vybrané žádosti.

    ![Seznam žádostí o schválení se zvýrazněnou možností schválit](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Do pole **důvod schválení** zadejte důvod.

    ![Schválit podokno vybrané žádosti s odůvodněním schválení](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klikněte na **Schválit**.

    Symbol stavu bude aktualizován na vaše schválení.

    ![Kliknutím na tlačítko Schválit podokno schválení vybraných žádostí](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

### <a name="deny-requests"></a>Zamítnutí žádostí

1. Vyberte žádosti, které chcete odepřít, a kliknutím na **Odepřít** otevřete podokno odepřít vybrané žádosti.

    ![Seznam žádostí o schválení s zvýrazněnou možností odepřít](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Do pole **důvod zamítnutí** zadejte důvod.

    ![Odepřít podokno vybrané žádosti s odůvodněním odepřít](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Vyberte **Odepřít**.

    Symbol stavu bude aktualizován o Vaše odmítnutí.

---

## <a name="next-steps"></a>Další kroky

- [E-mailová oznámení v Privileged Identity Management](pim-email-notifications.md)
- [Schválení nebo zamítnutí žádostí o role prostředků Azure v Privileged Identity Management](pim-resource-roles-approval-workflow.md)
