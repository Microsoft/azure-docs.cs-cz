---
title: Ukázky kódu pro Microsoft identity platform | Dokumentace Microsoftu
description: Poskytuje index k dispozici Microsoft identity ukázky kódu platformy (koncového bodu V2), uspořádané podle scénáře.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9a04dfd7009028197bee2602394a34803b1f8c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298361"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Ukázky kódu pro platformy Microsoft identity (koncový bod v2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Můžete použít Microsoft identity platform pro:

- Přidat ověřování a autorizace do webových aplikací a webových rozhraní API.
- Vyžadovat přístupový token pro přístup k chráněné webové rozhraní API.

Tento článek stručně popisuje a poskytuje odkazy na ukázky pro koncový bod služby Microsoft identity platform. Tyto ukázky předvádějí, jak se to dělá, spolu s fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázky kódu najdete podrobný soubor Readme pro témata, která pomáhají s požadavky, instalace a nastavení. Existují komentáře v kódu vám pomůžou pochopit kritických oddílů.

> [!NOTE]
> Pokud vás zajímají ukázky v1.0, přečtěte si téma [ukázky kódu Azure AD (koncový bod verze 1.0)](sample-v1-code.md).

Základní scénář pro každý typ vzorku najdete v tématu [typů aplikací pro koncový bod služby Microsoft identity platform](v2-app-types.md).

Také můžete přispívat na ukázky na Githubu. Další informace o postupu [Microsoft Azure Active Directory ukázky a dokumentaci](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>Jednostránkové aplikace (SPA)

Tyto ukázky předvádějí, jak psát jednostránkovou aplikaci zabezpečené s platformou identity Microsoft. Tyto ukázky použijte jeden z typů MSAL.js:

* [Microsoft Authentication Library pro JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)
* [Microsoft Authentication Library pro Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)
* [Microsoft Authentication Library pro AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

| Platforma | Volání Microsoft Graphu |
| -------- | --------------------- |
| ![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Úhlová](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Webové aplikace

Následující ukázky ukazují webové aplikace, které se přihlásit uživatele. Některé ukázky ukazují také aplikace volání Microsoft Graphu, nebo vlastní webové rozhraní API s využitím identity uživatele.

| Platforma | Pouze přihlášení uživatele | Přihlásí uživatele a volání Microsoft Graphu |
| -------- | ------------------- | --------------------------------- |
| ![Jádro ASP.NET](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.1 | [Kurz přihlásí uživatele základní webové aplikace ASP.NET](https://aka.ms/aspnetcore-webapp-sign-in) | Stejný ukázkový v [webové aplikace ASP.NET Core volání Microsoft Graphu](https://aka.ms/aspnetcore-webapp-call-msgraph) fáze |
| ![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Rychlý start technologie ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Rychlý úvod k Node.js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop a mobile veřejné klientské aplikace.

Následující ukázky ukazují veřejné klientské aplikace (desktop a mobilní aplikace), s přístupem k rozhraní Microsoft Graph API nebo vlastního webového rozhraní API jménem uživatele. Tyto klientské aplikace používají knihovny Microsoft Authentication (MSAL).

| Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu | Volání webového rozhraní API ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Plochy (WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | Interaktivní | [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Plocha (konzola)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NET.png) | Integrované ověřování systému Windows | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Plocha (konzola)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Uživatelské jméno/heslo |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Mobilní zařízení (Android, iOS, UPW)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interaktivní |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobilní zařízení (iOS)       | ![iOS / Objective C nebo swiftu.](media/sample-v2-code/logo_iOS.png) | Interaktivní |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Mobilní zařízení (Android)   | ![Android / Javu](media/sample-v2-code/logo_Android.png) | Interaktivní |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |  |

## <a name="daemon-applications"></a>Démon procesu aplikace

Následující ukázky ukazují aplikaci, která má přístup k rozhraní Microsoft Graph API s využitím vlastní identity (se žádný uživatel).

| Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu |
| ------------------ | -------- | ---------- | -------------------- |
| Konzola | ![.NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | Přihlašovací údaje klienta | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webová aplikace | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | Přihlašovací údaje klienta | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Bezobslužného aplikací

Následující příklad ukazuje veřejné klientské aplikace běžící na zařízeních bez webového prohlížeče. Aplikace může být nástroj příkazového řádku nebo běžet na systému Linux/Mac nebo IoT aplikaci. Ukázka funkce aplikace přístup k rozhraní Microsoft Graph API jménem uživatele, který přihlásí interaktivně na jiném zařízení (např. mobilní telefon). Tato klientská aplikace používá knihovny MicroSoft Authentication (MSAL).

| Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu |
| ------------------ | -------- |  ----------| ---------- |
| Plocha (konzola)   | ![.NET / C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Tok kódu zařízení |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>Webová rozhraní API

Následující příklad ukazuje způsob ochrany webového rozhraní API s koncovým bodem Microsoft identity platform. V aplikaci WPF se provede toto rozhraní API, ale mohou být volány žádné aplikace. Webové rozhraní API také volání Microsoft Graphu.

| Platforma | Ukázka |
| -------- | ------------------- |
| ![.NET/C#](media/sample-v2-code/logo_NET.png) | WebAPI (služba) z [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) |

## <a name="other-microsoft-graph-samples"></a>Další ukázky Microsoft Graphu

Další informace o [ukázky](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) a kurzy, které ukazují odlišnými vzory použití pro Microsoft Graph API, včetně ověřování pomocí Azure AD, najdete v článku [komunity Microsoft Graph ukázky a kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Další informace najdete v tématech

- [Průvodce vývojáře Azure Active Directory (verze 1.0).](v1-overview.md)
- [Azure AD Graph API konceptuální a referenční dokumentace](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Azure AD Graph API pomocné knihovny](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
