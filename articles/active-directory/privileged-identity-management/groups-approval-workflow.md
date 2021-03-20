---
title: Schvalování žádostí o aktivaci pro členy skupiny a vlastníky v Privileged Identity Management – Azure AD
description: Naučte se schvalovat nebo odmítat žádosti pro skupiny s přiřazením rolí v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a3b6dad33ce678478a201dc8b7b612f4b8507c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91536983"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>Schvalování žádostí o aktivaci pro členy a vlastníky skupiny privilegovaného přístupu (Preview)

U Privileged Identity Management (PIM) v Azure Active Directory (Azure AD) můžete nakonfigurovat členy skupiny privilegovaný přístup a vlastníky, aby vyžadovaly schválení aktivace, a vybrat uživatele nebo skupiny z vaší organizace Azure AD jako delegované schvalovatele. Pro každou skupinu doporučujeme vybrat dva nebo více schvalovatelů, abyste snížili zatížení pro správce privilegovaných rolí. Delegovaní schvalovatelé mají ke schválení požadavků 24 hodin. Pokud žádost není schválena do 24 hodin, pak oprávněný uživatel musí znovu odeslat novou žádost. Časový interval schválení 24 hodin nelze konfigurovat.

Podle kroků v tomto článku můžete schvalovat nebo odmítat žádosti o role prostředků Azure.

## <a name="view-pending-requests"></a>Zobrazení žádostí čekajících na vyřízení

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek role prostředku Azure čeká na vaše schválení. Můžete zobrazit nevyřízené žádosti v Privileged Identity Management.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **schvalovat žádosti**.

    ![Žádosti o schválení – stránka prostředků Azure ukazující žádost o kontrolu](./media/groups-approval-workflow/groups-select-request.png)

    V části **žádosti o aktivaci rolí** se zobrazí seznam žádostí, které čekají na vaše schválení.

## <a name="approve-requests"></a>Schvalování žádostí

1. Vyhledejte a vyberte žádost, kterou chcete schválit, a vyberte **schválit**.

    ![Snímek obrazovky zobrazující stránku schválení požadavků se zvýrazněnými tlačítky schválit a potvrdit](./media/groups-approval-workflow/groups-confirm-approval.png)

1. Do pole **odůvodnění** zadejte obchodní odůvodnění.

1. Vyberte **Potvrdit**. Vaše schválení vygeneruje oznámení Azure.

## <a name="deny-requests"></a>Zamítnutí žádostí

1. Vyhledejte a vyberte požadavek, který chcete odepřít, a vyberte **Odepřít**.

    ![Schválení žádostí – podokno schválení nebo odepření s podrobnostmi a polem odůvodnění](./media/groups-approval-workflow/groups-confirm-denial.png)

1. Do pole **odůvodnění** zadejte obchodní odůvodnění.

1. Vyberte **Potvrdit**. Odmítnutí vygeneruje oznámení Azure.

## <a name="workflow-notifications"></a>Oznámení pracovního postupu

Tady jsou některé informace o oznámeních pracovního postupu:

- Schvalovatelé jsou upozorňováni e-mailem, když požadavek na přiřazení skupiny čeká na jejich kontrolu. E-mailová oznámení zahrnují přímý odkaz na žádost, kde schvalovatel může schválit nebo odepřít.
- Žádosti jsou vyřešeny prvním schvalovatelem, který schválí nebo zamítne.
- Když schvalovatel odpoví na požadavek, všichni schvalovatelé budou upozorněni na akci.

>[!Note]
>Správce, který se domnívá, že by neměl být aktivní schválený uživatel, může odebrat přiřazení aktivní skupiny v Privileged Identity Management. I když správci prostředků nejsou upozorňováni na nevyřízené žádosti, pokud se nejedná o schvalovatele, mohou zobrazit a zrušit nevyřízené žádosti pro všechny uživatele zobrazením nevyřízených žádostí v Privileged Identity Management.

## <a name="next-steps"></a>Další kroky

- [Prodloužit nebo prodloužit přiřazení skupiny v Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [E-mailová oznámení v Privileged Identity Management](pim-email-notifications.md)
- [Schválení nebo zamítnutí žádostí o přiřazení skupiny v Privileged Identity Management](azure-ad-pim-approval-workflow.md)
