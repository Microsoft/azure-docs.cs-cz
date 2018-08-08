---
title: Azure Active Directory v2.0 koncový bod omezení a limity | Dokumentace Microsoftu
description: Seznam omezení a limity pro koncový bod Azure AD v2.0.
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
ms.date: 07/12/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: edf0b52e5889fe8fa875de65fcaa8c2a22df1a7f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590745"
---
# <a name="should-i-use-the-v20-endpoint"></a>Použít koncový bod verze 2.0?

Při sestavování aplikací, které se integrují s Azure Active Directory (Azure AD), musíte se rozhodnout, jestli protokoly pro ověřování a koncový bod v2.0 vašim potřebám. Původní koncového bodu Azure AD jsou stále plně podporovány a v některých ohledech se další bohaté funkce než verze 2.0. Ale koncovým bodem v2.0 [představuje významné výhody](azure-ad-endpoint-comparison.md) pro vývojáře.

Tady je zjednodušené doporučení pro vývojáře v daném okamžiku:

* Pokud vaše aplikace musí podporovat osobní účty Microsoft, použijte koncový bod verze 2.0. Ale předtím, než provedete, ujistěte se, že rozumíte omezení popsané v tomto článku.
* Pokud vaše aplikace potřebuje pouze pro podporu pracovní a školní účty, nepoužívejte koncový bod verze 2.0. Místo toho odkazovat [Příručka pro vývojáře Azure AD](azure-ad-developers-guide.md).

Chcete-li odstranit omezení zde uvedeny, tak, aby vždy jen musíte použít koncový bod verze 2.0 bude vyvíjet koncový bod verze 2.0. Do té doby použijte tento článek k určení, zda je koncový bod verze 2.0 pro vás nejvhodnější. Budeme nadále aktualizovat tak, aby odrážela aktuální stav koncového bodu v2.0 tohoto článku. Vraťte se zpět na opětovné vyhodnocení požadavků pro funkce verze 2.0.

Pokud máte existující aplikace Azure AD, která nepoužívá koncovým bodem v2.0, není nutné spustit od začátku. V budoucnu poskytneme vám používat svoje stávající aplikace Azure AD s koncovým bodem v2.0.

## <a name="restrictions-on-app-types"></a>Omezení typů aplikací

V současné době nepodporuje následující typy aplikací koncovým bodem v2.0. Popis typů podporovaných aplikací najdete v tématu [typů aplikací pro koncový bod služby Azure Active Directory v2.0](v2-app-types.md).

### <a name="standalone-web-apis"></a>Samostatné webové rozhraní API

