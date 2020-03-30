---
title: Schválení požadavků na role prostředků Azure v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak schválit nebo odepřít požadavky na role prostředků Azure v Azure AD Privileged Identity Management (PIM).
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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e779f633efccf7b594c193e165a584d22b1d653b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021973"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Schválení nebo zamítnutí požadavků na role prostředků Azure v privilegované správě identit

Pomocí správy privilegovaných identit (PIM) ve službě Azure Active Directory (Azure AD) můžete nakonfigurovat role tak, aby vyžadovaly schválení pro aktivaci, a vybrat uživatele nebo skupiny z vaší organizace Azure AD jako delegované schvalovatele. Doporučujeme vybrat dva nebo více schvalovatelů pro každou roli snížit zatížení pro správce privilegované role. Delegovaní schvalovatelé mají 24 hodin na schválení žádostí. Pokud žádost nebude schválena do 24 hodin, musí oprávněný uživatel znovu odeslat novou žádost. 24hodinové časové okno schválení není konfigurovatelné.

Podle kroků v tomto článku schvalte nebo zamítnete požadavky na role prostředků Azure.

## <a name="view-pending-requests"></a>Zobrazení žádostí čekajících na vyřízení

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek na roli prostředku Azure čeká na vaše schválení. Tyto nevyřízené požadavky můžete zobrazit ve správě privilegovaných identit.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **schválit požadavky**.

    ![Schválení žádostí – stránka prostředků Azure zobrazující žádost o kontrolu](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    V části **Žádosti o aktivace rolí** se zobrazí seznam žádostí čekajících na schválení.

## <a name="approve-requests"></a>Schvalování žádostí

1. Vyhledejte a vyberte požadavek, který chcete schválit. Zobrazí se stránka schválení nebo zamítnutí.

    ![Schválit žádosti – schválit nebo odepřít podokno s podrobnostmi a polem Odůvodnění](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Do pole **Odůvodnění** zadejte obchodní odůvodnění.

1. Vyberte **schválit**. Obdržíte oznámení Azure o vašem schválení.

    ![Schválit oznámení zobrazující požadavek byl schválen](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

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
- Správci prostředků jsou upozorněni, když se schválený uživatel stane aktivním ve své roli.

>[!Note]
>Správce prostředků, který se domnívá, že schválený uživatel by neměl být aktivní, může odebrat přiřazení aktivní role ve správě privilegovaných identit. Přestože správci prostředků nejsou upozorněni na nevyřízené žádosti, pokud nejsou schvalovatelem, mohou zobrazit a zrušit čekající požadavky pro všechny uživatele zobrazením čekajících požadavků v privilegované správě identit.

## <a name="next-steps"></a>Další kroky

- [Rozšíření nebo obnovení rolí prostředků Azure ve správě privilegovaných identit](pim-resource-roles-renew-extend.md)
- [E-mailová oznámení ve správě privilegovaných identit](pim-email-notifications.md)
- [Schválení nebo zamítnutí požadavků na role Azure AD ve správě privilegovaných identit](azure-ad-pim-approval-workflow.md)
