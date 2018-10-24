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
ms.date: 10/17/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7323f532787fa767d875e13262cce9f1ceaa79d8
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955509"
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
![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
![Úhlová](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Webové aplikace

Následující ukázky ukazují webové aplikace, které se přihlásit uživatele. Některé ukázky ukazují také aplikace volání Microsoft Graphu, nebo vlastní webové rozhraní API s využitím identity uživatele.

 Platforma | Pouze přihlášení uživatele | Přihlásí uživatele a volání Microsoft Graphu
 -------- | ------------------- | ---------------------------------
![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET | [appmodelv2 webapp openIDConnect dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [DotNet webapp openidconnect v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [aspnetcore webapp openidconnect v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [AppModelv2 WebApp OpenIDConnect nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [Ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop a mobile veřejné klientské aplikace.

Následující ukázky ukazují veřejné klientské aplikace (desktop a mobilní aplikace), které přistupují k Microsoft Graphu nebo vlastního webového rozhraní API jménem uživatele. Tyto klientské aplikace pomocí knihovny MicroSoft Authentication (MSAL)

Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu | Volání webového rozhraní API ASP.NET Core 2.0
------------------ | -------- |  ----------| ---------- | -------------------------
Plochy (WPF)      | ![.NET / C#](media/sample-v2-code/logo_NET.png) | Interaktivní | [DotNet desktopu msgraph v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet-admin-s omezením pomocí specifikátoru obory v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet – nativní aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Plocha (konzola)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NET.png) | Integrované ověřování systému Windows |[DotNet – iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
Plocha (konzola)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Uživatelské jméno/heslo |[verze 2 dotnetcore nahoru](https://github.com/azure-samples/active-directory-dotnetcore-up-v2)
Mobilní zařízení (UPW)   | ![.NET / C# (UPW)](media/sample-v2-code/logo_windows.png) | Interaktivní |[DotNet nativní UPW v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobilní zařízení (Android, iOS, UPW)   | ![.NET / C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interaktivní |[xamarin – nativní v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobilní zařízení (iOS)       | ![iOS / Objective C nebo swiftu.](media/sample-v2-code/logo_iOS.png) | Interaktivní |[IOS swift nativní v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [IOS native nxoauth2 v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobilní zařízení (Android)   | ![Android / Javu](media/sample-v2-code/logo_Android.png) | Interaktivní |  [Android native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Démon procesu aplikace

Následující ukázkové aplikace, která má přístup k Microsoft Graphu pomocí vlastní identity (se žádný uživatel).

Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu
------------------ | -------- | ---------- | --------------------
Konzola | ![.NET Core](media/sample-v2-code/logo_NETcore.png)<p/> ASP.NET  | Přihlašovací údaje klienta | [dotnetcore – démon v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
Webová aplikace | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET  | Přihlašovací údaje klienta | [DotNet – démon v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>Bezobslužného aplikací

Následující ukázky ukazují veřejným klientem aplikace běžící na zařízení bez webového prohlížeče. Tyto aplikace může být nástroje příkazového řádku nebo aplikace spuštěné v systému Linux/Mac nebo iOT aplikaci. Ukázky funkcí aplikace přístup k Microsoft Graphu názvu uživatele, který přihlásí interaktivně na jiném zařízení (například mobilní telefon). Tyto klientské aplikace pomocí knihovny MicroSoft Authentication (MSAL)

Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu
------------------ | -------- |  ----------| ----------
Plocha (konzola)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Tok kódu při zařízení |[dotnetcore. devicecodeflow v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>Webová rozhraní API

Následující příklad ukazuje způsob ochrany webového rozhraní API ke koncovému bodu Azure AD V2. Toto rozhraní API vykonávají aplikace WPF (ale může být volána ve skutečnosti ve všech aplikacích)

Platforma | Ukázka
 -------- | -------------------
![.NET / C#](media/sample-v2-code/logo_NET.png) | [DotNet – nativní aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Další ukázky Microsoft Graphu

Další informace o [ukázky](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) a kurzy, které ukazují odlišnými vzory použití pro Microsoft Graph API, včetně ověřování pomocí Azure AD, najdete v článku [komunity Microsoft Graph ukázky a kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Další informace najdete v tématech

[Příručka pro vývojáře pro službu Azure Active Directory](v1-overview.md)

[Azure AD Graph API konceptuální a referenční dokumentace](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API pomocné knihovny](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
