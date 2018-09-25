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
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e76097c0d0cbaf14f2fc2b1a407bc2d320a2091d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964383"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Ukázky kódu Azure Active Directory (koncového bodu V2)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Microsoft Azure Active Directory (Azure AD) můžete použít pro:

- Přidat ověřování a autorizace do webových aplikací a webových rozhraní API.
- Vyžadovat přístupový token pro přístup k chráněné webové rozhraní API.

Tento článek stručně popisuje a poskytuje odkazy na ukázky pro koncový bod Azure AD V2. Tyto ukázky předvádějí, jak se to dělá, spolu s fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázky kódu najdete podrobný soubor Readme pro témata, která pomáhají s požadavky, instalace a nastavení. Existují komentáře v kódu vám pomůžou pochopit kritických oddílů.

> [!NOTE]
> Pokud vás zajímají ukázky V1, přečtěte si téma [ukázky kódu Azure AD (verze 1 koncový bod)](sample-v1-code.md).

Základní scénář pro každý typ vzorku najdete v tématu [typů aplikací pro koncový bod služby Azure Active Directory v2.0](v2-app-types.md).

Také můžete přispívat na ukázky na Githubu. Další informace o postupu [Microsoft Azure Active Directory ukázky a dokumentaci](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>Jednostránkové aplikace (SPA)

Tato ukázka předvádí, jak psát jednostránkovou aplikaci zabezpečené službou Azure AD. Tyto ukázky použijte jeden z typů MSAL.js: [knihovna Microsoft Authentication Library pro JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core), [knihovna Microsoft Authentication Library pro Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular), [knihovna Microsoft Authentication Library pro AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

 Platforma |  Volání Microsoft Graphu
 -------- |  ---------------------
![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [JavaScript – graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (msal AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
![Úhlová](media/sample-v2-code/logo_angular.png) JavaScript (msal Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Webové aplikace

Následující ukázky ukazují webové aplikace, které se přihlásit uživatele. Některé ukázky ukazují také aplikace volání Microsoft Graphu, nebo vlastní webové rozhraní API s využitím identity uživatele.

 Platforma | Pouze přihlášení uživatele | Přihlásí uživatele a volání Microsoft Graphu
 -------- | ------------------- | ---------------------------------
![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET | [appmodelv2 webapp openIDConnect dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [DotNet webapp openidconnect v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [aspnetcore webapp openidconnect v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [AppModelv2 WebApp OpenIDConnect nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [Ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop a mobile veřejné klientské aplikace.

Následující ukázky ukazují veřejné klientské aplikace (desktop a mobilní aplikace), s přístupem k Microsoft Graphu nebo vlastní webové rozhraní API jménem uživatele pomocí interaktivního přihlášení. Tyto klientské aplikace pomocí knihovny MicroSoft Authentication (MSAL)

Klientská aplikace | Platforma | Volání Microsoft Graphu | Volání webového rozhraní API ASP.NET Core 2.0
------------------ | -------- |  -------------------- | -------------------------
Plochy (WPF)      | ![.NET / C#](media/sample-v2-code/logo_NET.png) | [DotNet desktopu msgraph v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet-admin-s omezením pomocí specifikátoru obory v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet – nativní aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Mobilní zařízení (UPW)   | ![.NET / C# (UPW)](media/sample-v2-code/logo_windows.png) | [DotNet nativní UPW v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobilní zařízení (Android, iOS, UPW)   | ![.NET / C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [xamarin – nativní v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobilní zařízení (iOS)       | ![iOS / Objective C nebo swiftu.](media/sample-v2-code/logo_iOS.png) | [IOS swift nativní v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [IOS native nxoauth2 v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobilní zařízení (Android)   | ![Android / Javu](media/sample-v2-code/logo_Android.png) |   [Android native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Démon procesu aplikace

Následující ukázkové aplikace, která má přístup k Microsoft Graphu pomocí vlastní identity (se žádný uživatel).

Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu
------------------ | -------- | ---------- | --------------------
Webová aplikace | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET  | Přihlašovací údaje klienta | [DotNet – démon v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

> Ukázka zobrazující aplikaci klasické pracovní plochy démon, je v nevyřízených položkách.

## <a name="web-apis"></a>Webová rozhraní API

Následující příklad ukazuje způsob ochrany webového rozhraní API ke koncovému bodu Azure AD V2. Toto rozhraní API vykonávají aplikace WPF (ale může být volána ve skutečnosti ve všech aplikacích)

Platforma | Ukázka
 -------- | -------------------
![.NET / C#](media/sample-v2-code/logo_NET.png) | [DotNet – nativní aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Další ukázky Microsoft Graphu

Další informace o [ukázky](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) a kurzy, které ukazují odlišnými vzory použití pro Microsoft Graph API, včetně ověřování pomocí Azure AD, najdete v článku [komunity Microsoft Graph ukázky a kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Další informace najdete v tématech

[Příručka pro vývojáře pro službu Azure Active Directory](azure-ad-developers-guide.md)

[Azure AD Graph API konceptuální a referenční dokumentace](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API pomocné knihovny](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
