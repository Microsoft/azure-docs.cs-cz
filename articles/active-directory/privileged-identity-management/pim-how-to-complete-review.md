---
title: Dokončení kontroly přístupu rolí Azure AD v PIM-Azure Active Directory | Microsoft Docs
description: Naučte se, jak dokončit kontrolu přístupu pro role Azure AD v Azure AD Privileged Identity Management (PIM) a zobrazit výsledky.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/06/2017
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50ccc208219896e89bcc80f40c846f69c759f9b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804398"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-pim"></a>Dokončení kontroly přístupu rolí Azure AD v PIM
Správci privilegovaných rolí můžou po [spuštění kontroly přístupu](pim-how-to-start-security-review.md)zkontrolovat privilegovaný přístup. Azure Active Directory (Azure AD) Privileged Identity Management (PIM) automaticky pošle e-mail s výzvou, aby si uživatelé zkontrolovali přístup. Pokud uživatel neobdržel e-mail, můžete jim poslat pokyny, [Jak provést kontrolu přístupu](pim-how-to-perform-security-review.md).

Po uplynutí doby kontroly přístupu nebo všech uživatelích, kteří si dokončili vlastní kontrolu, postupujte podle kroků v tomto článku ke správě kontroly a zobrazení výsledků.

## <a name="manage-access-reviews"></a>Správa kontrol přístupu
1. Přejít na [Azure Portal](https://portal.azure.com/) a na řídicím panelu vyberte aplikaci **Azure AD Privileged Identity Management** .
2. Vyberte část kontroly **přístupu** na řídicím panelu.
3. Vyberte kontrolu přístupu, kterou chcete spravovat.

V okně podrobností kontroly přístupu je k dispozici několik možností pro správu této recenze.

![Tlačítka pro kontrolu přístupu PIM – snímek obrazovky](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Připomenout
Pokud je kontrola přístupu nastavená tak, aby se uživatelé zkontrolovali, tlačítko **připomenout** pošle oznámení. 

### <a name="stop"></a>Zastavit
Všechny kontroly přístupu mají koncové datum, ale k jeho dokončení můžete použít tlačítko **zastavit** . Pokud nějaký uživatel nebyl tímto časem zkontrolován, nebude po zastavení revize možné. Po zastavení nemůžete kontrolu restartovat.

### <a name="apply"></a>Použít
Po dokončení kontroly přístupu buď protože jste dosáhli koncového data nebo ho zastavili ručně, tlačítko **použít** provede implementaci výsledku revize. Pokud se k přístupu uživatele v recenzi zamítl přístup, jedná se o krok, který odebere přiřazení role.  

### <a name="export"></a>Export
Pokud chcete výsledky kontroly přístupu použít ručně, můžete tuto kontrolu exportovat. Tlačítko **exportovat** spustí stahování souboru CSV. Výsledky můžete spravovat v Excelu nebo v jiných programech, které otevřou soubory CSV.

### <a name="delete"></a>Odstranění
Pokud si to ještě zajímáte, odstraňte ho. Tlačítko **Odstranit** odebere revizi z aplikace PIM.

> [!IMPORTANT]
> Dokud nedojde k odstranění, nebudete se zobrazovat upozornění, proto je potřeba tuto recenzi odstranit. 

## <a name="next-steps"></a>Další postup

- [Spuštění kontroly přístupu pro role Azure AD v PIM](pim-how-to-start-security-review.md)
- [Provedení kontroly přístupu mých rolí Azure AD v PIM](pim-how-to-perform-security-review.md)
