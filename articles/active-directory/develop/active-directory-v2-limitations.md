---
title: Azure Active Directory v2.0 koncový bod omezení a limity | Microsoft Docs
description: Seznam omezení a limity pro koncový bod v2.0 Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: e026fd7021b39905d5392be55dbf3862cd307360
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156387"
---
# <a name="should-i-use-the-v20-endpoint"></a>Použít koncového bodu v2.0?
Při vytváření aplikací, které se integrují s Azure Active Directory, musíte rozhodnout, zda protokoly v2.0 koncový bod a ověřování podle svých potřeb. Původní koncový bod Azure služby Active Directory je stále plně podporovaná a v některých ohledech je další bohaté funkce než v2.0. Ale koncového bodu v2.0 [představuje významné výhody](active-directory-v2-compare.md) pro vývojáře.

Tady je naše zjednodušené doporučení pro vývojáře v tuto chvíli:

* Jestliže musíte podporovat osobní účty Microsoft v aplikaci, použijte koncového bodu v2.0. Ale předtím, než provedete, ujistěte se, že rozumíte omezení, které v tomto článku probereme.
* Pokud aplikace potřebuje pouze na podporu společnosti Microsoft pracovní a školní účty, nepoužívejte koncového bodu v2.0. Místo toho najdete na našem [Příručka pro vývojáře Azure AD](active-directory-developers-guide.md).

V čase bude dosáhnout koncového bodu v2.0 eliminovat omezení zde uvedeny, takže je potřeba vždy jen pomocí koncového bodu v2.0. Do té doby Tento článek je určený k vám pomohou určit, jestli je koncový bod v2.0 pro vás nejvhodnější. Budeme nadále aktualizace v tomto článku tak, aby odrážela aktuální stav koncového bodu v2.0. Vraťte se zpět na přehodnocovat požadavků na možnosti v2.0.

Pokud máte existující aplikaci Azure AD, která nepoužívá koncový bod v2.0, je potřeba začít úplně od začátku. V budoucnu poskytujeme způsob, jak používat svoje stávající aplikace Azure AD s koncovým bodem v2.0.

