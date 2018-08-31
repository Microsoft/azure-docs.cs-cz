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
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189154"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Schválit nebo zamítnout žádosti pro role adresáře Azure AD v PIM

Privileged Identity Management můžete konfigurovat role, které vyžadují schválení pro aktivaci a zvolte jednu nebo více uživatelů nebo skupin jako delegovaný schvalovatele.

## <a name="view-pending-approvals-requests"></a>Zobrazení čekajících na schválení (požadavků)

Jako delegovaný schvalovatel obdržíte e-mailová oznámení, když je požadavek čekající na vaše schválení. Chcete-li zobrazit tyto požadavky na portálu PIM, z řídicího panelu (v nové navigace) vyberte v levém navigačním panelu kartu "Čekající na vyřízení žádosti o schválení".

![](media/azure-ad-pim-approval-workflow/image023.png)

Tady se zobrazí seznam žádostí o schválení:

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>Schválit nebo odmítnout žádosti o zvýšení oprávnění role (jeden nebo hromadně)

Vyberte žádosti, na které chcete schválit nebo zamítnout a klikněte na tlačítko na panelu akcí, která odpovídá vaší rozhodnutí:

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>Uveďte její odůvodnění moje schválení nebo odmítnutí

Otevře se nové okno Schválit nebo zamítnout žádosti více najednou. Zadejte odůvodnění pro své rozhodnutí, a klikněte na tlačítko Schválit (nebo zakázat) v dolní části nebo v okně:

![](media/azure-ad-pim-approval-workflow/image029.png)

Po dokončení zpracování žádosti o symbolu stavu bude odrážet provedené rozhodnutí (v tomto příkladu rozhodnutí je schválit):

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>Další postup

- [Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM](pim-resource-roles-approval-workflow.md)
- [E-mailových oznámení v PIM](pim-email-notifications.md)