Můžete použít k koncovým bodem v2.0 [sestavení webového rozhraní API, která je zabezpečena pomocí OAuth 2.0](v2-app-types.md#web-apis). Ale tohoto webového rozhraní API může přijímat tokeny pouze z aplikace, která má stejné ID aplikace. Nelze přistupovat k webové rozhraní API z klienta, který má jiné ID aplikace. Klient nebude moci vyžádat si či získat oprávnění pro vaše webové rozhraní API.

Informace o tom sestavení webového rozhraní API, které přijímá tokeny z klienta, který má stejné ID aplikace, najdete v ukázkách webového rozhraní API koncový bod v2.0 v [Začínáme](active-directory-appmodel-v2-overview.md#getting-started) části.

## <a name="restrictions-on-app-registrations"></a>Omezení registrace aplikací

V současné době pro každou aplikaci, kterou chcete integrovat s koncovým bodem v2.0, je nutné vytvořit registrace aplikace v novém [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Existující služby Azure AD, nebo nejsou kompatibilní s koncovým bodem v2.0 aplikace účtu Microsoft. Aplikace, které jsou registrovány žádné portálu než portál pro registraci aplikací nejsou kompatibilní s koncovým bodem v2.0. V budoucnu plánujeme zajistit způsob použití stávající aplikace jako aplikaci verze 2.0. V současné době neexistuje žádný způsob migrace pro existující aplikace pro práci s koncovým bodem v2.0.

Kromě toho registrace aplikací, které vytvoříte v [portál pro registraci aplikací](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) mají následující upozornění:

* Jsou povolené jenom dva tajné kódy aplikace za ID aplikace.
* Registrace aplikace s registrovaných uživatelů pomocí osobního účtu Microsoft můžete zobrazit a spravovat jenom jeden vývojářský účet. Není možné sdílet mezi více vývojářů. Pokud chcete sdílet vaše registrace aplikace mezi více vývojářů, můžete vytvořit aplikaci po přihlášení k portálu pro registraci pomocí účtu služby Azure AD.
* Existuje několik omezení na formátu identifikátoru URI, který je povoleno přesměrování. Další informace o identifikátory URI pro přesměrování najdete v další části.

## <a name="restrictions-on-redirect-uris"></a>Omezení pro identifikátory URI pro přesměrování

Aplikace, které jsou registrovány v portálu pro registraci aplikace jsou omezeny na omezenou sadu hodnot identifikátoru URI přesměrování. Přesměrování identifikátor URI pro webové aplikace a služby musí začínat schématem `https`, a všechny hodnoty identifikátoru URI pro přesměrování musejí sdílet jednu doménu DNS. Například nemůžete zaregistrovat webovou aplikaci s některou z těchto identifikátory URI pro přesměrování:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Registrační systém porovnává celý název DNS stávající URI přesměrování na název DNS přesměrování identifikátor URI, který chcete přidat. Požadavek na přidání názvu DNS selže, pokud platí některá z následujících podmínek:  

* Celý název DNS nové identifikátor URI pro přesměrování neodpovídá názvu DNS stávající identifikátor URI pro přesměrování.
* Celý název DNS nové identifikátor URI pro přesměrování není subdoménou stávající identifikátor URI pro přesměrování.

Například, pokud má tento identifikátor URI pro přesměrování aplikace:

`https://login.contoso.com`

Můžete ji přidat tímto způsobem:

`https://login.contoso.com/new`

V tomto případě se název DNS přesně shoduje. Nebo můžete provést toto:

`https://new.login.contoso.com`

V tomto případě odkazujete na subdoménu DNS login.contoso.com. Pokud chcete mít aplikaci, která má login-east.contoso.com a login-west.contoso.com jako identifikátory URI pro přesměrování, je nutné přidat že tyto identifikátory URI přesměrování v tomto pořadí:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Druhé dvě můžete přidat, protože jsou subdoménami první URI přesměrování contoso.com. Toto omezení se odeberou v nadcházející verzi.

Všimněte si také, může mít pouze 20 adresy URL odpovědí pro určitou aplikaci.

Zjistěte, jak zaregistrovat aplikaci v portálu pro registraci aplikací, najdete v článku [postup registrace aplikace s koncovým bodem v2.0](quickstart-v2-register-an-app.md).

## <a name="restrictions-on-libraries-and-sdks"></a>Omezení týkající se knihoven a sad SDK

Podpora knihovny pro koncový bod verze 2.0 je aktuálně omezená. Pokud chcete použít koncový bod v2.0 v produkční aplikace, máte tyto možnosti:

* Pokud vytváříte webovou aplikaci, můžete bez obav použít middlewaru Microsoft obecně dostupné na straně serveru k provedení ověřování přihlášení a token. Patří mezi ně middleware OWIN Open ID Connect pro ASP.NET a Node.js Passport modulu plug-in. Ukázky kódu, které používají Microsoft middleware, najdete v článku [Začínáme](active-directory-appmodel-v2-overview.md#getting-started) oddílu.
* Pokud vytváříte desktopové nebo mobilní aplikaci, můžete použít jednu z verze preview knihovny Microsoft Authentication (MSAL). Tyto knihovny jsou ve verzi preview s podporou produkčního prostředí, takže je bezpečný pro použití v aplikacích v produkčním prostředí. Další informace o podmínkách verze preview a dostupné knihovny v [referenční dokumentace knihoven ověřování](reference-v2-libraries.md).
* Pro platformy není pokrytá knihoven Microsoftu můžete integrovat s koncovým bodem v2.0 pomocí přímo, odesílání a přijímání zprávy protokolu v kódu aplikace. Protokoly OpenID Connect a OAuth 2.0 [výslovně popsanými](active-directory-v2-protocols.md) můžete provádět tato integrační.
* Nakonec můžete použít open source knihovny otevřete ID Connect a OAuth pro integraci s koncovým bodem v2.0. Verze 2.0 protokol by měl být kompatibilní s mnoha protokol open source knihovny bez důležité změny. Dostupnost tyto druhy knihoven se liší podle jazyka a libovolné platformy. [Open ID Connect](http://openid.net/connect/) a [OAuth 2.0](http://oauth.net/2/) websites udržovat seznam oblíbených implementace. Další informace najdete v tématu [knihovny Azure Active Directory v2.0 a ověřování](reference-v2-libraries.md)a seznam open source klientské knihovny a ukázky, které byly testovány s koncovým bodem v2.0.

## <a name="restrictions-on-protocols"></a>Omezení pro protokoly

Koncový bod v2.0 nepodporuje SAML nebo WS-Federation; podporuje pouze Open ID Connect a OAuth 2.0. Ne všechny funkce a možnosti protokoly OAuth byly zahrnuty do koncového bodu v2.0.

Následující protokol funkce a možnosti v současné době jsou *není k dispozici* koncový bod verze 2.0:

* V současné době `email` deklarace identity se vrátí pouze pokud je nakonfigurovaná volitelnou deklaraci a obor je rozsah = e-mailu byla zadaná v žádosti. Toto chování však změní jako koncový bod v2.0 se aktualizuje a další v souladu se standardy Open ID Connect a OAuth 2.0.
* Koncový bod OpenID Connect UserInfo není implementované u koncového bodu v2.0. Všechna data profilu, které potenciálně by se zobrazila v tomto koncovém bodu je však k dispozici z Microsoft Graphu `/me` koncového bodu.
* Koncový bod verze 2.0 nepodporuje vydávající roli nebo skupinu deklarací identity v tokenech ID.
* [Udělení přihlašovacích údajů heslo vlastníka prostředku OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3) není podporované koncovým bodem v2.0.

Koncový bod v2.0 nepodporuje žádnou formu protokoly SAML nebo WS-Federation.

Chcete-li lépe pochopit tak rozsah protokolu funkcí podporovaných koncový bod verze 2.0, přečtěte si naše [referenční informace o protokolu OpenID Connect a OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Omezení pro pracovní a školní účty

Pokud jste použili Active Directory Authentication Library (ADAL) v aplikacích Windows, může být trvaly výhod integrovaného ověřování Windows, který používá kontrolní výraz udělení zabezpečení kontrolního výrazu SAML (Markup Language). S tomuto grantu federovaných uživatelů Azure AD můžete bezobslužně tenanti ověřují pomocí jejich místní instancí Active Directory bez zadávání přihlašovacích údajů. Udělení kontrolního výrazu SAML se v současné době nepodporuje na koncový bod verze 2.0.