## <a name="restrictions-on-app-types"></a>Omezení typů aplikací
V současné době nepodporuje následující typy aplikací koncového bodu v2.0. Popis typů podporovaných aplikací najdete v tématu [typy aplikací pro koncový bod v2.0 Azure Active Directory](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>Samostatné webové rozhraní API
Můžete použít pro koncový bod v2.0 [sestavit Web API, která je zabezpečená pomocí OAuth 2.0](active-directory-v2-flows.md#web-apis). Ale že webového rozhraní API může přijímat tokeny pouze z aplikace, která má stejné ID aplikace Nelze získat přístup webového rozhraní API z klienta, který má jiné ID aplikace Klienta nebude možné požadovat nebo získat oprávnění k vaší webové rozhraní API.

Jak sestavit webové rozhraní API, které přijímá tokeny z klienta, který má stejné ID aplikace, najdete v sekci ukázky webového rozhraní API koncový bod v2.0 v našem [Začínáme](active-directory-appmodel-v2-overview.md#getting-started) části.

## <a name="restrictions-on-app-registrations"></a>Omezení při registraci aplikace
V současné době pro každou aplikaci, kterou chcete integrovat s koncovým bodem v2.0, je nutné vytvořit registraci aplikací v novém [portálu pro registraci aplikace Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Existující služby Azure AD, nebo nejsou kompatibilní s koncovým bodem v2.0 aplikace účtu Microsoft. Aplikace, které jsou zaregistrovány v libovolnému portálu než portálu pro registraci aplikace nejsou kompatibilní s koncovým bodem v2.0. V budoucnu plánujeme poskytnout způsob, jak používat existující aplikaci jako v2.0 aplikaci. V současné době ale neexistuje žádná cesta migrace pro stávající aplikace pro práci s koncovým bodem v2.0.

Kromě toho registrace aplikace, které vytvoříte v [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) mít zohledněním těchto aspektů:

* Jsou povoleny pouze dva tajné klíče aplikace na ID aplikace.
* Registraci aplikace registrovaný uživatel s osobní účet Microsoft můžete zobrazit a spravovat pouze podle jednoho vývojářského účtu. Nesmí se sdílet mezi více vývojářů. Pokud chcete sdílet mezi více vývojářů registrace aplikace, můžete vytvořit aplikaci po přihlášení k portálu pro registraci pomocí účtu Azure AD.
* Existuje několik omezení na formát identifikátor URI, který je povoleno přesměrování. Další informace o přesměrování identifikátory URI najdete v další části.

## <a name="restrictions-on-redirect-uris"></a>Omezení přesměrování identifikátory URI
Aplikace, které jsou zaregistrovány v portálu pro registraci aplikace v současné době jsou omezeny na omezenou sadu hodnot identifikátoru URI přesměrování. Přesměrování identifikátor URI pro webové aplikace a služby musí začínat schéma `https`, a všechny hodnoty identifikátoru URI přesměrování musí sdílí jedinou doménu DNS. Například nelze zaregistrovat webové aplikace, který obsahuje jedno z těchto přesměrování identifikátory URI:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Systém registrace porovná celý název DNS existující identifikátor URI pro přesměrování název DNS identifikátor URI, který chcete přidat přesměrování. Požadavek na přidání názvu DNS selže, pokud platí některá z následujících podmínek:  

* Celý název DNS nový identifikátor URI pro přesměrování neodpovídá názvu DNS existující identifikátor URI pro přesměrování.
* Celý název DNS nový identifikátor URI pro přesměrování není subdoména existující identifikátor URI pro přesměrování.

Například, pokud má tento identifikátor URI pro přesměrování:

`https://login.contoso.com`

Můžete ji přidat tímto způsobem:

`https://login.contoso.com/new`

V tomto případě se název DNS přesně shoduje. Nebo můžete provést toto:

`https://new.login.contoso.com`

V tomto případě odkazujete na subdoménu DNS login.contoso.com. Pokud budete chtít mít aplikaci, která má přihlášení east.contoso.com a west.contoso.com přihlášení jako identifikátory URI přesměrování, je nutné přidat že ty přesměrování identifikátory URI v tomto pořadí:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Druhé dva můžete přidat, protože jsou subdomény první URI přesměrování contoso.com. Toto omezení se odeberou v nadcházející verzi.

Všimněte si také, může mít jenom 20 adresy URL odpovědi pro konkrétní aplikace.

Zjistěte, jak zaregistrovat aplikaci v portálu pro registraci aplikace, najdete v tématu [postup registrace aplikace s koncovým bodem v2.0](active-directory-v2-app-registration.md).

## <a name="restrictions-on-libraries-and-sdks"></a>Omezení knihovny a sady SDK
Podpora knihovny pro koncový bod v2.0 je v současné době omezené. Pokud chcete použít koncového bodu v2.0 v produkční aplikace, máte tyto možnosti:

* Pokud vytváříte webovou aplikaci, bezpečně můžete všeobecně dostupná serverové middlewaru Microsoft k přihlášení a token ověření. Mezi ně patří middleware OWIN Open ID Connect pro technologii ASP.NET a Node.js Passport modulu plug-in. Ukázky kódu, které používají Microsoft middleware, najdete v tématu naše [Začínáme](active-directory-appmodel-v2-overview.md#getting-started) části.
* Pokud vytváříte aplikace desktop či mobile, můžete použít jeden z našich preview Microsoft ověřování knihovny (MSAL). Tyto knihovny jsou ve verzi preview s provozním podporována, takže je bezpečné používat v produkční aplikace. Další informace o podmínky verze preview a dostupných knihoven v našich [referenční dokumentace knihoven ověřování](active-directory-v2-libraries.md).
* Pro platformy, které nejsou pokryty knihovny Microsoft můžete integrovat s koncovým bodem v2.0 pomocí přímo odesílání a přijímání zprávy protokolu v kódu aplikace. Protokoly OpenID Connect a OAuth v2.0 [explicitně popsané](active-directory-v2-protocols.md) můžete provádět takové integrace.
* Nakonec můžete otevřít ID Connect a OAuth knihovny open-source pro integraci s koncovým bodem v2.0. Protokol v2.0 musí být kompatibilní s mnoha knihovny open-source protokolu bez hlavní změny. Dostupnost tohoto typu knihovny se liší podle jazyka a platformu. [Open ID Connect](http://openid.net/connect/) a [OAuth 2.0](http://oauth.net/2/) weby udržovat seznam oblíbených implementace. Další informace najdete v tématu [v2.0 a ověřování knihovny Azure Active Directory](active-directory-v2-libraries.md)a seznam open-source klientské knihovny a ukázky, které byly testovány s koncovým bodem v2.0.

## <a name="restrictions-on-protocols"></a>Omezení pro protokoly
Koncový bod v2.0 nepodporuje SAML nebo WS-Federation; podporuje pouze Open ID Connect a OAuth 2.0. Ne všechny funkce a možnosti OAuth protokolů byly zahrnuty do koncového bodu v2.0. Aktuálně jsou tyto funkce protokolu a možnosti *není k dispozici* v koncového bodu v2.0:

* ID tokeny, které jsou vydány koncového bodu v2.0 neobsahují `email` deklarace pro uživatele, i když získat oprávnění od uživatele k zobrazení e-mailu.
* Koncový bod OpenID Connect uživateli není implementována pro koncový bod v2.0. Všechna data profilu, které se by potenciálně zobrazit na tento koncový bod je však k dispozici z Microsoft Graph `/me` koncový bod.
* Koncový bod v2.0 nepodporuje vydávající role nebo skupiny deklarací identity v tokenech ID.
* [Udělení pověření heslo vlastníka prostředku OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3) nepodporuje koncového bodu v2.0.

Kromě toho koncového bodu v2.0 nepodporuje jakoukoli formu protokoly SAML nebo WS-Federation.

Abyste lépe pochopili rozsah protokol funkcí podporovaných v koncový bod v2.0, přečíst naše [referenční informace o protokolu OpenID Connect a OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Omezení pro pracovní a školní účty
Pokud jste použili Active Directory Authentication Library (ADAL) v aplikacích Windows, může mít prováděné výhod integrované ověřování systému Windows, který používá grant assertion Security (Assertion Markup Language SAML). S Tento grant federované uživatele Azure AD klienty můžete bezobslužně ověřit pomocí jejich místní instanci Active Directory bez nutnosti zadávat přihlašovací údaje. Udělení kontrolního výrazu SAML v současné době nepodporuje na koncový bod v2.0.
