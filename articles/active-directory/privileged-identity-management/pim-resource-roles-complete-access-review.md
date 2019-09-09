---
title: Dokončení kontroly přístupu pro role prostředků Azure v PIM-Azure Active Directory | Microsoft Docs
description: Naučte se, jak dokončit kontrolu přístupu k rolím prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b9563a4032011b999bf867fc782ba4cbb9c3fac
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804260"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-pim"></a>Dokončení kontroly přístupu pro role prostředků Azure v PIM
Správci privilegovaných rolí můžou po [spuštění kontroly přístupu](pim-resource-roles-start-access-review.md)zkontrolovat privilegovaný přístup. Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automaticky pošle e-mail s výzvou uživatelům, aby zkontrolovali přístup. Pokud uživatel neobdrží e-mail, můžete jim poslat pokyny, [Jak provést kontrolu přístupu](pim-resource-roles-perform-access-review.md).

Po uplynutí doby kontroly přístupu nebo po skončení vlastního přezkoumání všemi uživateli postupujte podle pokynů v tomto článku a spravujte kontrolu a podívejte se na výsledky.

## <a name="manage-access-reviews"></a>Správa kontrol přístupu
1. Přejděte na [Azure Portal](https://portal.azure.com/). Pak na řídicím panelu vyberte aplikaci **prostředků Azure** .

2. Vyberte prostředek.

3. Vyberte část kontroly **přístupu** na řídicím panelu.

    ![Prostředky Azure – seznam kontrol přístupu, který zobrazuje roli, vlastníka, datum zahájení, datum ukončení a stav](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Vyberte kontrolu přístupu, kterou chcete spravovat.

V okně podrobností kontroly přístupu je k dispozici několik možností pro správu této recenze. Možnosti jsou následující:

![Možnosti správy revize – zastavení, resetování, použití, odstranění](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Zastavit
Všechny kontroly přístupu mají koncové datum, ale k jeho dokončení můžete použít tlačítko **zastavit** . Všichni uživatelé, kteří nedokončili svou kontrolu v této době, ji nebudou moct dokončit po zastavení revize. Po zastavení nemůžete kontrolu znovu spustit.

### <a name="reset"></a>Obnovit
Chcete-li odebrat všechna rozhodnutí, která jsou v něm provedena, můžete obnovit kontrolu přístupu. Po obnovení kontroly přístupu budou všichni uživatelé označení jako nerevidováni. 

### <a name="apply"></a>Použít
Po dokončení kontroly přístupu použijte tlačítko **použít** k implementaci výsledku revize. Pokud byl přístup uživatele při revizi zamítnutý, tento krok odebere přiřazení role.  

### <a name="delete"></a>Odstranění
Pokud se vám žádné informace nejímá, odstraňte je. Tlačítko **Odstranit** odebere revizi z aplikace PIM.

## <a name="results"></a>Výsledky
Na stránce **výsledky** zobrazte seznam výsledků kontroly a Stáhněte si ho. 

![Stránka výsledků výpis uživatelů, výsledek, důvod, revize, uplatněno a výsledek použití](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Revidující
Zobrazení a přidání revidujících ke stávající kontrole přístupu. Připomenout kontrolorům, aby dokončili své recenze.

![Jméno a název zobrazení stránky revidujících a hlavní název uživatele](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Další postup

- [Zahájení kontroly přístupu pro role prostředků Azure v nástroji PIM](pim-resource-roles-start-access-review.md)
- [Provedení kontroly přístupu rolí prostředků Azure v nástroji PIM](pim-resource-roles-perform-access-review.md)
