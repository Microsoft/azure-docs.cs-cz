---
title: Omezení spolupráce B2B – Azure Active Directory | Dokumenty společnosti Microsoft
description: Aktuální omezení pro spolupráci služby Azure Active Directory B2B
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263358"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Omezení spolupráce Azure AD B2B
Spolupráce b2B služby Azure Active Directory (Azure AD) aktuálně podléhá omezením popsaným v tomto článku.

## <a name="possible-double-multi-factor-authentication"></a>Možné dvojité vícefaktorové ověřování
S Azure AD B2B můžete vynutit vícefaktorové ověřování v organizaci prostředků (zvoucí organizace). Důvody pro tento přístup jsou podrobně popsány v [podmíněném přístupu pro uživatele spolupráce B2B](conditional-access.md). Pokud partner již má vícefaktorové ověřování nastaveno a vynuceno, jeho uživatelé budou muset ověření provést jednou ve své domovské organizaci a potom znovu ve vaší organizaci.

## <a name="instant-on"></a>Okamžité zapnutí
V tocích spolupráce B2B přidáváme uživatele do adresáře a dynamicky je aktualizujeme během uplatnění pozvánky, přiřazení aplikace a tak dále. Aktualizace a zápisy obvykle dojít v jedné instanci adresáře a musí být replikovány ve všech instancích. Replikace je dokončena, jakmile jsou aktualizovány všechny instance. Někdy při zápisu nebo aktualizaci objektu v jedné instanci a volání k načtení tohoto objektu je do jiné instance, může dojít k latenci replikace. Pokud k tomu dojde, aktualizujte nebo se pokuste o pomoc. Pokud píšete aplikaci pomocí našeho rozhraní API, pak opakování s některými back-off je dobrá, obranná praxe ke zmírnění tohoto problému.

## <a name="azure-ad-directories"></a>Adresáře Azure AD
Azure AD B2B podléhá omezením adresáře služby Azure AD. Podrobnosti o počtu adresářů, které může uživatel vytvořit, a o počtu adresářů, do kterých může uživatel nebo uživatel typu Host patřit, naleznete v [tématu omezení a omezení služby Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Národní mraky
[Národní cloudy](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) jsou fyzicky izolované instance Azure. Spolupráce B2B není podporována přes hranice národního cloudu. Například pokud váš tenant Azure je ve veřejném globálním cloudu, nemůžete pozvat uživatele, jehož účet je v národním cloudu. Chcete-li s uživatelem spolupracovat, požádejte ho o jinou e-mailovou adresu nebo pro ně vytvořte členský uživatelský účet ve vašem adresáři.

## <a name="azure-us-government-clouds"></a>Cloudy azure americké vlády
V rámci cloudu Azure US Government je spolupráce B2B momentálně podporovaná jenom mezi klienty, kteří jsou v cloudu Azure US Government a oba podporují spolupráci B2B. Pokud pozvete uživatele v tenantovi, který není součástí cloudu Azure US Government nebo který ještě nepodporuje spolupráci B2B, pozvánka se nezdaří nebo uživatel nebude moct pozvánku uplatnit. Podrobnosti o dalších omezeních najdete [v tématu Varianty Služby Azure Active Directory Premium P1 a P2](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Jak poznám, jestli je spolupráce B2B dostupná v mém tenantovi Azure US Government?
Pokud chcete zjistit, jestli váš tenant cloudu Azure US Government podporuje spolupráci B2B, postupujte takto:

1. V prohlížeči přejděte na následující adresu URL a naponořte název * &lt;klienta&gt;názvem tenanta*:

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Najít `"tenant_region_scope"` v odpovědi JSON:

   - Pokud `"tenant_region_scope":"USGOV”` se zobrazí, B2B je podporována.
   - Pokud `"tenant_region_scope":"USG"` se zobrazí, B2B není podporována.

## <a name="next-steps"></a>Další kroky

Podívejte se na následující články o spolupráci Azure AD B2B:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Pozvánky na spolupráci b2B delegáta](delegate-invitations.md)

