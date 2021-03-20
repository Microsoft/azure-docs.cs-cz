---
title: Kontrola přístupu k rolím prostředků Azure v PIM – Azure AD | Microsoft Docs
description: Přečtěte si, jak zkontrolovat přístup k rolím prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8091890d174ae68f012a6ec24685a0e705100f8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743673"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Kontrola přístupu k rolím prostředků Azure v Privileged Identity Management

Kontroly přístupu Privileged Identity Management (PIM) můžou přispět k zabezpečení přístupu k privilegovaným rolím v Azure Active Directory (Azure AD). V tomto článku najdete postup dokončení kontroly přiřazení privilegovaných rolí v rámci kontroly přístupu Azure AD.

Pokud jste přihlášeni k roli správce, může být nutné dokončit kontrolu přístupu vaším správcem, aby potvrdil potřebnou roli. Žádost o potvrzení může obsahovat e-mail, který obsahuje odkaz, nebo můžete potvrdit v [Azure Portal](https://portal.azure.com).

Pokud jste správce privilegovaných rolí, který zajímá kontroly přístupu, získáte další podrobnosti o [tom, jak spustit kontrolu přístupu](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Schválit nebo zamítnout přístup

Můžete schválit nebo odepřít přístup na základě toho, jestli tuto roli pořád používáte. Vyberte **schválit** , pokud chcete zůstat v roli, nebo **zakažte** , pokud už nepotřebujete přístup. Stav se změní až po tom, co Kontrolor použije výsledky.

Pomocí těchto kroků můžete najít a dokončit kontrolu přístupu:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Vyberte **Azure Active Directory** a otevřete **Privileged Identity Management**.
1. Vyberte **zkontrolovat přístup**.

   ![Snímek obrazovky aplikace Privileged Identity Management s vybraným oknem kontrola přístupu](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Vyberte revizi, kterou chcete dokončit.
1. Vyberte **schválit** nebo **Odepřít**. V případě potřeby v poli **Zadejte důvod** zadejte obchodní odůvodnění.

   ![Snímek obrazovky se stránkou s podrobnostmi o kontrole](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Další kroky

- [Provedení kontroly přístupu mých rolí Azure AD v Privileged Identity Management](pim-how-to-perform-security-review.md)
