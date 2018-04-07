---
title: Pro prostředky Azure – kontrola dokončení přístupu pro prostředky Azure privileged Identity Management | Microsoft Docs
description: Popisuje postup dokončete kontrola přístupu pro prostředky Azure.
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
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Kontrola přístupu privileged Identity Management - prostředků role - dokončit
Správce privilegovaných rolí můžete zkontrolovat jednou privilegovaného přístupu [revize zabezpečení byla spuštěna](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) pro prostředky Azure bude automaticky posílat vyzývá uživatele, aby kontrolovat jejich přístup k e-mailu. Pokud uživatel neobdrželi e-mailu, můžete odeslat je podle pokynů [jak provádět kontrolu zabezpečení](pim-resource-roles-perform-access-review.md).

Po dobu kontrola zabezpečení je nad nebo dokončení jejich samoobslužné zkontrolujte všechny uživatele, postupujte podle kroků v tomto článku ke správě kontrola a zobrazit výsledky.

## <a name="manage-security-reviews"></a>Správa zabezpečení recenze
1. Přejděte na [portál Azure](https://portal.azure.com/) a vyberte **prostředky Azure** aplikace na řídicím panelu.
2. Vyberte prostředek.
3. Vyberte **přístup recenze** část řídicího panelu.
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. Vyberte kontrola přístupu, který chcete spravovat.

V okně podrobností kontrola přístupu existuje několik možností pro správu této revize.
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Zastavit
Všechny recenze přístup mít koncové datum, ale můžete použít **Zastavit** tlačítko ukončíte již v rané fázi. Pokud žádné uživatele nebyly revidován té doby, nebudou moci po ukončení kontrola. Kontrolu nelze restartovat po byla zastavena.

### <a name="reset"></a>Resetovat
Kontrola přístupu k odebrání všech rozhodnutí v něm můžete resetovat. Jakmile nastavíte kontrola přístupu, všichni uživatelé jsou označeny jako nerevidované znovu. 

### <a name="apply"></a>Použít
Po dokončení kontrola přístupu buď protože bylo dosaženo maximálního koncové datum nebo byla ručně zastavena **použít** tlačítko implementuje výsledek kontrola. Pokud v kontrola byl odepřen přístup uživatele, je to krok, který odstraní přiřazení role.  

### <a name="delete"></a>Odstranění
Pokud si nejste zájem o žádné další kontrolní, odstraňte jej. **Odstranit** tlačítko odstraní kontrola z aplikace PIM.

## <a name="results"></a>Výsledky
Zobrazit a stáhnout seznam výsledků zkontrolujte na kartě výsledky. ![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Revidující
Zobrazení a přidejte kontroloři do vaší stávající kontrola přístupu. Připomeňte kontroloři dokončit jejich recenze.
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



