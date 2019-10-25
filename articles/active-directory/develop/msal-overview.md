---
title: Další informace o knihovně Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Knihovna Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací získat tokeny, aby mohli volat zabezpečená webová rozhraní API. Tato webová rozhraní API můžou být Microsoft Graph, jiná rozhraní API Microsoftu, webová rozhraní API třetích stran nebo vlastní webové rozhraní API. MSAL podporuje více architektur aplikací a platforem.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/4/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9d3cf20e7e950aed01c6a0dc5a4ea95a81f815d
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802579"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Přehled knihovny Microsoft Authentication Library (MSAL)
Knihovna Microsoft Authentication Library (MSAL) umožňuje vývojářům získat [tokeny](developer-glossary.md#security-token) z koncového bodu Microsoft Identity Platform, aby mohli přistupovat k zabezpečeným webovým rozhraním API. Tato webová rozhraní API můžou být Microsoft Graph, jiná rozhraní API Microsoftu, webová rozhraní API třetích stran nebo vlastní webové rozhraní API. MSAL je k dispozici pro .NET, JavaScript, Android a iOS, které podporují spoustu různých architektur aplikací a platforem.

MSAL poskytuje mnoho způsobů, jak získat tokeny s konzistentním rozhraním API pro celou řadu platforem. Použití MSAL přináší následující výhody:

* Není nutné přímo používat knihovny a kód OAuth s protokolem ve vaší aplikaci.
* Získá tokeny jménem uživatele nebo jménem aplikace (pokud to platí pro platformu).
* Udržuje mezipaměť tokenů a aktualizuje tokeny za vás, pokud se blíží vypršení platnosti. Nemusíte sami zpracovávat vypršení platnosti tokenu.
* Vám pomůže určit cílovou skupinu, se kterou chcete, aby se vaše aplikace přihlásila (vaše organizace, několik organizace, pracovních a školních a osobních účtů Microsoftu, sociální identity Azure AD B2C, uživatelů v svrchovaném a národním cloudech).
* Pomůže vám nastavit aplikaci z konfiguračních souborů.
* Pomáhá při řešení potíží s aplikací tím, že vystavuje akce, protokolování a telemetrii s výjimkou.

## <a name="application-types-and-scenarios"></a>Typy a scénáře aplikací
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
| [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Rozhraní JavaScript/TypeScript, jako je AngularJS, života. js nebo Durandal. js|
| [MSAL pro Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL pro iOS a macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS a macOS|
| [MSAL4J ve verzi Preview](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|

## <a name="differences-between-adal-and-msal"></a>Rozdíly mezi ADAL a MSAL

Active Directory Authentication Library (ADAL) se integruje s koncovým bodem Azure AD for Developers (v 1.0), kde MSAL se integruje s koncovým bodem Microsoft Identity Platform (v 2.0). Koncový bod v 1.0 podporuje pracovní účty, ale ne osobní účty. Koncový bod v 2.0 je sjednocením osobních účtů a pracovních účtů společnosti Microsoft do jednoho ověřovacího systému. Navíc můžete pomocí MSAL získat také ověřování pro Azure AD B2C.

Konkrétnější informace najdete v tématu [migrace na MSAL.NET z ADAL.NET](msal-net-migration.md) a [migrace na MSAL. js z ADAL. js](msal-compare-msal-js-and-adal-js.md).
