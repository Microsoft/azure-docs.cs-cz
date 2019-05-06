---
title: Další informace o Microsoft Authentication Library (MSAL) | Azure
description: Microsoft Authentication Library (MSAL) vývojářům aplikací umožňuje získat tokeny pro volání zabezpečená webová rozhraní API. Tato webová rozhraní API může být Microsoft Graphu, jiné APIS Microsoftu, třetích stran webová rozhraní API nebo svým vlastním rozhraním Web API. Knihovna MSAL podporuje více aplikačních architektur a platformy.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2198a5efe276ce872487cde7b2ddb5cb00c58c50
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080895"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Přehled knihovny Microsoft Authentication Library (MSAL)
Microsoft Authentication Library (MSAL) umožňuje vývojářům získat [tokeny](developer-glossary.md#security-token) z Microsoft identity platform koncový bod pro přístup k zabezpečená webová rozhraní API. Tato webová rozhraní API může být Microsoft Graphu, jiné APIS Microsoftu, třetích stran webová rozhraní API nebo svým vlastním rozhraním Web API. Knihovna MSAL je k dispozici pro .NET, JavaScript, Android a iOS, které podporují mnoho různých aplikační architektury a platformy.

Knihovna MSAL nabízí mnoho způsobů, jak získat tokeny s konzistentním rozhraním API pro řadu platforem. S využitím MSAL poskytuje následující výhody:

* Už není potřeba přímo pomocí OAuth knihovny nebo kód proti protokolu ve vaší aplikaci.
* Získá tokeny jménem uživatele nebo jejich jménem aplikace (Pokud se používá k platformě).
* Udržuje mezipaměť tokenu a aktualizuje tokeny pro vás, když jsou zavřít vyprší. Není nutné ke zpracování vypršení platnosti tokenu sami.
* Umožňuje určit, která cílová skupina má aplikace přihlásit (vaše organizace, několik organizace, práce a školní a osobní účty Microsoft, sociálních identit s Azure AD B2C, uživatelé v suverénních a národních cloudů).
* Umožňuje nastavení z konfiguračních souborů aplikace.
* Pomáhá řešit zveřejněním užitečné výjimky, protokolování a telemetrie vaší aplikace.

## <a name="application-types-and-scenarios"></a>Typy aplikací a scénářů
S využitím MSAL, je možné získat token, z mnoha typů aplikací: webových aplikací a webových rozhraní API, jednostránkové aplikace (JavaScript), mobilní a nativní aplikace a procesy démon a aplikací na straně serveru. 

Knihovna MSAL je použít v mnoha scénářích aplikací, včetně následujících:

* [Jednostránkové aplikace (JavaScript)](scenario-spa-overview.md) 
* [Webová aplikace přihlašování uživatelů](scenario-web-app-sign-user-overview.md)
* [Webová aplikace přihlášení uživatele a volat webové rozhraní API jménem uživatele](scenario-web-app-call-api-overview.md)
* [Ochrana webového rozhraní API, aby jenom ověření uživatelé k němu přístup](scenario-protected-web-api-overview.md)
* [Webové rozhraní API volání podřízené webové rozhraní API jménem přihlášeného uživatele](scenario-web-api-call-api-overview.md)
* [Volání webového rozhraní API jménem přihlášeného uživatele aplikace klasické pracovní plochy](scenario-desktop-overview.md)
* [Mobilní aplikace volá webové rozhraní API jménem uživatele, který je přihlášený interaktivní](scenario-mobile-overview.md).
* [Volání webového rozhraní API jménem samotné aplikace démon desktopu nebo služby](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Jazyky a architektury

| Knihovna | Podporované platformy a rozhraní|
| --- | --- | 
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/>[MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| Rozhraní .NET framework, .NET Core, Xamarin Android, Xamarin iOS, univerzální platforma Windows|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/>[MSAL.js (preview)](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/TypeScript architektury, jako jsou AngularJS, Ember.js nebo Durandal.js|
| ![Knihovna MSAL pro Android](media/sample-v2-code/logo_Android.png) <br/>[Knihovna MSAL pro Android (preview)](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| ![Knihovna MSAL pro iOS](media/sample-v2-code/logo_iOS.png) <br/>[KNIHOVNA MSAL. Objective-C (preview)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS|

## <a name="differences-between-adal-and-msal"></a>Rozdíly mezi ADAL a MSAL
Active Directory Authentication Library (ADAL) se integruje s Azure AD pro vývojáře (verze 1.0) koncový bod, kde MSAL integruje do koncového bodu Microsoft identity platform (verze 2.0). Koncový bod verze 1.0 podporuje pracovní účty, ale ne osobní účty. Koncový bod v2.0 sjednocuje osobní účty Microsoft a pracovní účty do jednoho ověřovacího systému. Kromě toho se MSAL získáte taky ověřování pro Azure AD B2C.

Podrobnější informace, přečtěte si informace o [migrace na MSAL.NET z ADAL.NET](msal-net-migration.md) a [migrace na MSAL.js z ADAL.js](msal-compare-msal-js-and-adal-js.md).

            