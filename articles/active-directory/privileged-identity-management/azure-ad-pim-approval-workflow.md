---
title: Schválit nebo zamítnout žádosti pro role adresáře Azure AD v PIM | Dokumentace Microsoftu
description: Naučte se schválit nebo zamítnout žádosti pro role adresáře Azure AD v Azure AD Privileged Identity Management (PIM).
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
ms.component: pim
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: bc60c0dbe6c609a08907feb243fe514badfe3e4e
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43288071"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Schválit nebo zamítnout žádosti pro role adresáře Azure AD v PIM

S Azure AD Privileged Identity Management (PIM) můžete nakonfigurovat role, které vyžadují schválení pro aktivaci a zvolte jednu nebo více uživatelů nebo skupin jako delegovaný schvalovatele.

## <a name="view-pending-requests"></a>Zobrazit čekající žádosti

Jako delegovaný schvalovatel obdržíte e-mailová oznámení, když je požadavek čekající na vaše schválení. Zobrazí se tyto žádosti čekající na vyřízení v PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **role adresáře Azure AD**.

1. Klikněte na tlačítko **schvalovat žádosti o**.

    ![Role adresáře PIM Azure AD - role](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

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

- [Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM](pim-resource-roles-approval-workflow.md)
- [E-mailových oznámení v PIM](pim-email-notifications.md)
