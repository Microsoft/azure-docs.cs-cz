---
title: Dokončení kontroly přístupu pro role prostředků Azure v PIM – Azure AD | Microsoft Docs
description: Naučte se, jak dokončit kontrolu přístupu k rolím prostředků Azure Privileged Identity Management v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74021986"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Dokončení kontroly přístupu pro role prostředků Azure v Privileged Identity Management

Správci privilegovaných rolí můžou po [spuštění kontroly přístupu](pim-resource-roles-start-access-review.md)zkontrolovat privilegovaný přístup. Privileged Identity Management (PIM) v Azure Active Directory (Azure AD) automaticky pošle e-mail s výzvou uživatelům, aby zkontrolovali přístup. Pokud uživatel neobdrží e-mail, můžete jim poslat pokyny, [Jak provést kontrolu přístupu](pim-resource-roles-perform-access-review.md).

Po uplynutí doby kontroly přístupu nebo po skončení vlastního přezkoumání všemi uživateli postupujte podle pokynů v tomto článku a spravujte kontrolu a podívejte se na výsledky.

## <a name="manage-access-reviews"></a>Správa kontrol přístupu

1. Přejít na [Azure Portal](https://portal.azure.com/). Na řídicím panelu vyberte službu **prostředků Azure** .

2. Vyberte prostředek.

3. Vyberte část kontroly **přístupu** na řídicím panelu.

    ![Prostředky Azure – seznam kontrol přístupu, který zobrazuje roli, vlastníka, datum zahájení, datum ukončení a stav](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Vyberte kontrolu přístupu, kterou chcete spravovat.

Na stránce podrobností pro kontrolu přístupu existuje několik možností pro správu této recenze. Možnosti jsou tyto:

![Možnosti správy revize – zastavení, resetování, použití, odstranění](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Zastavit

Všechny recenze přístupů mají datum ukončení. Vyberte **zastavit** , aby se dokončilo předčasné dokončení. Všichni uživatelé, kteří nedokončili svou kontrolu v této době, ji nebudou moci dokončit po zastavení revize. Po zastavení nemůžete kontrolu znovu spustit.

### <a name="reset"></a>Resetovat

Chcete-li odebrat všechna rozhodnutí, která jsou v něm provedena, můžete obnovit kontrolu přístupu. Po obnovení kontroly přístupu budou všichni uživatelé označeni jako nekontrolované.

### <a name="apply"></a>Použít

Po dokončení kontroly přístupu vyberte **použít** pro implementaci výsledku revize. Pokud byl přístup uživatele při revizi zamítnutý, tento krok odebere přiřazení role.  

### <a name="delete"></a>Odstranit

Pokud se vám žádné informace nejímá, odstraňte je. Vyberte možnost **Odstranit** Jo odebrat revizi ze služby Privileged Identity Management.

## <a name="results"></a>Výsledky

Na stránce **výsledky** zobrazte seznam výsledků kontroly a Stáhněte si ho.

![Stránka výsledků výpis uživatelů, výsledek, důvod, revize, uplatněno a výsledek použití](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Recenzenti

Zobrazení a přidání revidujících ke stávající kontrole přístupu. Připomenout kontrolorům, aby dokončili své recenze.

![Jméno a název zobrazení stránky revidujících a hlavní název uživatele](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Další kroky

- [Spuštění kontroly přístupu pro role prostředků Azure v Privileged Identity Management](pim-resource-roles-start-access-review.md)
- [Provedení kontroly přístupu mých rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-perform-access-review.md)
