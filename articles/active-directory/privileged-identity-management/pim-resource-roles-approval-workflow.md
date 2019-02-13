---
title: Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM | Dokumentace Microsoftu
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
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c176055098cd1a24fec6b4bc4a1265c1062ac82b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56218002"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM

S Azure AD Privileged Identity Management (PIM) můžete nakonfigurovat role, které vyžadují schválení pro aktivaci a zvolte jednu nebo více uživatelů nebo skupin jako delegovaný schvalovatele. Postupujte podle kroků v tomto článku pro schválení nebo zamítnutí žádosti pro role prostředků Azure.

## <a name="view-pending-requests"></a>Zobrazit čekající žádosti

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek pro role prostředků Azure čeká na schválení. Zobrazí se tyto žádosti čekající na vyřízení v PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **schvalovat žádosti o**.

    ![Prostředky Azure – schválit žádosti](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    V **žádosti o aktivace rolí** části, zobrazí se vám seznam žádosti čekající na vaše schválení.

## <a name="approve-requests"></a>Schvalování žádostí

1. Vyhledejte a klikněte na žádost, kterou chcete schválit. Otevře se podokno schválení.

    ![Schválit žádosti podokno](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. V **odůvodnění** zadejte důvod.

1. Klikněte na tlačítko **schválit**.

    Oznámení se zobrazí vaše schválení.

    ![Schvalte oznámení](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Zamítnout žádosti

1. Vyhledejte a klikněte na tlačítko, které chcete zamítnout žádost. Otevře se podokno schválení.

    ![Schválit žádosti podokno](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

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
- [Schválit nebo zamítnout žádosti pro role adresáře Azure AD v PIM](azure-ad-pim-approval-workflow.md)
