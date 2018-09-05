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
ms.openlocfilehash: 9402824540f965cb89aa00791d093bd87712a89a
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665838"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Schválit nebo zamítnout žádosti pro role adresáře Azure AD v PIM

S Azure AD Privileged Identity Management (PIM) můžete nakonfigurovat role, které vyžadují schválení pro aktivaci a zvolte jednu nebo více uživatelů nebo skupin jako delegovaný schvalovatele. Postupujte podle kroků v tomto článku pro schválení nebo zamítnutí žádosti pro role adresáře Azure AD.

## <a name="view-pending-requests"></a>Zobrazit čekající žádosti

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek pro role adresáře Azure AD čeká na schválení. Zobrazí se tyto žádosti čekající na vyřízení v PIM.

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

- [E-mailových oznámení v PIM](pim-email-notifications.md)
- [Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM](pim-resource-roles-approval-workflow.md)
