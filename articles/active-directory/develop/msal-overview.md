---
title: Další informace o MSAL | Azure
titleSuffix: Microsoft identity platform
description: Knihovna Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací získat tokeny, aby mohli volat zabezpečená webová rozhraní API. Tato webová rozhraní API můžou být Microsoft Graph, jiná rozhraní API Microsoftu, webová rozhraní API třetích stran nebo vlastní webové rozhraní API. MSAL podporuje více architektur aplikací a platforem.
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
ms.openlocfilehash: c16eb2af5dfb103541c2eac207eb8ee5c303168f
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754708"
---
# <a name="overview-of-the-microsoft-authentication-library-msal"></a>Přehled knihovny Microsoft Authentication Library (MSAL)
Knihovna Microsoft Authentication Library (MSAL) umožňuje vývojářům získat [tokeny](developer-glossary.md#security-token) od platformy Microsoft identity, aby mohli ověřovat uživatele a přistupovat k zabezpečeným webovým rozhraním API. Dá se použít k zajištění zabezpečeného přístupu k Microsoft Graph, dalším rozhraním API Microsoftu, webovým rozhraním API jiných výrobců nebo k vlastnímu webovému rozhraní API. MSAL podporuje spoustu různých aplikačních architektur a platforem, jako je .NET, JavaScript, Java, Python, Android a iOS.

MSAL poskytuje mnoho způsobů, jak získat tokeny s konzistentním rozhraním API pro celou řadu platforem. Použití MSAL přináší následující výhody:

* Není nutné přímo používat knihovny a kód OAuth s protokolem ve vaší aplikaci.
* Získá tokeny jménem uživatele nebo jménem aplikace (pokud to platí pro platformu).
* Udržuje mezipaměť tokenů a aktualizuje tokeny za vás, pokud se blíží vypršení platnosti. Nemusíte sami zpracovávat vypršení platnosti tokenu.
* Vám pomůže určit cílovou skupinu, se kterou chcete, aby se vaše aplikace přihlásila (vaše organizace, několik organizace, pracovních a školních a osobních účtů Microsoftu, sociální identity Azure AD B2C, uživatelů v svrchovaném a národním cloudech).
* Pomůže vám nastavit aplikaci z konfiguračních souborů.
* Pomáhá při řešení potíží s aplikací tím, že vystavuje akce, protokolování a telemetrii s výjimkou.

## <a name="application-types-and-scenarios"></a>Typy aplikací a scénáře
Pomocí MSAL lze token získat z několika typů aplikací: webové aplikace, webová rozhraní API, jednostránkové aplikace (JavaScript), mobilní a nativní aplikace a démoni a aplikace na straně serveru.

MSAL se dá použít v mnoha scénářích aplikací, včetně následujících:

* [Jednostránkové aplikace (JavaScript)](scenario-spa-overview.md)
* [Webová aplikace přihlašování uživatelů](scenario-web-app-sign-user-overview.md)
* [Webová aplikace, která přihlašuje uživatele a volá webové rozhraní API jménem uživatele](scenario-web-app-call-api-overview.md)
* [Ochrana webového rozhraní API, aby k němu měli přístup jenom ověření uživatelé](scenario-protected-web-api-overview.md)
* [Webové rozhraní API, které jménem přihlášeného uživatele volá jiné webové rozhraní API pro příjem dat](scenario-web-api-call-api-overview.md)
* [Aplikace klasické pracovní plochy, která jménem přihlášeného uživatele volá webové rozhraní API](scenario-desktop-overview.md)
* [Mobilní aplikace, která volá webové rozhraní API jménem uživatele, který je přihlášený interaktivně](scenario-mobile-overview.md).
* [Aplikace démona plochy/služby – volání webového rozhraní API jménem sebe sama](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Jazyky a rozhraní

| Knihovna | Podporované platformy a architektury|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Univerzální platforma Windows|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Rozhraní JavaScript/TypeScript, jako je AngularJS, Ember.js nebo Durandal.js|
| [MSAL pro Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL pro iOS a MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS a macOS|
| [MSAL v Javě](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|

## <a name="differences-between-adal-and-msal"></a>Rozdíly mezi ADAL a MSAL

Active Directory Authentication Library (ADAL) se integruje s koncovým bodem Azure AD for Developers (v 1.0), kde MSAL se integruje s platformou Microsoft identity. Koncový bod v 1.0 podporuje pracovní účty, ale ne osobní účty. Koncový bod v 2.0 je sjednocením osobních účtů a pracovních účtů společnosti Microsoft do jednoho ověřovacího systému. Navíc můžete pomocí MSAL získat také ověřování pro Azure AD B2C.

Podrobnější informace najdete v článku o [migraci na MSAL.NET z ADAL.NET](msal-net-migration.md) a [migrace na MSAL.js z ADAL.js](msal-compare-msal-js-and-adal-js.md).
