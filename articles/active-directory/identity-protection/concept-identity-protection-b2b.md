---
title: Ochrana identit a uživatelé B2B – Azure Active Directory
description: Používání ochrany identity s uživateli B2B, jak funguje, a známých omezení
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95ea7eb470a5880bc88b3df903d33854f363e974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72881314"
---
# <a name="identity-protection-and-b2b-users"></a>Ochrana identit a uživatelé B2B

Díky spolupráci Azure AD B2B můžou organizace vynucovat zásady založené na rizicích pro uživatele B2B pomocí ochrany identity. Tyto zásady lze nakonfigurovat dvěma způsoby:

- Správci mohou nakonfigurovat integrované zásady ochrany identity založené na rizicích, které se vztahují na všechny aplikace, které zahrnují uživatele typu Host.
- Správci mohou nakonfigurovat své zásady podmíněného přístupu pomocí rizika přihlášení jako podmínky, která zahrnuje uživatele typu Host.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Jak se hodnotí riziko pro uživatele b2B spolupráce

Uživatelské riziko pro uživatele spolupráce B2B je vyhodnocováno v jejich domovském adresáři. Riziko přihlášení v reálném čase pro tyto uživatele je vyhodnocováno v adresáři prostředků při pokusu o přístup k prostředku.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Omezení ochrany identity pro uživatele b2B spolupráce

Existují omezení v implementaci identity protection pro uživatele spolupráce B2B v adresáři prostředků z důvodu jejich identity existující v jejich domovském adresáři. Hlavní omezení jsou následující:

- Pokud uživatel typu Host spustí zásady rizika uživatele ochrany identity, aby vynutil resetování hesla, **bude blokován**. Tento blok je způsoben neschopností obnovit hesla v adresáři prostředků.
- **Uživatelé typu Host se v přehledu rizikových uživatelů nezobrazují**. Tato ztráta viditelnosti je způsobena vyhodnocením rizika, ke kterému dochází v domovském adresáři uživatele B2B.
- Správci **nemohou zavřít nebo napravit rizikového uživatele spolupráce B2B** v jejich adresáři prostředků. Tato ztráta funkčnosti je způsobena správci v adresáři prostředků, kteří nemají přístup k domovskému adresáři uživatele B2B.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Proč nemohu v adresáři napravit rizikové uživatele spolupráce B2B?

Hodnocení rizik a náprava pro uživatele B2B dochází v jejich domovském adresáři. Z tohoto důvodu se uživatelé typu Host nezobrazují v sestavě rizikových uživatelů v adresáři prostředků a správci v adresáři prostředků nemohou vynutit bezpečné resetování hesla pro tyto uživatele.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Co mám dělat, když byl uživatel spolupráce B2B zablokován z důvodu zásad založených na rizicích v organizaci?

Pokud je rizikový uživatel B2B ve vašem adresáři blokován zásadami založenými na rizicích, bude muset toto riziko napravit ve svém domovském adresáři. Uživatelé mohou své riziko napravit provedením bezpečného resetování hesla v domovském adresáři. Pokud nemají v domovském adresáři povoleno samoobslužné resetování hesla, budou muset kontaktovat pracovníky IT své vlastní organizace, aby správce ručně zamítl riziko nebo resetoval své heslo.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Jak zabráním uživatelům spolupráce B2B, aby byli ovlivněni zásadami založenými na rizicích?

Vyloučení uživatelů B2B z zásad podmíněného přístupu vaší organizace založené na rizicích zabrání uživatelům B2B, aby byli ovlivněni nebo blokováni jejich hodnocením rizik. Chcete-li vyloučit tyto uživatele B2B, vytvořte skupinu ve službě Azure AD, která obsahuje všechny uživatele typu Host vaší organizace. Potom přidejte tuto skupinu jako vyloučení pro integrované riziko uživatele ochrany identity a zásady rizik přihlášení, stejně jako všechny zásady podmíněného přístupu, které používají riziko přihlášení jako podmínku.

## <a name="next-steps"></a>Další kroky

Podívejte se na následující články o spolupráci Azure AD B2B:

- [Co je spolupráce B2B ve službě Azure AD?](../b2b/what-is-b2b.md)
