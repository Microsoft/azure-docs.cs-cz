---
title: Ukázky kódu pro Azure Active Directory B2C | Microsoft Docs
description: Ukázky kódu pro mobilní, desktopové, webové a jednostránkové aplikace Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 01/23/2018
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: ffb60ab07ef21a94a7ba978fe354948f18479a37
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446556"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Ukázky kódu pro Azure Active Directory B2C

Následující tabulka obsahuje odkazy na ukázky pro aplikace, včetně ukázek pro iOS, Android, .NET a Node.js.

## <a name="mobile-and-desktop-apps"></a>Mobilní a desktopové aplikace

| Ukázka | Popis |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Ukázka pro iOS v jazyce Swift, která ověřuje uživatele Azure AD B2C a volá rozhraní API s použitím OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | Jednoduchá aplikace pro Android, která ukazuje použití MSAL k ověřování uživatelů prostřednictvím Azure Active Directory B2C a přístup k webovému rozhraní API s použitím výsledných tokenů. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Ukázka způsobu použití knihovny třetí strany k sestavení aplikace pro iOS v Objective-C, která ověřuje uživatele s identitou Microsoft ve službě identit Azure AD B2C. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Ukázka způsobu použití knihovny třetí strany k sestavení aplikace pro Android, která ověřuje uživatele s identitou Microsoft ve službě identit Azure AD B2C a volá webové rozhraní API s použitím přístupových tokenů OAuth 2.0. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Ukázka způsobu, jakým desktopová aplikace .NET pro Windows (WPF) může přihlásit uživatele pomocí Azure AD B2C, získat přístupový token pomocí MSAL.NET a volat rozhraní API. | 
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Jednoduchá aplikace Xamarin Forms, která ukazuje použití MSAL k ověřování uživatelů prostřednictvím Azure Active Directory B2C a přístup k webovému rozhraní API s použitím výsledných tokenů. |

## <a name="web-apps-and-apis"></a>Webové aplikace a rozhraní API

| Ukázka | Popis |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Souhrnná ukázka pro webovou aplikaci .NET, která volá webové rozhraní API .NET, obojí se zabezpečením pomocí Azure AD B2C. |
| [dotnetcore-webapp](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | Webová aplikace ASP.NET Core, která dokáže přihlásit uživatele pomocí Azure AD B2C, získat přístupový token pomocí MSAL.NET a volat rozhraní API. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | Aplikace Node.js, která poskytuje rychlý a snadný způsob nastavení webové aplikace pomocí Express s použitím OpenID Connect. |
| [javascript-nodejs-webapp](https://github.com/AzureADQuickStarts/active-directory-b2c-javascript-nodejs-webapp) | Server Node.js, který poskytuje rychlý a snadný způsob nastavení služby REST API pomocí protokolu OAuth2. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Malé webové rozhraní API Node.js pro Azure AD B2C, které ukazuje způsob ochrany webového rozhraní API a příjmu přístupových tokenů B2C pomocí passport.js. |

## <a name="single-page-apps"></a>Jednostránkové aplikace

| Ukázka | Popis |
|--------| ----------- |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Jednostránková aplikace (SPA) volající webové rozhraní API. Ověřování provádí Azure AD B2C s využitím MSAL.js. | 
| [javascript-hellojs-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-hellojs-singlepageapp) | Jednostránková aplikace s implementovaným back-endem webového rozhraní API ASP.NET, která registruje a přihlašuje uživatele pomocí Azure AD B2C a volá webové rozhraní API s použitím přístupových tokenů OAuth 2.0. |