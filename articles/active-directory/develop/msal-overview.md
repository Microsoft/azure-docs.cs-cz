---
title: Další informace o MSAL | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací získat tokeny za účelem volání zabezpečených webových rozhraní API. Tato webová rozhraní API mohou být Microsoft Graph, další rozhraní API společnosti Microsoft, webová rozhraní API jiných výrobců nebo vlastní webové rozhraní API. MSAL podporuje více architektur aplikací a platforem.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 89c5117e59f7856c3bd572bbea297a836b5ad589
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536212"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Přehled knihovny ověřování společnosti Microsoft (MSAL)
Microsoft Authentication Library (MSAL) umožňuje vývojářům získat [tokeny](developer-glossary.md#security-token) z koncového bodu platformy identit microsoftu pro přístup k zabezpečeným webovým rozhraním API. Tato webová rozhraní API mohou být Microsoft Graph, další rozhraní API společnosti Microsoft, webová rozhraní API jiných výrobců nebo vlastní webové rozhraní API. MSAL je k dispozici pro .NET, JavaScript, Android a iOS, které podporují mnoho různých architektur aplikací a platforem.

MSAL poskytuje mnoho způsobů, jak získat tokeny, s konzistentní rozhraní API pro řadu platforem. Použití msal poskytuje následující výhody:

* Není třeba přímo používat knihovny OAuth nebo kód proti protokolu ve vaší aplikaci.
* Získá tokeny jménem uživatele nebo jménem aplikace (pokud se vztahuje na platformu).
* Udržuje mezipaměť tokenů a aktualizuje tokeny za vás, když jsou blízko k vypršení platnosti. Není nutné zpracovávat vypršení platnosti tokenu na vlastní pěst.
* Pomáhá určit, které okruhy uživatelů chcete, aby se vaše aplikace přihlašovala (vaše organizace, několik orgů, pracovní a školní osobní účty a osobní účty Microsoftu, sociální identity s Azure AD B2C, uživatelé ve suverénních a národních cloudech).
* Pomáhá nastavit aplikaci z konfiguračních souborů.
* Pomáhá řešit problémy s aplikací tím, že vystaví použitelné výjimky, protokolování a telemetrii.

## <a name="application-types-and-scenarios"></a>Typy aplikací a scénáře
Pomocí MSAL lze získat token z řady typů aplikací: webové aplikace, webová api, jednostránkové aplikace (JavaScript), mobilní a nativní aplikace a daemons a aplikace na straně serveru.

MSAL lze použít v mnoha scénářích aplikace, včetně následujících:

* [Jednostránkové aplikace (JavaScript)](scenario-spa-overview.md)
* [Přihlášení uživatelů pomocí webové aplikace](scenario-web-app-sign-user-overview.md)
* [Přihlášení webové aplikace k uživateli a volání webového rozhraní API jménem uživatele](scenario-web-app-call-api-overview.md)
* [Ochrana webového rozhraní API, aby k němu měli přístup pouze ověření uživatelé](scenario-protected-web-api-overview.md)
* [Webové rozhraní API volající jiné podřízené webové rozhraní API jménem přihlášeného uživatele](scenario-web-api-call-api-overview.md)
* [Desktopová aplikace volající webové rozhraní API jménem přihlášeného uživatele](scenario-desktop-overview.md)
* [Mobilní aplikace volá webové rozhraní API jménem uživatele, který je přihlášen interaktivně](scenario-mobile-overview.md).
* [Desktop/service daemon aplikace volání webové rozhraní API jménem sebe](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Jazyky a rámce

| Knihovna | Podporované platformy a rámce|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| Rozhraní .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, univerzální platforma Windows|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/TypeScript architektury jako AngularJS, Ember.js nebo Durandal.js|
| [MSAL pro Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL pro iOS a MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS a macOS|
| [MSAL Java (náhled)](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|
| [MSAL Python (náhled)](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Python|

## <a name="differences-between-adal-and-msal"></a>Rozdíly mezi ADAL a MSAL

Knihovna adisponátu Služby Active Directory (ADAL) se integruje s koncovým bodem Azure AD pro vývojáře (v1.0), kde se MSAL integruje s koncovým bodem platformy Microsoft identit (v2.0). Koncový bod v1.0 podporuje pracovní účty, ale ne osobní účty. Koncový bod verze 2.0 je sjednocení osobních účtů Microsoft a pracovních účtů do jediného ověřovacího systému. Kromě toho s MSAL můžete také získat ověřování pro Azure AD B2C.

Podrobnější informace naleznete v informacích o [migraci do MSAL.NET z ADAL.NET](msal-net-migration.md) a [migraci na soubor MSAL.js z adal.js](msal-compare-msal-js-and-adal-js.md).
