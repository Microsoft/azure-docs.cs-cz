---
title: Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Naučte se schválit nebo zamítnout žádosti pro role prostředků Azure v Azure AD Privileged Identity Management (PIM).
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
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d2e8b4ae1a01cd299d910c4e88655885c7d00dc
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476385"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM

S Azure Active Directory (Azure AD) Privileged Identity Management (PIM), můžete konfigurovat role, které vyžadují schválení pro aktivaci a zvolte jednu nebo více uživatelů nebo skupin jako delegovaný schvalovatele. Delegované schvalovatelů mít 24 hodin se schválit žádosti. Pokud žádost není schválený během 24 hodin, oprávněný uživatel musí znovu odešlete novou žádost. Časový interval 24 hodin schválení není konfigurovatelné.

Postupujte podle kroků v tomto článku pro schválení nebo zamítnutí žádosti pro role prostředků Azure.

## <a name="view-pending-requests"></a>Zobrazit čekající žádosti

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek pro role prostředků Azure čeká na schválení. Zobrazí se tyto žádosti čekající na vyřízení v PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **schvalovat žádosti o**.

    ![Schválit žádosti – prostředky Azure stránky zobrazující žádosti ke kontrole](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    V **žádosti o aktivace rolí** části, zobrazí se vám seznam žádosti čekající na vaše schválení.

## <a name="approve-requests"></a>Schvalování žádostí

1. Vyhledejte a klikněte na žádost, kterou chcete schválit. Schválit nebo odmítnout, otevře se podokno.

    ![Schválit žádosti – Schvalte nebo zamítněte podokně s podrobnostmi a odůvodnění pole](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. V **odůvodnění** zadejte důvod.

1. Klikněte na tlačítko **schválit**.

    Oznámení se zobrazí vaše schválení.

    ![Schválit oznámení, že žádost byla schválena](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Zamítnout žádosti

1. Vyhledejte a klikněte na tlačítko, které chcete zamítnout žádost. Schválit nebo odmítnout, otevře se podokno.

    ![Schválit žádosti – Schvalte nebo zamítněte podokně s podrobnostmi a odůvodnění pole](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. V **odůvodnění** zadejte důvod.

1. Klikněte na tlačítko **Odepřít**.

    Oznámení se zobrazí s vaší odmítnutí.

## <a name="workflow-notifications"></a>Oznámení pro pracovní postupy

Zde jsou některé informace o oznámeních pracovního postupu:

- Všichni členové schvalovatel seznamu se zobrazí oznámení e-mailem žádost o roli čeká na kontrolu. E-mailová oznámení zahrnují přímý odkaz na žádost, ve kterém můžete schvalující schválit nebo zamítnout.
- Požadavky jsou vyřešeny prvního člena seznamu, který potvrdí nebo odmítne.
- Když schvalující odpoví na žádost, se zobrazí všechny členy seznamu schvalovatel oznámení akce.
- Správci prostředků se zobrazí oznámení, když schváleného člena stane aktivní v jejich role.

>[!Note]
>Správce prostředků, který se řídí zásadou, že by neměl být aktivní schváleného člena odebrat přiřazení active role v PIM. Přestože správci prostředků nejsou dostávají oznámení o čekajících žádostí Pokud nejsou členy seznamu schvalovatele, mohou zobrazit a zrušit čekající žádosti o pro všechny uživatele tak, že zobrazení čekající žádosti v PIM. 

## <a name="next-steps"></a>Další postup

- [Rozšíření nebo obnovení role prostředků Azure v PIM](pim-resource-roles-renew-extend.md)
- [E-mailových oznámení v PIM](pim-email-notifications.md)
- [Schválit nebo zamítnout žádosti pro role Azure AD v PIM](azure-ad-pim-approval-workflow.md)
