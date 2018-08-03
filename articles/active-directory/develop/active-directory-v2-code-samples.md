---
title: Ukázky kódu pro Azure Active Directory | Dokumentace Microsoftu
description: Poskytuje vzorové kódy, uspořádané podle scénáře index k dispozici Azure Active Directory (koncového bodu V2).
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 51a50693a97a011d00d055eff8d3a0846512bce5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441404"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Ukázky kódu Azure Active Directory (koncového bodu V2)

Microsoft Azure Active Directory (Azure AD) můžete použít pro:

- Přidat ověřování a autorizace do webových aplikací a webových rozhraní API.
- Vyžadovat přístupový token pro přístup k chráněné webové rozhraní API.

Tento článek stručně popisuje a poskytuje odkazy na ukázky pro koncový bod Azure AD V2. Tyto ukázky předvádějí, jak se to dělá, spolu s fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázky kódu najdete podrobný soubor Readme pro témata, která pomáhají s požadavky, instalace a nastavení. Existují komentáře v kódu vám pomůžou pochopit kritických oddílů.

> [!NOTE]
> Pokud vás zajímají ukázky V1, přečtěte si téma [ukázky kódu Azure AD (verze 1 koncový bod)](active-directory-code-samples.md).

Základní scénář pro každý typ vzorku najdete v tématu [typů aplikací pro koncový bod služby Azure Active Directory v2.0](active-directory-v2-flows.md).

Také můžete přispívat na ukázky na Githubu. Další informace o postupu [Microsoft Azure Active Directory ukázky a dokumentaci](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop a mobile veřejné klientské aplikace.

Následující ukázky ukazují veřejné klientské aplikace (desktop a mobilní aplikace), s přístupem k Microsoft Graphu nebo webové rozhraní API jménem uživatele.

Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu | Volání webového rozhraní API ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Plochy (WPF)      | .NET / C#  | Interaktivní | [DotNet desktopu msgraph v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet-admin-s omezením pomocí specifikátoru obory v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet – nativní aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Mobilní zařízení (UPW)   | .NET / C# (UPW) | Interaktivní | [DotNet nativní UPW v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobilní zařízení (Android, iOS, UPW)   | .NET / C# (Xamarin) | Interaktivní | [xamarin – nativní v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobilní zařízení (iOS)       | iOS / Objective C nebo swiftu. | Interaktivní | [IOS swift nativní v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [IOS native nxoauth2 v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobilní zařízení (Android)   | Android / Javu | Interaktivní |   [Android native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Webové aplikace

Následující ukázky ukazují webové aplikace, které přihlašování uživatelů, volání Microsoft Graphu nebo volání webového rozhraní API s využitím identity uživatele.

 Platforma | Pouze přihlášení uživatele | Přihlásí uživatele a volání Microsoft Graphu 
 -------- | ------------------- | --------------------------------- 
ASP.NET 4.x | [appmodelv2 webapp openIDConnect dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [DotNet webapp openidconnect v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
ASP.NET Core 2.0 | [aspnetcore webapp openidconnect v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2 WebApp OpenIDConnect nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [Ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>Démon procesu aplikace

Následující ukázky ukazují desktopových nebo webových aplikací s přístupem k Microsoft Graphu nebo webové rozhraní API s využitím identity aplikace (žádný uživatel).

Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu 
------------------ | -------- | ---------- | -------------------- 
Webová aplikace | .NET / C#  | Přihlašovací údaje klienta | [DotNet – démon v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>Jednostránkové aplikace (SPA)

Tato ukázka předvádí, jak psát jednostránkovou aplikaci zabezpečené službou Azure AD.

 Platforma |  Volání Microsoft Graphu 
 -------- |  --------------------- 
JavaScript (msal.js)  | [JavaScript – graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [angular-connect-rest-sample](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [JavaScript – graphapi-web-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + Angular 4) | [angular4-connect-sample](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>Webová rozhraní API

### <a name="web-api-protected-by-azure-ad"></a>Webové rozhraní API chráněné službou Azure AD

Následující příklad ukazuje způsob ochrany webového rozhraní API ke koncovému bodu Azure AD V2.

Platforma | Ukázka | Popis
 -------- | ------------------- | ---------------------
Node.js | [DotNet – nativní aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | Volání na rozhraní Web API ASP.NET Core z aplikace WPF pomocí Azure AD V2.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webové rozhraní API volání Microsoft Graphu nebo jiného webového rozhraní API

Tato ukázka ještě není k dispozici.

## <a name="other-microsoft-graph-samples"></a>Další ukázky Microsoft Graphu

Další informace o [ukázky](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) a kurzy, které ukazují odlišnými vzory použití pro Microsoft Graph API, včetně ověřování pomocí Azure AD, najdete v článku [komunity Microsoft Graph ukázky a kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Další informace najdete v tématech

[Příručka pro vývojáře pro službu Azure Active Directory](azure-ad-developers-guide.md)

[Azure AD Graph API konceptuální a referenční dokumentace](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API pomocné knihovny](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
