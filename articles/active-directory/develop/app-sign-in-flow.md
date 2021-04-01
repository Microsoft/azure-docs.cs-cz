---
title: Postup přihlášení aplikace s platformou Microsoft identity | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o procesu přihlašování webových, desktopových a mobilních aplikací na platformě Microsoft identity.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 1f9f330ab140fa66b5a66a112c47ca2a68ba56bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755702"
---
# <a name="app-sign-in-flow-with-the-microsoft-identity-platform"></a>Postup přihlášení aplikace s platformou Microsoft identity

Toto téma popisuje základní postup přihlášení pro webové, desktopové a mobilní aplikace s využitím platformy Microsoft Identity Platform. V tématu [toky ověřování a scénáře aplikací](authentication-flows-app-scenarios.md) se dozvíte o scénářích přihlašování podporovaných platformou Microsoft identity.

## <a name="web-app-sign-in-flow"></a>Tok přihlášení k webové aplikaci

Když uživatel přejde v prohlížeči do webové aplikace, dojde k následujícímu:

* Webová aplikace určuje, jestli je uživatel ověřený.
* Pokud se uživatel neověřuje, Webová aplikace se přihlásí k Azure AD, aby se přihlásili uživateli. Toto přihlášení bude vyhovovat zásadám organizace, což může znamenat, že uživatel musí zadat své přihlašovací údaje pomocí služby [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) (někdy se mu říká dvojúrovňové ověřování nebo 2FA) nebo vůbec nepoužívat heslo (například Windows Hello).
* Uživateli se zobrazí výzva k vyjádření souhlasu s přístupem, který klientská aplikace potřebuje. Důvodem je, že klientské aplikace musí být zaregistrované ve službě Azure AD, aby platforma identity Microsoftu mohla poskytovat tokeny představující přístup, ke kterému se uživatel poslal.

Po úspěšném ověření uživatele:

* Microsoft Identity Platform pošle token webové aplikaci.
* Soubor cookie je uložen přidružený k doméně služby Azure AD, která obsahuje identitu uživatele v JAR souboru cookie prohlížeče. Když aplikace příště používá prohlížeč k přechodu na koncový bod autorizace platformy Microsoft Identity Platform, prohlížeč prezentuje soubor cookie, takže se uživatel nemusí znovu přihlašovat. To je také způsob, jakým je dosaženo jednotného přihlašování. Soubor cookie se vytvoří pomocí Azure AD a může ho rozumět jenom Azure AD.
* Webová aplikace potom ověří token. Pokud je ověření úspěšné, Webová aplikace zobrazí chráněnou stránku a uloží soubor cookie relace do jar v souboru cookie prohlížeče. Když uživatel přejde na jinou stránku, Webová aplikace ví, že se uživatel ověřuje na základě souboru cookie relace.

Následující sekvenční diagram shrnuje tuto interakci:

![proces ověřování webové aplikace](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Jak webová aplikace určuje, jestli je uživatel ověřený

Vývojáři webové aplikace mohou určit, zda některé stránky vyžadují ověření. Například v ASP.NET/ASP.NET Core je to provedeno přidáním `[Authorize]` atributu k akcím kontroleru.

Tento atribut způsobí, že ASP.NET zkontroluje přítomnost souboru cookie relace, který obsahuje identitu uživatele. Pokud soubor cookie přítomen není, ASP.NET přesměruje ověřování na zadaného zprostředkovatele identity. Pokud je poskytovatel identity Azure AD, přesměruje Tato webová aplikace ověřování na `https://login.microsoftonline.com` , které zobrazuje přihlašovací dialog.

### <a name="how-a-web-app-delegates-sign-in-to-the-microsoft-identity-platform-and-obtains-a-token"></a>Způsob, jakým webová aplikace deleguje přihlášení k platformě Microsoft identity a získá token

K ověřování uživatelů dochází prostřednictvím prohlížeče. Protokol OpenID používá standardní zprávy protokolu HTTP.

* Webová aplikace pošle do prohlížeče protokol HTTP 302 (přesměrování), aby používal platformu Microsoft Identity Platform.
* Po ověření uživatele pošle platforma Microsoft Identity token do webové aplikace pomocí přesměrování přes prohlížeč.
* Přesměrování je poskytováno webovou aplikací ve formě identifikátoru URI přesměrování. Tento identifikátor URI přesměrování je zaregistrován u objektu aplikace služby Azure AD. Může existovat několik identifikátorů URI pro přesměrování, protože aplikaci je možné nasadit na několik adres URL. Proto bude webová aplikace také muset určit identifikátor URI pro přesměrování, který se má použít.
* Azure AD ověří, že identifikátor URI přesměrování odesílaný webovou aplikací je jedním z registrovaných identifikátorů URI pro přesměrování pro aplikaci.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>Postup přihlášení k desktopovým a mobilním aplikacím

Výše popsaný postup se týká mírně rozdílů pro stolní a mobilní aplikace.

Desktopové a mobilní aplikace můžou k ověřování používat vložený webový ovládací prvek nebo systémový prohlížeč. Následující diagram znázorňuje, jak desktopová nebo mobilní aplikace používá knihovnu Microsoft Authentication Library (MSAL) k získání přístupových tokenů a volání webových rozhraní API.

![Desktopová aplikace, jak vypadá](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL pomocí prohlížeče získá tokeny. Stejně jako u webových aplikací je ověřování delegované na platformu Microsoft Identity Platform.

Vzhledem k tomu, že Azure AD ukládá stejný soubor cookie identity v prohlížeči jako v případě webových aplikací, pokud nativní nebo mobilní aplikace používá prohlížeč systému, bude ihned přihlášená k odpovídající webové aplikaci.

Ve výchozím nastavení používá MSAL prohlížeč systému. Výjimkou je .NET Framework desktopové aplikace, kde se k zajištění více integrovaných uživatelských prostředí používá integrovaný ovládací prvek.

## <a name="next-steps"></a>Další kroky

Další témata týkající se ověřování a základů autorizace:

* V tématu [ověřování vs. autorizace](authentication-vs-authorization.md) se dozvíte o základních konceptech ověřování a autorizace na platformě Microsoft identity.
* V tématu [tokeny zabezpečení](security-tokens.md) se dozvíte, jak se v ověřování a autorizaci používají přístupové tokeny, aktualizují tokeny a tokeny ID.
* V článku [aplikační model](application-model.md) se dozvíte o procesu registrace aplikace, aby se mohla integrovat s platformou Microsoft identity.

Další informace o toku přihlašování k aplikacím:

* Další informace o dalších scénářích pro ověřování uživatelů, které podporuje platforma Microsoft identity, najdete v tématu [toky ověřování a scénáře aplikací](authentication-flows-app-scenarios.md) .
* V tématu [knihovny MSAL](msal-overview.md) najdete informace o knihovnách Microsoftu, které vám pomůžou vyvíjet aplikace, které pracují s účty Microsoft, účty Azure AD a Azure AD B2C uživatelů v jednom, zjednodušeném programovacím modelu.
