---
title: Kontrola přístupu k rolím prostředků Azure v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak zkontrolovat přístup k rolím prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847006"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Kontrola přístupu k rolím prostředků Azure ve správě privilegovaných identit

Kontroly přístupu k privilegované správě identit (PIM) můžou pomoct zabezpečit přístup k privilegovaným rolím ve službě Azure Active Directory (Azure AD). Tento článek k provedení kroky k dokončení revize přiřazení privilegované role v kontrole přístupu Azure AD.

Pokud jste přiřazeni k roli správce, může být nutné provést kontrolu přístupu správcem, abyste potvrdili potřebu role. Žádost o potvrzení může přijít e-mail, který obsahuje odkaz, nebo můžete potvrdit na [webu Azure Portal](https://portal.azure.com).

Pokud jste správce privilegovaných rolí, který má zájem o kontroly přístupu, přečtěte si další podrobnosti v části [Jak zahájit kontrolu přístupu](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Schválení nebo odepření přístupu

Můžete schválit nebo odepřít přístup na základě toho, zda tuto roli stále používáte nebo ne. Pokud chcete v roli zůstat, zvolte **Schválit,** nebo **Odepřít,** pokud už přístup nepotřebujete. Váš stav se změní až poté, co recenzent použije výsledky.

Chcete-li vyhledat a dokončit kontrolu přístupu, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Vyberte **Službu Azure Active Directory** a otevřete **správu privilegovaných identit**.
1. Vyberte **možnost Zkontrolovat přístup**.

   ![Snímek obrazovky aplikace Privileged Identity Management s vybranou možností Zkontrolovat přístup](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Vyberte recenzi, kterou chcete dokončit.
1. Zvolte **Schválit** nebo **Odepřít**. Do **pole Zadejte důvod**, v případě potřeby zadejte obchodní odůvodnění svého rozhodnutí.

   ![Snímek obrazovky se stránkou Podrobnosti o recenzi](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Další kroky

- [Provedení kontroly přístupu mých rolí Azure AD ve správě privilegovaných identit](pim-how-to-perform-security-review.md)
