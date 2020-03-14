---
title: Omezení spolupráce B2B – Azure Active Directory | Microsoft Docs
description: Aktuální omezení pro Azure Active Directory spolupráci B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffee01488ecf658ce02a20a41252aca19288667c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263358"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Omezení spolupráce B2B Azure AD
Spolupráce B2B v Azure Active Directory (Azure AD) v současné době závisí na omezeních popsaných v tomto článku.

## <a name="possible-double-multi-factor-authentication"></a>Možné dvojité ověřování Multi-Factor Authentication
Pomocí Azure AD B2B můžete vyhodnotit vícefaktorové ověřování v organizaci poskytující prostředky (subjekt pro pozvání). Důvody tohoto přístupu jsou podrobně popsané v části [podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md). Pokud už má partner nastavené a vymáhání aplikace Multi-Factor Authentication, může být nutné provést ověřování jednou v jejich domovské organizaci a pak znovu.

## <a name="instant-on"></a>Okamžitý
V tocích spolupráce B2B přidáme do adresáře uživatele a dynamicky je aktualizujeme během uplatnění pozvánky, přiřazení aplikace atd. Aktualizace a zápisy se obvykle vyskytují v jedné instanci adresáře a musí se replikovat napříč všemi instancemi. Po aktualizaci všech instancí se replikace dokončila. V některých případech se může stát, že když se objekt napíše nebo aktualizuje v jedné instanci a volání metody načítající tento objekt je další instance, může dojít k latenci replikace. Pokud k tomu dojde, aktualizujte nebo znovu spusťte podporu. Pokud píšete aplikaci pomocí našeho rozhraní API, pak opakování pokusů s některým z nich je dobrým obrannou liniím postupem, jak tento problém zmírnit.

## <a name="azure-ad-directories"></a>Adresáře Azure AD
Azure AD B2B podléhá omezením adresáře služby Azure AD. Podrobnosti o počtu adresářů, které může uživatel vytvořit, a počtu adresářů, do kterých může uživatel nebo uživatel typu Host patřit, najdete v tématu omezení [a omezení služby Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Národní cloudy
[Národní cloudy](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) jsou fyzicky izolované instancemi Azure. Spolupráce B2B není podporovaná v rámci hranic národního cloudu. Pokud je například váš tenant Azure ve veřejném, globálním cloudu, nemůžete pozvat uživatele, jehož účet je v národním cloudu. Pokud chcete s uživatelem spolupracovat, požádejte je o další e-mailovou adresu nebo pro ně vytvořte uživatelský účet v adresáři.

## <a name="azure-us-government-clouds"></a>Cloudy pro státní správu Azure USA
V cloudu pro státní správu Azure USA je spolupráce B2B v současnosti podporovaná jenom mezi klienty, kteří jsou v cloudu Azure USA a kteří podporují spolupráci B2B. Pokud uživatele v tenantovi, který není součástí cloudu pro státní správu Azure USA nebo který ještě nepodporuje spolupráci B2B, nebude tato pozvánka úspěšná nebo uživatel nebude moct pozvánku uplatnit. Podrobnosti o dalších omezeních najdete v tématu [variace Azure Active Directory Premium P1 a P2](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Jak zjistím, jestli je spolupráce B2B dostupná ve vašem tenantovi Azure pro státní správu USA?
Pokud chcete zjistit, jestli váš cloudový tenant Azure pro státní správu USA podporuje spolupráci B2B, udělejte toto:

1. V prohlížeči přejdete na následující adresu URL, kde nahradíte název tenanta pro *&lt;tenant&gt;* :

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Najít `"tenant_region_scope"` v odpovědi JSON:

   - Pokud se zobrazí `"tenant_region_scope":"USGOV”`, je podpora B2B podporována.
   - Pokud se zobrazí `"tenant_region_scope":"USG"`, B2B se nepodporuje.

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články o spolupráci Azure AD B2B:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Delegovat pozvánky pro spolupráci B2B](delegate-invitations.md)

