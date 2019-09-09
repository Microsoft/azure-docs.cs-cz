---
title: Schválení nebo zamítnutí žádostí o role prostředků Azure v PIM-Azure Active Directory | Microsoft Docs
description: Naučte se schvalovat nebo odmítat žádosti o role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b39434f8763e44a126f74ac9a19596e4413ae9c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804270"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Schválení nebo zamítnutí žádostí o role prostředků Azure v PIM

V případě služby Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete nakonfigurovat role tak, aby vyžadovaly schválení aktivace, a vybrat jednoho nebo více uživatelů nebo skupin jako delegované schvalovatele. Delegovaní schvalovatelé mají ke schválení požadavků 24 hodin. Pokud žádost není schválena do 24 hodin, pak oprávněný uživatel musí znovu odeslat novou žádost. Časový interval schválení 24 hodin nelze konfigurovat.

Podle kroků v tomto článku můžete schvalovat nebo odmítat žádosti o role prostředků Azure.

## <a name="view-pending-requests"></a>Zobrazit nevyřízené žádosti

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek role prostředku Azure čeká na vaše schválení. Tyto nedokončené žádosti můžete zobrazit v PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **schvalovat žádosti**.

    ![Žádosti o schválení – stránka prostředků Azure ukazující žádost o kontrolu](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    V části **žádosti o aktivaci rolí** se zobrazí seznam žádostí, které čekají na vaše schválení.

## <a name="approve-requests"></a>Schválit žádosti

1. Vyhledejte a klikněte na žádost, kterou chcete schválit. Zobrazí se podokno schválení nebo zamítnutí.

    ![Schválení žádostí – podokno schválení nebo odepření s podrobnostmi a polem odůvodnění](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Do pole **odůvodnění** zadejte důvod.

1. Klikněte na tlačítko **schválit**.

    Zobrazí se oznámení s vaším souhlasem.

    ![Schválit oznámení ukazující, že žádost byla schválena](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Zamítnutí žádostí

1. Vyhledejte a klikněte na žádost, kterou chcete odepřít. Zobrazí se podokno schválení nebo zamítnutí.

    ![Schválení žádostí – podokno schválení nebo odepření s podrobnostmi a polem odůvodnění](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Do pole **odůvodnění** zadejte důvod.

1. Klikněte na **Odepřít**.

    Zobrazí se oznámení s odepřením.

## <a name="workflow-notifications"></a>Oznámení pracovního postupu

Tady jsou některé informace o oznámeních pracovního postupu:

- V případě, že požadavek na roli čeká na revizi, budou všichni členové seznamu schvalovatelů upozorňováni e-mailem. E-mailová oznámení zahrnují přímý odkaz na žádost, kde schvalovatel může schválit nebo odepřít.
- Žádosti jsou vyřešeny prvním členem seznamu, který schválí nebo zamítne.
- Když schvalovatel odpoví na požadavek, všichni členové seznamu schvalovatelé budou upozorněni na akci.
- Správci prostředků jsou upozorněni v případě, že se schválený člen ve své roli aktivuje.

>[!Note]
>Správce prostředků, který se domnívá, že by neměl být aktivní člen aktivní, může odebrat přiřazení aktivní role v PIM. I když správci prostředků nejsou upozorňováni na nevyřízené žádosti, pokud nejsou členy seznamu schvalovatelů, můžou zobrazit a zrušit nedokončené žádosti všech uživatelů, a to zobrazením nevyřízených žádostí v PIM. 

## <a name="next-steps"></a>Další postup

- [Rozšiřování nebo obnovení rolí prostředků Azure v PIM](pim-resource-roles-renew-extend.md)
- [E-mailová oznámení v PIM](pim-email-notifications.md)
- [Schválení nebo zamítnutí žádostí o role Azure AD v PIM](azure-ad-pim-approval-workflow.md)
