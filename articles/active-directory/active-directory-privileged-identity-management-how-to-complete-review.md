---
title: Jak provést kontrola přístupu | Microsoft Docs
description: Po spuštění kontrola přístupu v Azure AD Privileged Identity Management zjistěte, jak dokončit ho a zobrazení výsledků
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b026a1fce61cfbf6469c8ef8140813906beba5f7
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234319"
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Jak provést kontrola přístupu v Azure AD Privileged Identity Management
Správce privilegovaných rolí můžete zkontrolovat jednou privilegovaného přístupu [revize zabezpečení byla spuštěna](active-directory-privileged-identity-management-how-to-start-security-review.md). Azure AD Privileged Identity Management (PIM), bude automaticky posílat vyzývá uživatele, aby kontrolovat jejich přístup k e-mailu. Pokud uživatel neobdrželi e-mailu, můžete odeslat je podle pokynů [jak provádět kontrolu zabezpečení](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Po dobu kontrola zabezpečení je nad nebo dokončení jejich samoobslužné zkontrolujte všechny uživatele, postupujte podle kroků v tomto článku ke správě kontrola a zobrazit výsledky.

## <a name="manage-security-reviews"></a>Správa zabezpečení recenze
1. Přejděte na [portál Azure](https://portal.azure.com/) a vyberte **Azure AD Privileged Identity Management** aplikace na řídicím panelu.
2. Vyberte **přístup recenze** část řídicího panelu.
3. Vyberte kontrola přístupu, který chcete spravovat.

V okně podrobností kontrola přístupu jsou číslo možností pro správu této revize.

![PIM přístupu zkontrolujte tlačítka – snímek obrazovky][1]

### <a name="remind"></a>Připomenout
Pokud je kontrola přístupu nastavený tak, aby uživatelé sami, zkontrolujte **připomenutí** tlačítko odesílá oznámení. 

### <a name="stop"></a>Zastavit
Všechny recenze přístup mít koncové datum, ale můžete použít **Zastavit** tlačítko ukončíte již v rané fázi. Pokud žádné uživatele nebyly revidován té doby, nebudou moci po ukončení kontrola. Kontrolu nelze restartovat po byla zastavena.

### <a name="apply"></a>Použít
Po dokončení kontrola přístupu buď protože bylo dosaženo maximálního koncové datum nebo byla ručně zastavena **použít** tlačítko implementuje výsledek kontrola. Pokud v kontrola byl odepřen přístup uživatele, je to krok, který odstraní přiřazení role.  

### <a name="export"></a>Export
Pokud chcete použít výsledků revize zabezpečení ručně, můžete exportovat kontrola. **Exportovat** tlačítko se spustí stahování souboru CSV. Můžete spravovat výsledky v aplikaci Excel nebo programy, které soubory sdíleného svazku clusteru.

### <a name="delete"></a>Odstranění
Pokud si nejste zájem o žádné další kontrolní, odstraňte jej. **Odstranit** tlačítko odstraní kontrola z aplikace PIM.

> [!IMPORTANT]
> Nebude zobrazí upozornění, než dojde k odstranění, proto se ujistěte, že chcete odstranit tento kontrolní. 

## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
