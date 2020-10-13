---
title: Dokončení kontroly přístupu rolí Azure AD v PIM – Azure AD | Microsoft Docs
description: Naučte se, jak dokončit kontrolu přístupu pro role Azure AD v Azure AD Privileged Identity Management (PIM) a zobrazit výsledky.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa7e8089fbbf2ee653100a05383fdbdc877ffda4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84742212"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Dokončení kontroly přístupu rolí Azure AD v Privileged Identity Management

Správci privilegovaných rolí můžou po [spuštění kontroly přístupu](pim-how-to-start-security-review.md)zkontrolovat privilegovaný přístup.  Privileged Identity Management (PIM) automaticky pošle uživatelům v organizaci Azure Active Directory (Azure AD) e-mail s výzvou, aby zkontroloval svůj přístup. Pokud uživatel neobdržel e-mail, můžete jim poslat pokyny, [Jak provést kontrolu přístupu](pim-how-to-perform-security-review.md).

Po uplynutí doby kontroly přístupu nebo všech uživatelích, kteří si dokončili vlastní kontrolu, postupujte podle kroků v tomto článku ke správě kontroly a zobrazení výsledků.

## <a name="manage-access-reviews"></a>Správa kontrol přístupu

1. Přejít na [Azure Portal](https://portal.azure.com/) a na řídicím panelu vyberte službu **Azure AD Privileged Identity Management** .
1. Vyberte část kontroly **přístupu** na řídicím panelu.
1. Vyberte kontrolu přístupu, kterou chcete spravovat.

V okně podrobností kontroly přístupu je k dispozici několik možností pro správu této recenze.

![Tlačítka pro kontrolu přístupu Privileged Identity Management – snímek obrazovky](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Abyste

Pokud je kontrola přístupu nastavená tak, aby se uživatelé zkontrolovali, tlačítko **připomenout** pošle oznámení.

### <a name="stop"></a>Zastavit

Všechny kontroly přístupu mají koncové datum, ale k jeho dokončení můžete použít tlačítko **zastavit** . Pokud nějaký uživatel nebyl tímto časem zkontrolován, nebude po zastavení revize možné. Po zastavení nemůžete kontrolu restartovat.

### <a name="apply"></a>Použít

Po dokončení kontroly přístupu buď protože jste dosáhli koncového data nebo ho zastavili ručně, tlačítko **použít** provede implementaci výsledku revize. Pokud se k přístupu uživatele v recenzi zamítl přístup, jedná se o krok, který odebere přiřazení role.  

### <a name="export"></a>Export

Pokud chcete výsledky kontroly přístupu použít ručně, můžete tuto kontrolu exportovat. Tlačítko **exportovat** spustí stahování souboru CSV. Výsledky můžete spravovat v Excelu nebo v jiných programech, které otevřou soubory CSV.

### <a name="delete"></a>Odstranit

Pokud si to ještě zajímáte, odstraňte ho. Tlačítko **Odstranit** odebere kontrolu z Privileged Identity Management služby.

> [!IMPORTANT]
> Nebudete se muset ujistit, že se tato destruktivní změna bude vyžadovat, a pak ověřte, že chcete tuto recenzi odstranit.

## <a name="next-steps"></a>Další kroky

- [Spuštění kontroly přístupu pro role Azure AD v Privileged Identity Management](pim-how-to-start-security-review.md)
- [Provedení kontroly přístupu mých rolí Azure AD v Privileged Identity Management](pim-how-to-perform-security-review.md)
