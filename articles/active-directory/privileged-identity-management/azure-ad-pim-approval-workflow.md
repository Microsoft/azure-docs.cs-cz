---
title: Schválení nebo zamítnutí žádostí o role Azure AD v PIM-Azure Active Directory | Microsoft Docs
description: Naučte se schvalovat nebo odmítat žádosti pro role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
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
ms.openlocfilehash: 60a8d373a7e6edeaefd933e4f8ec8ee11e3c14ee
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804040"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Schválení nebo zamítnutí žádostí o role Azure AD v PIM

V případě služby Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete nakonfigurovat role tak, aby vyžadovaly schválení aktivace, a vybrat jednoho nebo více uživatelů nebo skupin jako delegované schvalovatele. Delegovaní schvalovatelé mají ke schválení požadavků 24 hodin. Pokud žádost není schválena do 24 hodin, pak oprávněný uživatel musí znovu odeslat novou žádost. Časový interval schválení 24 hodin nelze konfigurovat.

Podle kroků v tomto článku můžete schvalovat nebo odmítat žádosti pro role Azure AD.

## <a name="view-pending-requests"></a>Zobrazit nevyřízené žádosti

Jako delegovaný schvalovatel obdržíte e-mailové oznámení, když požadavek role Azure AD čeká na vaše schválení. Tyto nedokončené žádosti můžete zobrazit v PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **role Azure AD**.

1. Klikněte na **schvalovat žádosti**.

    ![Role Azure AD – schvalování žádostí](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Zobrazí se seznam žádostí, které čekají na vaše schválení.

## <a name="approve-requests"></a>Schválit žádosti

1. Vyberte žádosti, které chcete schválit, a kliknutím na **schválit** otevřete podokno schválit vybrané žádosti.

    ![Seznam žádostí o schválení se zvýrazněnou možností schválit](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Do pole **důvod schválení** zadejte důvod.

    ![Schválit podokno vybrané žádosti s odůvodněním schválení](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klikněte na tlačítko **schválit**.

    Symbol stavu bude aktualizován na vaše schválení.

    ![Kliknutím na tlačítko Schválit podokno schválení vybraných žádostí](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Zamítnutí žádostí

1. Vyberte žádosti, které chcete odepřít, a kliknutím na **Odepřít** otevřete podokno odepřít vybrané žádosti.

    ![Seznam žádostí o schválení s zvýrazněnou možností odepřít](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Do pole **důvod zamítnutí** zadejte důvod.

    ![Odepřít podokno vybrané žádosti s odůvodněním odepřít](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Klikněte na **Odepřít**.

    Symbol stavu bude aktualizován o Vaše odmítnutí.

## <a name="next-steps"></a>Další postup

- [E-mailová oznámení v PIM](pim-email-notifications.md)
- [Schválení nebo zamítnutí žádostí o role prostředků Azure v PIM](pim-resource-roles-approval-workflow.md)
