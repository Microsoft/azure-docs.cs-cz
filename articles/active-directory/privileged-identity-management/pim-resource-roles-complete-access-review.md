---
title: Dokončení kontroly přístupu rolí prostředků Azure v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak ve službě Azure Active Directory dokončit kontrolu přístupu k rolím prostředků Azure Privileged Identity Management.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021986"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Dokončení kontroly přístupu rolí prostředků Azure v privilegované správě identit

Správci privilegovaných rolí mohou po [spuštění kontroly přístupu](pim-resource-roles-start-access-review.md)zkontrolovat privilegovaný přístup . Privilegovaná správa identit (PIM) ve službě Azure Active Directory (Azure AD) automaticky odešle e-mail, který vyzve uživatele ke kontrole jejich přístupu. Pokud uživatel neobdrží e-mail, můžete mu poslat [pokyny, jak provést kontrolu přístupu](pim-resource-roles-perform-access-review.md).

Po uplynutí období kontroly přístupu nebo po dokončení vlastní kontroly všemi uživateli postupujte podle pokynů v tomto článku a spravujte recenzi a podívejte se na výsledky.

## <a name="manage-access-reviews"></a>Správa kontrol přístupu

1. Přejděte na [portál Azure](https://portal.azure.com/). Na řídicím panelu vyberte službu **prostředků Azure.**

2. Vyberte svůj zdroj.

3. Vyberte část **Kontroly aplikace Access** na řídicím panelu.

    ![Prostředky Azure – seznam kontrol aplikace Access zobrazující roli, vlastníka, datum zahájení, datum ukončení a stav](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Vyberte kontrolu přístupu, kterou chcete spravovat.

Na stránce podrobností pro kontrolu přístupu existuje řada možností pro správu této kontroly. Možnosti jsou tyto:

![Možnosti správy recenze – Zastavit, Obnovit, Použít, Odstranit](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Zastavit

Všechny kontroly přístupu mají datum ukončení. Chcete-li ji dokončit dříve, vyberte **možnost Zastavit.** Uživatelé, kteří do této doby nedokončili svou recenzi, ji po zastavení recenze nebudou moci dokončit. Po zastavení recenze nelze recenzi restartovat.

### <a name="reset"></a>Resetovat

Můžete obnovit kontrolu přístupu a odebrat všechna rozhodnutí, která jsou na něm učiněna. Po resetování kontroly přístupu jsou všichni uživatelé označeni jako nezkontrolováni znovu.

### <a name="apply"></a>Použít

Po dokončení kontroly přístupu vyberte **použít** a implementujte výsledek kontroly. Pokud byl v recenzi odepřen přístup uživatele, odebere tento krok přiřazení jeho role.  

### <a name="delete"></a>Odstranění

Pokud už recenzi nechcete mít zájem, odstraňte ji. Vyberte **Odstranit** yo odebrat recenzi ze služby Správa privilegovaných identit.

## <a name="results"></a>Výsledky

Na stránce **Výsledky** zobrazte a stáhněte si seznam výsledků recenze.

![Stránka s výsledky se seznamem uživatelů, výsledků, důvodu, kontroly, použití a použití výsledku](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Recenzenti

Zobrazení a přidání recenzentů do stávající kontroly přístupu. Připomeňte recenzentům, aby dokončili své recenze.

![Název stránky recenzentů a hlavní jméno uživatele](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Další kroky

- [Spuštění kontroly přístupu pro role prostředků Azure ve správě privilegovaných identit](pim-resource-roles-start-access-review.md)
- [Provedení kontroly přístupu mých rolí prostředků Azure ve správě privilegovaných identit](pim-resource-roles-perform-access-review.md)
