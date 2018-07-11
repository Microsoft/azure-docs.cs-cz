---
title: Dokončení kontroly přístupu | Dokumentace Microsoftu
description: Po spuštění kontroly přístupu v Azure AD Privileged Identity Management, přečtěte si, jak dokončit a zobrazit výsledky
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: fc6376a7bf1abd81e7a5ff17549a7b4a193edec4
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950576"
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Dokončení kontroly přístupu v Azure AD Privileged Identity Management
Správci privilegovaných rolí můžete zkontrolovat privilegovaný přístup jednou [se spustila kontrola zabezpečení](active-directory-privileged-identity-management-how-to-start-security-review.md). Azure AD Privileged Identity Management (PIM) se automaticky pošle e-mail vyzývá uživatele, aby zkontrolujte svůj přístup. Pokud uživatel nezískaly v e-mailu, můžete jim poslat pokyny [provádění kontroly zabezpečení](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Po období kontroly zabezpečení nebo dokončení jejich svým přezkoumat všechny uživatele, postupujte podle kroků v tomto článku můžete spravovat revize a zobrazit výsledky.

## <a name="manage-security-reviews"></a>Spravovat revize zabezpečení
1. Přejděte [webu Azure portal](https://portal.azure.com/) a vyberte **Azure AD Privileged Identity Management** aplikace na řídicím panelu.
2. Vyberte **kontrol přístupu** část řídicího panelu.
3. Vyberte kontroly přístupu, kterou chcete spravovat.

V okně podrobností kontroly přístupu jsou čísla možnosti pro správu této revize.

![Tlačítka revize přístupu PIM – snímek obrazovky](./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png)

### <a name="remind"></a>Připomenout
Pokud kontrola přístupu je nastavený tak, aby uživatele zkontrolujte, **připomenutí** tlačítko odešle oznámení. 

### <a name="stop"></a>Zastavit
Všechny kontroly přístupu mají koncové datum, ale můžete použít **Zastavit** tlačítko pro dokončení včas. Pokud v tuto chvíli ještě byly zkontrolovány všechny uživatele, nebudou moct po zastavení revize. Kontrola nelze restartovat po byla zastavena.

### <a name="apply"></a>Použít
Po dokončení kontroly přístupu, protože se dosáhlo koncové datum nebo ručně, je zastavena **použít** tlačítko implementuje výsledek kontroly. Pokud v revizi byl odepřen přístup uživatelů, jedná se krok, který se odebrat přiřazení role.  

### <a name="export"></a>Export
Pokud chcete ručně použití výsledků kontroly zabezpečení, můžete to taky revizi. **Exportovat** tlačítka se spustí stažení souboru CSV. Můžete spravovat výsledky v Excelu nebo jinými programy, které otevřete soubory CSV.

### <a name="delete"></a>Odstranění
Pokud si nejste zájem o další revizi, odstraňte ho. **Odstranit** tlačítko kontroly odebere z aplikace PIM.

> [!IMPORTANT]
> Nebudou vám upozornění, než dojde k odstranění, proto buďte Opravdu chcete odstranit tuto revizi. 

## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
