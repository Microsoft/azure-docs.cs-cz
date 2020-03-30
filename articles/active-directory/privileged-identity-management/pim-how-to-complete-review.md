---
title: Dokončení kontroly přístupu rolí Azure AD v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak dokončit kontrolu přístupu rolí Azure AD v azure ad privilegované správy identit (PIM) a zobrazit výsledky
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2d85d605b9ee418a5709ddcdb448c56be1d918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022281"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Dokončení kontroly přístupu rolí Azure AD ve správě privilegovaných identit

Správci privilegovaných rolí mohou po [spuštění kontroly přístupu](pim-how-to-start-security-review.md)zkontrolovat privilegovaný přístup .  Správa privilegovaných identit (PIM) automaticky odešle e-mail uživatelům ve vaší organizaci Azure Active Directory (Azure AD), ve které je vyzve ke kontrole jejich přístupu. Pokud uživatel neobdržel e-mail, můžete mu poslat [pokyny, jak provést kontrolu přístupu](pim-how-to-perform-security-review.md).

Po uplynutí období kontroly přístupu nebo po dokončení vlastní kontroly všichni uživatelé postupujte podle pokynů v tomto článku, abyste mohli recenzi spravovat a zobrazit výsledky.

## <a name="manage-access-reviews"></a>Správa kontrol přístupu

1. Přejděte na [portál Azure a](https://portal.azure.com/) vyberte službu Azure **AD Privileged Identity Management** na řídicím panelu.
1. Vyberte část **Kontroly aplikace Access** na řídicím panelu.
1. Vyberte kontrolu přístupu, kterou chcete spravovat.

Na podrobném okně kontroly přístupu existuje řada možností pro správu této recenze.

![Tlačítka pro kontrolu přístupu k privilegované správě identit – snímek obrazovky](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Připomenout

Pokud je kontrola přístupu nastavena tak, aby se uživatelé sami zkontrolovali, tlačítko **Připomenout** odešle oznámení.

### <a name="stop"></a>Zastavit

Všechny kontroly přístupu mají datum ukončení, ale můžete ho dokončit dříve pomocí tlačítka **Zastavit.** Pokud do této doby nebyli některá uživatelé zkontrolováni, po zastavení recenze nebudou moci. Po zastavení recenze nelze recenzi restartovat.

### <a name="apply"></a>Použít

Po dokončení kontroly přístupu, protože jste dosáhli koncového data nebo ji ručně zastavili, tlačítko **Použít** implementuje výsledek kontroly. Pokud byl v recenzi odepřen přístup uživatele, jedná se o krok, který odebere přiřazení jejich role.  

### <a name="export"></a>Export

Pokud chcete použít výsledky kontroly přístupu ručně, můžete recenzi exportovat. Tlačítko **Export** začne stahovat soubor CSV. Výsledky můžete spravovat v aplikaci Excel nebo v jiných aplikacích, které svírají soubory CSV.

### <a name="delete"></a>Odstranění

Pokud nemáte zájem o recenzi žádné další, odstraňte ji. Tlačítko **Odstranit** odebere recenzi ze služby Správa privilegovaných identit.

> [!IMPORTANT]
> Nebudete muset tuto destruktivní změnu potvrdit, proto ověřte, zda chcete tuto recenzi odstranit.

## <a name="next-steps"></a>Další kroky

- [Spuštění kontroly přístupu pro role Azure AD ve správě privilegovaných identit](pim-how-to-start-security-review.md)
- [Provedení kontroly přístupu mých rolí Azure AD ve správě privilegovaných identit](pim-how-to-perform-security-review.md)
