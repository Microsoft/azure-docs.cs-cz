---
title: Dokončení kontroly přístupu Azure AD rolí v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Další informace o dokončení kontroly přístupu Azure AD rolí v Azure AD Privileged Identity Management (PIM) a zobrazit výsledky
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a7fa3bfe159620130bc0962b470cea8e7422646
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602160"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-pim"></a>Dokončení kontroly přístupu Azure AD rolí v PIM
Správci privilegovaných rolí jednou zkontrolovat privilegovaný přístup [se spustila kontrola přístupu](pim-how-to-start-security-review.md). Privileged Identity Management (PIM) ve Azure Active Directory (Azure AD) se automaticky pošle e-mail vyzývá uživatele, aby zkontrolujte svůj přístup. Pokud uživatel nezískaly v e-mailu, můžete jim poslat pokyny [jak k provádění kontroly přístupu](pim-how-to-perform-security-review.md).

Po období kontroly přístupu je nad nebo dokončení jejich svým přezkoumat všechny uživatele, postupujte podle kroků v tomto článku můžete spravovat revize a zobrazit výsledky.

## <a name="manage-access-reviews"></a>Správa kontroly přístupu
1. Přejděte [webu Azure portal](https://portal.azure.com/) a vyberte **Azure AD Privileged Identity Management** aplikace na řídicím panelu.
2. Vyberte **kontrol přístupu** část řídicího panelu.
3. Vyberte kontroly přístupu, kterou chcete spravovat.

V okně podrobností kontroly přístupu jsou čísla možnosti pro správu této revize.

![Tlačítka revize přístupu PIM – snímek obrazovky](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Připomenout
Pokud kontrola přístupu je nastavený tak, aby uživatele zkontrolujte, **připomenutí** tlačítko odešle oznámení. 

### <a name="stop"></a>Zastavit
Všechny kontroly přístupu mají koncové datum, ale můžete použít **Zastavit** tlačítko pro dokončení včas. Pokud v tuto chvíli ještě byly zkontrolovány všechny uživatele, nebudou moct po zastavení revize. Kontrola nelze restartovat po byla zastavena.

### <a name="apply"></a>Použít
Po dokončení kontroly přístupu, protože se dosáhlo koncové datum nebo ručně, je zastavena **použít** tlačítko implementuje výsledek kontroly. Pokud v revizi byl odepřen přístup uživatelů, jedná se krok, který se odebrat přiřazení role.  

### <a name="export"></a>Export
Pokud chcete použít výsledky kontroly přístupu ručně, můžete to taky revizi. **Exportovat** tlačítka se spustí stažení souboru CSV. Můžete spravovat výsledky v Excelu nebo jinými programy, které otevřete soubory CSV.

### <a name="delete"></a>Odstranění
Pokud si nejste zájem o další revizi, odstraňte ho. **Odstranit** tlačítko kontroly odebere z aplikace PIM.

> [!IMPORTANT]
> Nebudou vám upozornění, než dojde k odstranění, proto buďte Opravdu chcete odstranit tuto revizi. 

## <a name="next-steps"></a>Další postup

- [Zahájení kontroly přístupu pro role Azure AD v PIM](pim-how-to-start-security-review.md)
- [Provádění kontroly přístupu sady Moje role Azure AD v PIM](pim-how-to-perform-security-review.md)
