---
title: Ukázky kódu Azure Active Directory | Microsoft Docs
description: Poskytuje index k dispozici Azure Active Directory (koncový bod V2) ukázky kódu, uspořádané podle scénáře.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2018
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 39c2c5adef72db830ef226228017065c1b70f496
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Ukázky kódu Azure Active Directory (koncový bod V2)

Microsoft Azure Active Directory (Azure AD) můžete použít k:

- Přidání ověřování a autorizace do webových aplikací a webové rozhraní API.
- Vyžadovat přístupový token pro přístup k chráněné webové rozhraní API.

Tento článek stručně popisuje a poskytuje odkazy na ukázky pro koncový bod Azure AD V2. Tyto ukázky vám ukážou, jak se to dokončí, společně s fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázkový kód najdete podrobné readme témata, která pomůže s požadavky, instalace a nastavení. Komentáře v kódu, které vám pomohou pochopit kritické oddíly existují.

> [!NOTE]
> Pokud vás zajímá ukázky V1, přečtěte si téma [ukázky kódu Azure AD (koncový bod V1)](active-directory-code-samples.md).

Abyste pochopili základní scénáře pro každý typ ukázka, najdete v části [typy aplikací pro koncový bod v2.0 Azure Active Directory](active-directory-v2-flows.md).

Také můžete přispívat k ukázky z webu GitHub. Další informace, jak zjistit, [Microsoft Azure Active Directory ukázky a dokumentace](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop a mobile veřejné klientské aplikace

Následující ukázky zobrazit veřejné klientské aplikace (desktop nebo mobile aplikace), které přístup k Microsoft Graph nebo webového rozhraní API jménem uživatele.

klientské aplikace | Platforma | Tok/Grant | Volání Microsoft Graph | Volá webové rozhraní API základní technologie ASP.NET 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)      | .NET / C#  | Interaktivní | [DotNet plochy msgraph v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet správce omezený obory v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet nativní aspnetcore v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Mobile (UWP)   | .NET / C# (UWP) | Interaktivní | [DotNet nativní uwp v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobile (Android, iOS, UWP)   | .NET / C# (Xamarin) | Interaktivní | [xamarin. nativní v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobile (iOS)       | iOS / Objective C nebo swiftu. | Interaktivní | [IOS swift nativní v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [IOS nativní nxoauth2 v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobile (Android)   | Android / Java | Interaktivní |   [Android – nativní v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Webové aplikace

Následující ukázky znázorňují webové aplikace, které přihlášení uživatele, Microsoft Graph volání nebo volání webového rozhraní API s identitu uživatele.

 Platforma | Pouze přihlášení uživatele | Přihlásí uživatele a volání Microsoft Graph 
 -------- | ------------------- | --------------------------------- 
ASP.NET 4.x | [appmodelv2 webapp openIDConnect dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [DotNet webapp openidconnect v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
Jádro ASP.NET 2.0 | [aspnetcore webapp openidconnect v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore připojit ukázka](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2 WebApp OpenIDConnect nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [Ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>Démon procesu aplikace

Následující ukázky zobrazit plochy nebo webové aplikace, které přístup k Microsoft Graph nebo webové rozhraní API s identitou aplikace (žádný uživatel).

klientské aplikace | Platforma | Tok/Grant | Volání Microsoft Graph 
------------------ | -------- | ---------- | -------------------- 
Webová aplikace | .NET / C#  | Pověření klienta | [DotNet. démon v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>Jednostránkové aplikace (SPA)

Tento příklad ukazuje, jak psát jednostránkové aplikace zabezpečené s Azure AD.

 Platforma |  Volání Microsoft Graph 
 -------- |  --------------------- 
JavaScript (msal.js)  | [JavaScript – graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [úhlová connect-rest-sample](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [JavaScript – graphapi-web-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + úhlová 4) | [angular4 připojit ukázka](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>Webová rozhraní API

### <a name="web-api-protected-by-azure-ad"></a>Webové rozhraní API, které jsou chráněné službou Azure AD

Následující příklad ukazuje, jak chránit webového rozhraní API s koncovým bodem Azure AD V2.

Platforma | Ukázka | Popis
 -------- | ------------------- | ---------------------
Node.js | [DotNet nativní aspnetcore v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | Zavolá rozhraní API webové jádro ASP.NET z grafického subsystému WPF aplikace pomocí Azure AD V2.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webové rozhraní API volání Microsoft Graph nebo jiného webového rozhraní API

Tato ukázka dosud nejsou k dispozici.

## <a name="other-microsoft-graph-samples"></a>Další ukázky Microsoft Graph

Další informace o [ukázky](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) a návodů, které ukazují různé vzorce pro Microsoft Graph API, včetně ověřování s Azure AD, najdete v části [ukázky Microsoft Graph Community & kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Další informace najdete v tématech

[Příručka pro vývojáře pro službu Azure Active Directory](active-directory-developers-guide.md)

[Azure AD Graph API koncepční a referenční informace](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API pomocné knihovny](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
