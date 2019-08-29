---
title: Jak odblokovat uživatele pomocí Azure Active Directory Identity Protection | Microsoft Docs
description: Přečtěte si, jak odblokovat uživatele blokované zásadou Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e3756435703c4e8c887a4e7b9d4f75a6701d840
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126254"
---
# <a name="how-to-unblock-users"></a>Jak: Odblokování uživatelů

Pomocí Azure Active Directory Identity Protection můžete nakonfigurovat zásady, které blokují uživatele, pokud jsou nakonfigurované podmínky splněné. Obvykle se zablokování kontaktování kontaktů blokovaného uživatele stane odblokované. Tento článek popisuje kroky, které můžete provést k odblokování blokovaného uživatele.

## <a name="determine-the-reason-for-blocking"></a>Určete důvod blokování.

Jako první krok odblokování uživatele musíte určit typ zásady, která uživatele zablokovala, protože na něm závisí další postup.
Pomocí Azure Active Directory Identity Protection může uživatel zablokovat buď zásady rizik přihlašování, nebo zásady rizik uživatelů.

Můžete získat typ zásad, které zablokovaly uživatele z nadpisu v dialogovém okně, které se uživateli zobrazily během pokusu o přihlášení:

| Zásada | Uživatelský dialog |
| --- | --- |
| Riziko přihlášení |![Blokované přihlášení](./media/howto-unblock-user/02.png) |
| Riziko uživatele |![Blokovaný účet](./media/howto-unblock-user/104.png) |

Uživatel, který je zablokován nástrojem:

* Zásady pro rizikové přihlašování se taky označují jako podezřelé přihlášení.
* Zásady rizik uživatelů se také označují jako ohrožený účet.

## <a name="unblocking-suspicious-sign-ins"></a>Odblokování podezřelých přihlášení

Chcete-li odblokovat podezřelé přihlášení, máte následující možnosti:

1. **Přihlaste se ze známého umístění nebo zařízení** – běžným důvodem pro blokované podezřelé přihlášení jsou pokusy o přihlášení z neznámých umístění nebo zařízení. Uživatelé mohou rychle zjistit, zda se jedná o důvod blokování, pokusit se o přihlášení ze známého umístění nebo zařízení.
2. **Vyloučit ze zásad** – Pokud se domníváte, že aktuální konfigurace zásady přihlašování způsobuje problémy pro konkrétní uživatele, můžete z něj vyloučit uživatele. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Zakázat zásady** – Pokud se domníváte, že konfigurace zásad způsobuje problémy pro všechny uživatele, můžete zásady zakázat. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Odblokování ohrožených účtů

Chcete-li odblokovat účet s rizikem, máte následující možnosti:

1. **Resetování hesla** – můžete resetovat heslo uživatele. 
2. **Zrušit všechny detekce rizik** – zásady rizik uživatelů blokují uživatele, pokud byla dosažena nakonfigurovaná úroveň rizika uživatele pro blokování přístupu. Úroveň rizika uživatele můžete snížit ručním uzavřením hlášených zjištění rizik. 
3. **Vyloučit ze zásad** – Pokud se domníváte, že aktuální konfigurace zásady přihlašování způsobuje problémy pro konkrétní uživatele, můžete z něj vyloučit uživatele. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Zakázat zásady** – Pokud se domníváte, že konfigurace zásad způsobuje problémy pro všechny uživatele, můžete zásady zakázat. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Další kroky
 
Chcete získat další informace o Azure AD Identity Protection? Podívejte se [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
