---
title: Schvalování žádostí o role prostředků Azure v PIM – Azure AD | Microsoft Docs
description: Naučte se schvalovat nebo odmítat žádosti o role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 096da0547806680f62b569f7c7809a7bbb04cb86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84742075"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Schválení nebo zamítnutí žádostí o role prostředků Azure v Privileged Identity Management

Díky Privileged Identity Management (PIM) v Azure Active Directory (Azure AD) můžete nakonfigurovat role, které vyžadují schválení aktivace, a vybrat uživatele nebo skupiny z vaší organizace Azure AD jako delegované schvalovatele. Pro každou roli doporučujeme vybrat dva nebo více schvalovatelů, abyste snížili zatížení pro správce privilegovaných rolí. Delegovaní schvalovatelé mají ke schválení požadavků 24 hodin. Pokud žádost není schválena do 24 hodin, pak oprávněný uživatel musí znovu odeslat novou žádost. Časový interval schválení 24 hodin nelze konfigurovat.

Podle kroků v tomto článku můžete schvalovat nebo odmítat žádosti o role prostředků Azure.

## <a name="view-pending-requests"></a>Zobrazení žádostí čekajících na vyřízení

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek role prostředku Azure čeká na vaše schválení. Tyto nedokončené žádosti můžete zobrazit v Privileged Identity Management.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **schvalovat žádosti**.

    ![Žádosti o schválení – stránka prostředků Azure ukazující žádost o kontrolu](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    V části **žádosti o aktivaci rolí** se zobrazí seznam žádostí, které čekají na vaše schválení.

## <a name="approve-requests"></a>Schvalování žádostí

1. Vyhledejte a vyberte žádost, kterou chcete schválit. Zobrazí se stránka schválení nebo zamítnutí.

    ![Schválení žádostí – podokno schválení nebo odepření s podrobnostmi a polem odůvodnění](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Do pole **odůvodnění** zadejte obchodní odůvodnění.

1. Vyberte **schválit**. Obdržíte upozornění Azure na vaše schválení.

    ![Schválit oznámení ukazující, že žádost byla schválena](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Zamítnutí žádostí

1. Vyhledejte a vyberte požadavek, který chcete odepřít. Zobrazí se stránka schválení nebo zamítnutí.

    ![Schválení žádostí – podokno schválení nebo odepření s podrobnostmi a polem odůvodnění](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Do pole **odůvodnění** zadejte obchodní odůvodnění.

1. Vyberte **Odepřít**. Zobrazí se oznámení s odepřením.

## <a name="workflow-notifications"></a>Oznámení pracovního postupu

Tady jsou některé informace o oznámeních pracovního postupu:

- Schvalovatelé jsou upozorňováni e-mailem, když požadavek na roli čeká na jejich kontrolu. E-mailová oznámení zahrnují přímý odkaz na žádost, kde schvalovatel může schválit nebo odepřít.
- Žádosti jsou vyřešeny prvním schvalovatelem, který schválí nebo zamítne.
- Když schvalovatel odpoví na požadavek, všichni schvalovatelé budou upozorněni na akci.
- Správci prostředků jsou upozorňováni v případě, že se schváleného uživatele ve své roli aktivuje.

>[!Note]
>Správce prostředků, který se domnívá, že by neměl být aktivní schválený uživatel, může odebrat přiřazení aktivní role v Privileged Identity Management. I když správci prostředků nejsou upozorňováni na nevyřízené žádosti, pokud se nejedná o schvalovatele, mohou zobrazit a zrušit nevyřízené žádosti pro všechny uživatele zobrazením nevyřízených žádostí v Privileged Identity Management.

## <a name="next-steps"></a>Další kroky

- [Rozšiřování nebo obnovení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [E-mailová oznámení v Privileged Identity Management](pim-email-notifications.md)
- [Schválení nebo zamítnutí žádostí o role Azure AD v Privileged Identity Management](azure-ad-pim-approval-workflow.md)
