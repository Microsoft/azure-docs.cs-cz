---
title: Ukázky kódu pro Azure Active Directory B2C | Microsoft Docs
description: Ukázky kódu pro mobilní, desktopové, webové a jednostránkové aplikace Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a75896dde7a23ea346e0af73ed145094e78812ca
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654489"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Ukázky kódu pro Azure Active Directory B2C

Následující tabulka obsahuje odkazy na ukázky pro aplikace, včetně ukázek pro iOS, Android, .NET a Node.js.

## <a name="mobile-and-desktop-apps"></a>Mobilní a desktopové aplikace

| Ukázka | Popis |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Ukázka pro iOS v jazyce Swift, která ověřuje uživatele Azure AD B2C a volá rozhraní API s použitím OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | Jednoduchá aplikace pro Android, která ukazuje použití MSAL k ověřování uživatelů prostřednictvím Azure Active Directory B2C a přístup k webovému rozhraní API s použitím výsledných tokenů. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Ukázka, která ukazuje, jak můžete použít knihovnu třetí strany k sestavení aplikace pro iOS v cíli – C, která ověřuje uživatele identity od Microsoftu k naší Azure AD B2C službě identity. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Ukázka, která ukazuje, jak můžete pomocí knihovny třetích stran vytvořit aplikaci pro Android, která ověřuje uživatele identity Microsoftu na naši službu B2C identity a volá webové rozhraní API s použitím přístupových tokenů OAuth 2,0. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Ukázka způsobu, jakým desktopová aplikace .NET pro Windows (WPF) může přihlásit uživatele pomocí Azure AD B2C, získat přístupový token pomocí MSAL.NET a volat rozhraní API. |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Jednoduchá aplikace Xamarin Forms, která ukazuje použití MSAL k ověřování uživatelů prostřednictvím Azure Active Directory B2C a přístup k webovému rozhraní API s použitím výsledných tokenů. |

## <a name="web-apps-and-apis"></a>Webové aplikace a rozhraní API

| Ukázka | Popis |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Souhrnná ukázka pro webovou aplikaci .NET, která volá webové rozhraní API .NET, obojí se zabezpečením pomocí Azure AD B2C. |
| [dotnetcore-WebApp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | ASP.NET Core webová aplikace, která používá OpenID Connect k přihlašování uživatelů v Azure AD B2C. |
| [dotnetcore-WebApp-msal-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Webová aplikace ASP.NET Core, která dokáže přihlásit uživatele pomocí Azure AD B2C, získat přístupový token pomocí MSAL.NET a volat rozhraní API. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | Aplikace Node.js, která poskytuje rychlý a snadný způsob nastavení webové aplikace pomocí Express s použitím OpenID Connect. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Malé webové rozhraní API Node.js pro Azure AD B2C, které ukazuje způsob ochrany webového rozhraní API a příjmu přístupových tokenů B2C pomocí passport.js. |
| [MS-identity – Python – WebApp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Ukazují, jak integrovat B2C platformy Microsoft Identity Platform pomocí webové aplikace v Pythonu.  |

## <a name="single-page-apps"></a>Jednostránkové aplikace

| Ukázka | Popis |
|--------| ----------- |
| [MS-identity – B2C-JavaScript – Spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | Jediná stránková aplikace (SPA), která volá webové rozhraní API. Ověřování se provádí s Azure AD B2C pomocí MSAL.js. Tato ukázka používá tok autorizačního kódu s PKCE. |
| [JavaScript – NodeJS – Správa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter1) | Jediná stránková aplikace (SPA) volá Microsoft Graph pro správu uživatelů v adresáři B2C. Ověřování se provádí s Azure AD B2C pomocí MSAL.js. Tato ukázka používá tok autorizačního kódu s PKCE.|
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Jediná stránková aplikace (SPA), která volá webové rozhraní API. Ověřování se provádí s Azure AD B2C pomocí MSAL.js. Tato ukázka používá implicitní tok.|

## <a name="consoledaemon-apps"></a>Aplikace pro konzolu nebo démona

| Ukázka | Popis |
|--------| ----------- |
| [JavaScript – NodeJS – Správa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter2) | Aplikace démona konzoly Node.js a Express volá Microsoft Graph s vlastní identitou pro správu uživatelů v adresáři B2C. Ověřování se provádí pomocí Azure AD B2C pomocí uzlu MSAL. Tato ukázka používá tok autorizačního kódu.|
| [dotnetcore-B2C – Správa účtů](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) | Konzolová aplikace .NET Core volá Microsoft Graph s vlastní identitou pro správu uživatelů v adresáři B2C. Ověřování se provádí s Azure AD B2C pomocí MSAL.NET. Tato ukázka používá tok autorizačního kódu.|

## <a name="saml-test-application"></a>Testovací aplikace SAML

| Ukázka | Popis |
|--------| ----------- |
| [SAML-SP-Tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | Testovací aplikace SAML pro otestování Azure AD B2C nakonfigurovaná tak, aby fungovala jako zprostředkovatel identity SAML. |

## <a name="api-connectors"></a>Konektory rozhraní API

Následující tabulky obsahují odkazy na ukázky kódu pro využití webových rozhraní API ve vašich uživatelských tocích pomocí [konektorů rozhraní API](api-connectors-overview.md).

### <a name="azure-function-quickstarts"></a>Rychlé starty funkcí Azure

| Ukázka                                                                                                                          | Popis                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Tato ukázka .NET Core Azure Functions ukazuje, jak omezit podepisování na konkrétní e-mailové domény a ověřit informace zadané uživatelem. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Tato Node.js ukázka Azure Functions ukazuje, jak omezit podepisování na konkrétní e-mailové domény a ověřit informace zadané uživatelem.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Tato ukázka Azure Functions v Pythonu ukazuje, jak omezit podepisování na konkrétní e-mailové domény a ověřit informace zadané uživatelem.    |


### <a name="automated-fraud-protection-services--captcha"></a>Automatické služby ochrany před podvody & CAPTCHA
| Ukázka                                                                                                            | Popis                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Podvod a ochrana před zneužitím v Arkose Labs](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | V této ukázce se dozvíte, jak chránit vaše uživatelské přihlašování pomocí Arkose Labs a služby ochrany před zneužitím. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | V této ukázce se dozvíte, jak chránit uživatele pomocí reCAPTCHA výzvy k tomu, abyste zabránili automatizovanému zneužití. |


### <a name="identity-verification"></a>Ověření identity

| Ukázka                                                                                                            | Popis                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | V této ukázce se dozvíte, jak ověřit identitu uživatele v rámci vašich registračních toků pomocí konektoru rozhraní API pro integraci s IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | V této ukázce se dozvíte, jak ověřit identitu uživatele v rámci vašich registračních toků pomocí konektoru rozhraní API pro integraci s Experian. |


### <a name="other"></a>Jiné

| Ukázka                                                                                                            | Popis                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Kód pozvánky](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | Tato ukázka demonstruje, jak omezit registraci na konkrétní cílové skupiny pomocí kódů pozvánky.|
| [Ukázky komunitních konektorů API](https://github.com/azure-ad-b2c/api-connector-samples) | Toto úložiště má komunitu udržované vzorky scénářů povolených konektory rozhraní API.|
