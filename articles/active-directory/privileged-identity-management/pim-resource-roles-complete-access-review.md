---
title: Dokončit kontrola přístupu k prostředkům Azure pomocí Privileged Identity managementu | Microsoft Docs
description: Popisuje, jak provést kontrola přístupu k prostředkům Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: ae64d9ebbca80f6c21b8c7f352022a0878518e65
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="complete-an-access-review-for-azure-resources-by-using-privileged-identity-management"></a>Dokončit kontrola přístupu k prostředkům Azure pomocí Privileged Identity Management
Správce privilegovaných rolí můžete zkontrolovat privilegovaného přístupu po [revize zabezpečení byla spuštěna](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) k prostředkům Azure automaticky odešle e-mail, který vyzve uživatele, aby kontrolovat jejich přístup. Pokud uživatel nebude přijímat e-mailu, můžete jim poslat pokynů [jak provádět kontrolu zabezpečení](pim-resource-roles-perform-access-review.md).

Po dokončení zkontrolujte období zabezpečení nebo po dokončení své samoobslužné zkontrolujte všechny uživatele, postupujte podle kroků v tomto článku ke správě kontrola a zobrazit výsledky.

## <a name="manage-security-reviews"></a>Správa zabezpečení recenze
1. Přejděte na [portál Azure](https://portal.azure.com/). Na řídicím panelu, vyberte **prostředky Azure** aplikace.

2. Vyberte prostředek.

3. Vyberte **přístup recenze** část řídicího panelu.
![Recenze přístup](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Vyberte kontrola přístupu, který chcete spravovat.

V okně podrobností kontrola přístupu existuje několik možností pro správu této revize. Možnosti jsou následující:

![Možnosti pro správu kontrolu](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Zastavit
Všechny recenze přístup mít koncové datum, ale můžete použít **Zastavit** tlačítko ukončíte již v rané fázi. Všichni uživatelé, kteří nedokončily jejich revize této doby nebude možné dokončit, je poté, co zastavíte kontrola. Kontrolu nelze restartovat po byla zastavena.

### <a name="reset"></a>Resetovat
Můžete resetovat kontrola přístupu k odebrání všech rozhodnutí, která jsou probíhají. Po resetujete kontrola přístupu, všichni uživatelé jsou označeny jako nerevidované znovu. 

### <a name="apply"></a>Použít
Po dokončení kontrola přístupu používat **použít** tlačítko implementovat výsledek kontrola. Pokud v kontrola byl odepřen přístup uživatele, tento krok Odstraní přiřazení role.  

### <a name="delete"></a>Odstranění
Pokud máte nejsou zájem o kontrola víc, odstraňte jej. **Odstranit** tlačítko odstraní kontrola z aplikace PIM.

## <a name="results"></a>Výsledky
Na **výsledky** kartě, zobrazení a stažení seznamu zobrazit výsledky. 
![Karta výsledky](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Revidující
Zobrazení a přidejte kontroloři do vaší stávající kontrola přístupu. Připomeňte kontroloři dokončit jejich recenze.
![Přidání revidujících](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



