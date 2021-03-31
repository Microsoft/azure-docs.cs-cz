---
title: Identity Protection a B2B Users – Azure Active Directory
description: Použití Identity Protection s uživateli B2B, jak funguje a známá omezení
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
ms.openlocfilehash: c9ec9e110c3a476c9096ae3e216c9780da0e0f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88949192"
---
# <a name="identity-protection-and-b2b-users"></a>Ochrana identit a uživatelé B2B

Díky spolupráci Azure AD B2B můžou organizace vyhodnotit rizikové zásady pro uživatele B2B pomocí Identity Protection. Tyto zásady se konfigurují dvěma způsoby:

- Správci můžou nakonfigurovat integrované zásady založené na riziku Identity Protection, které platí pro všechny aplikace, které zahrnují uživatele typu Host.
- Správci můžou nakonfigurovat své zásady podmíněného přístupu, a to pomocí rizika přihlášení jako podmínky, která zahrnuje uživatele typu Host.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Jak se vyhodnocuje riziko pro uživatele spolupráce B2B

Riziko uživatele pro spolupráci B2B pro uživatele se vyhodnotí v domovském adresáři. Riziko přihlášení v reálném čase pro tyto uživatele se vyhodnotí v adresáři prostředků při pokusu o přístup k prostředku.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Omezení identity Protection pro uživatele spolupráce B2B

Existují omezení implementace Identity Protection pro uživatele spolupráce B2B v adresáři prostředků v důsledku jejich identity existující v domovském adresáři. Hlavní omezení jsou následující:

- Pokud uživatel typu Host aktivuje zásadu rizika uživatele ochrany identity, aby vynutil resetování hesla, **bude blokovaný**. Tento blok je z důvodu nemožnost resetovat hesla v adresáři prostředků.
- **Uživatelé typu Host se nezobrazí v sestavě rizikové uživatele**. Tato ztráta viditelnosti je způsobena hodnocením rizika, které se vyskytuje v domovském adresáři uživatele B2B.
- Správci **nemůžou v adresáři prostředků zavřít ani opravit rizikového uživatele spolupráce B2B** . Tato ztráta funkčnosti je způsobená tím, že správci v adresáři prostředků nemají přístup k domovskému adresáři uživatele B2B.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Proč není možné v adresáři napravit rizikové uživatele spolupráce B2B?

V domovském adresáři dojde k vyhodnocení rizik a nápravě pro uživatele B2B. Z tohoto důvodu se uživatelé typu Host nezobrazí v sestavě rizikové uživatele v adresáři prostředků a správci v adresáři prostředků nemohou vynutit zabezpečené resetování hesla pro tyto uživatele.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Jak mám dělat, když se uživatel spolupráce B2B zablokoval v důsledku zásad založených na rizicích v mojí organizaci?

Pokud vaše zásada založená na riziku blokuje rizikové uživatele B2B v adresáři, bude muset toto riziko opravit v domovském adresáři. Uživatelé mohou napravit riziko pomocí zabezpečeného resetování hesla v domovském adresáři. Pokud v domovském adresáři nejsou povolené Samoobslužné resetování hesla, bude nutné, aby se obrátili na pracovníky IT organizace, aby správce mohl ručně odvolat jejich riziko nebo resetovat heslo.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Návody zabránit tomu, aby uživatelé spolupráce B2B ovlivnili zásady založené na rizikech?

Vyloučení uživatelů B2B ze zásad podmíněného přístupu na základě rizik vaší organizace zabrání uživatelům B2B v ovlivnění nebo zablokování jejich hodnocením rizik. Pokud chcete vyloučit tyto uživatele B2B, vytvořte skupinu ve službě Azure AD, která bude obsahovat všechny uživatele typu Host vaší organizace. Pak přidejte tuto skupinu jako vyloučení pro integrované uživatelské riziko ochrany identity a rizikové zásady pro přihlašování a taky všechny zásady podmíněného přístupu, které jako podmínku používají přihlašování.

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články o spolupráci Azure AD B2B:

- [Co je spolupráce B2B ve službě Azure AD?](../external-identities/what-is-b2b.md)