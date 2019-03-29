---
title: Schválit nebo zamítnout žádosti pro role Azure AD v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Naučte se schválit nebo zamítnout žádosti pro role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 125a9864eaf53ecdf035247ba23d7c95a8daf131
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576790"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Schválit nebo zamítnout žádosti pro role Azure AD v PIM

S Azure Active Directory (Azure AD) Privileged Identity Management (PIM), můžete konfigurovat role, které vyžadují schválení pro aktivaci a zvolte jednu nebo více uživatelů nebo skupin jako delegovaný schvalovatele. Delegované schvalovatelů mít 24 hodin se schválit žádosti. Pokud žádost není schválený během 24 hodin, oprávněný uživatel musí znovu odešlete novou žádost. Časový interval 24 hodin schválení není konfigurovatelné.

Postupujte podle kroků v tomto článku pro schválení nebo zamítnutí žádosti pro role Azure AD.

## <a name="view-pending-requests"></a>Zobrazit čekající žádosti

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek role Azure AD čeká na schválení. Zobrazí se tyto žádosti čekající na vyřízení v PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **role Azure AD**.

1. Klikněte na tlačítko **schvalovat žádosti o**.

    ![Role AD PIM Azure – role](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Zobrazí se vám seznam žádosti čekající na vaše schválení.

## <a name="approve-requests"></a>Schvalování žádostí

1. Vyberte žádosti, které chcete schválit a potom klikněte na tlačítko **schválit** otevřete schválit vybrané žádosti podokně.

    ![Seznam požadavků schválit PIM](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. V **důvod pro schválení** zadejte důvod.

    ![PIM schválit vybrané žádosti](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klikněte na tlačítko **schválit**.

    Symbol stavu aktualizuje vaše schválení.

    ![PIM schválit vybrané žádosti](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Zamítnout žádosti

1. Vyberte žádosti, které chcete zamítnout a pak klikněte na tlačítko **Odepřít** otevřete odepřít vybrané žádosti podokně.

    ![Seznam požadavků schválit PIM](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. V **důvod pro zamítnutí** zadejte důvod.

    ![PIM zamítnout vybrané žádosti](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Klikněte na tlačítko **Odepřít**.

    Symbol stavu aktualizuje vaše odmítnutí.

## <a name="next-steps"></a>Další postup

- [E-mailových oznámení v PIM](pim-email-notifications.md)
- [Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM](pim-resource-roles-approval-workflow.md)
