---
title: Dokončení kontroly přístupu z role prostředků Azure v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak dokončení kontroly přístupu z role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: aee8ac3c2638ede559f8a1f9c51f2d6e62604998
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602307"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-pim"></a>Dokončení kontroly přístupu z role prostředků Azure v PIM
Správci privilegovaných rolí můžete zkontrolovat privilegovaný přístup po [se spustila kontrola přístupu](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) ve Azure Active Directory (Azure AD) automaticky odesílá e-mail, který vyzve uživatele ke kontrole jejich přístupu. Pokud uživatel e-mailu, můžete jim poslat pokyny [jak k provádění kontroly přístupu](pim-resource-roles-perform-access-review.md).

Po období kontroly přístupu, nebo po dokončení jejich svým přezkoumat všechny uživatele, postupujte podle kroků v tomto článku můžete spravovat revize a zobrazit výsledky.

## <a name="manage-access-reviews"></a>Správa kontroly přístupu
1. Přejděte na [Azure Portal](https://portal.azure.com/). Vyberte na řídicím panelu **prostředky Azure** aplikace.

2. Vyberte prostředek.

3. Vyberte **kontrol přístupu** část řídicího panelu.
![Kontroly přístupu](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Vyberte kontroly přístupu, kterou chcete spravovat.

V okně Podrobnosti o kontrolu přístupu existuje mnoho možností pro správu této revize. Možnosti jsou následující:

![Možnosti pro správu revize](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Ukončit
Všechny kontroly přístupu mají koncové datum, ale můžete použít **Zastavit** tlačítko pro dokončení včas. Všichni uživatelé, kteří nedokončili svoji kontrolu té doby nebude možné ji dokončit po zastavení revize. Kontrola nelze restartovat po byla zastavena.

### <a name="reset"></a>Obnovit
Kontroly přístupu k odebrání všech rozhodnutí, které jsou provedeny v něm můžete resetovat. Poté, co jste změnili kontroly přístupu, všichni uživatelé jsou označeny jako znovu neprovedla kontrola. 

### <a name="apply"></a>Použít
Po dokončení kontroly přístupu používat **použít** tlačítko k implementaci výsledek kontroly. Pokud v revizi byl odepřen přístup uživatelů, tento krok Odstraní přiřazení role.  

### <a name="delete"></a>Odstranit
Můžete se v případě zájmu revize víc, odstraňte jej. **Odstranit** tlačítko kontroly odebere z aplikace PIM.

## <a name="results"></a>Výsledky
Na **výsledky** kartu, zobrazit a stáhnout seznam výsledků kontroly. 
![Karta výsledky](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Revidující
Zobrazení a přidání revidujících k vaší existující kontrolu přístupu. Připomeňte revidujících k dokončení jejich revize.
![Přidat kontrolory](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Další postup

- [Zahájení kontroly přístupu pro role prostředků Azure v nástroji PIM](pim-resource-roles-start-access-review.md)
- [Provedení kontroly přístupu rolí prostředků Azure v nástroji PIM](pim-resource-roles-perform-access-review.md)
