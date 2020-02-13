---
title: Ukázky kódu pro Microsoft Identity Platform | Microsoft Docs
description: Poskytuje index dostupných ukázek kódu platformy Microsoft Identity Platform (v 2.0) uspořádaných podle scénáře.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0282336cb5df7d010571a15e9d1f9d93b82d461e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160317"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft Identity Platform Code Samples (koncový bod verze 2.0)

Microsoft Identity Platform můžete použít k těmto akcím:

- Přidejte k webovým aplikacím a webovým rozhraním API ověřování a autorizaci.
- Vyžadování přístupového tokenu pro přístup k chráněnému webovému rozhraní API.

Tento článek stručně popisuje a poskytuje odkazy na ukázky pro koncový bod Microsoft Identity Platform. Tyto ukázky ukazují, jak je to hotové, a také poskytují fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázka kódu najdete podrobná témata týkající se souboru Readme, která vám pomůžou s požadavky, instalací a nastavením. Komentáře v kódu vám pomůžou pochopit důležité oddíly.

> [!NOTE]
> Pokud vás zajímá ukázky v 1.0, přečtěte si téma [ukázky kódu Azure AD (v 1.0 koncový bod)](../azuread-dev/sample-v1-code.md).

Pokud chcete pochopit základní scénář pro každý typ vzorku, přečtěte si téma [typy aplikací pro koncový bod Microsoft Identity Platform](v2-app-types.md).

Můžete také přispívat na ukázky na GitHubu. Další informace najdete v tématu [Microsoft Azure Active Directory Samples and Documentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Jednostránkové aplikace

Tyto ukázky ukazují, jak napsat jednostránkovou aplikaci zabezpečenou technologií Microsoft Identity Platform. Tyto ukázky používají jeden z charakterů MSAL. js.

| Platforma | Popis | Odkaz |
| -------- | --------------------- | -------- |
| ![tomto obrázku se zobrazuje logo JavaScriptu](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) . | Volání Microsoft Graph |[JavaScript – graphapi – web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![tomto obrázku se zobrazuje logo JavaScriptu](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) . | Volá B2C |[B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![tomto obrázku se zobrazuje logo JavaScriptu](media/sample-v2-code/logo_js.png) [JavaScript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) . | Volá vlastní webové rozhraní API. |[JavaScript-singlepageapp-dotnet-WebApi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![tomto obrázku se zobrazuje logo úhlového JS](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs) .| Volání Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MsalAngularjsDemoApp)
| ![tomto obrázku se zobrazuje logo úhlu](media/sample-v2-code/logo_angular.png) [JavaScriptu (MSAL úhlové)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) .| Volání Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Webové aplikace

Následující ukázky ilustrují webové aplikace, které přihlásí uživatele. Některé ukázky také ukazují, že aplikace volá Microsoft Graph, nebo vaše vlastní webové rozhraní API s identitou uživatele.

| Platforma | Pouze přihlášení uživatelé | Přihlašování uživatelů a volání Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Tento obrázek ukazuje logo ASP.NET Core.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | [Kurz pro uživatele ASP.NET Core WebApp přihlašování uživatelů](https://aka.ms/aspnetcore-webapp-sign-in) | Stejná ukázka v [ASP.NET Core volání webové aplikace Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fáze |
| ![Na tomto obrázku vidíte logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Rychlý Start ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-WebApp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet – s omezením admin-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[MSGraph – školení – aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png)  |                   | [MS-identity – Java – WebApp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)  |                   | [MS-identity – Python – WebApp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Tento obrázek ukazuje logo Node. js.](media/sample-v2-code/logo_nodejs.png)  |                   | [Rychlý Start pro Node. js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Tento obrázek ukazuje logo Ruby](media/sample-v2-code/logo_ruby.png) |                   | [MSGraph – školení – rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Desktopové a mobilní veřejné klientské aplikace

Následující ukázky ukazují veřejné klientské aplikace (desktopové nebo mobilní aplikace), které přistupují k rozhraní Microsoft Graph API nebo vlastní webové rozhraní API v názvu uživatele. Všechny tyto klientské aplikace používají Microsoft Authentication Library (MSAL).

| Klientská aplikace | Platforma | Tok a udělení | Volání Microsoft Graph | Volá webové rozhraní API ASP.NET Core 2,0. |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Plocha (WPF)      | ![Tento obrázek ukazuje rozhraní .NET/C# logo](media/sample-v2-code/logo_NET.png) | [Interaktivní](msal-authentication-flows.md#interactive)| [dotnet – plocha-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet – nativní – aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo .NET/C# (Desktop).](media/sample-v2-code/logo_NET.png) | [Integrované ověřování systému Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet – IWA-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png) | [Integrované ověřování systému Windows](msal-authentication-flows.md#integrated-windows-authentication) |[MS-identity – Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo .NET/C# (Desktop).](media/sample-v2-code/logo_NETcore.png) | [Uživatelské jméno a heslo](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Plocha (konzola) se službou WAM  | ![Tento obrázek ukazuje logo .NET/C# (Desktop).](media/sample-v2-code/logo_NETcore.png) | [interaktivní s WAM](msal-authentication-flows.md#interactive) |[dotnet – nativní – UWP – WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png) | [Uživatelské jméno a heslo](msal-authentication-flows.md#usernamepassword) |[MS-identity – Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png) | [Uživatelské jméno a heslo](msal-authentication-flows.md#usernamepassword) |[MS-identity – Python – Desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Mobilní zařízení (Android, iOS, UWP)   | ![Tento obrázek ukazuje logo .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Interaktivní](msal-authentication-flows.md#interactive) |[Xamarin-Native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobilní zařízení (iOS)       | ![Tento obrázek znázorňuje iOS/objektivní-C nebo SWIFT.](media/sample-v2-code/logo_iOS.png) | [Interaktivní](msal-authentication-flows.md#interactive) |[iOS-SWIFT-objc-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [iOS – nativní-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Plocha (macOS)       | macOS | [Interaktivní](msal-authentication-flows.md#interactive) |[macOS-SWIFT-objc-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobilní (Android – Java)   | ![Tento obrázek ukazuje logo Androidu.](media/sample-v2-code/logo_Android.png) | [Interaktivní](msal-authentication-flows.md#interactive) |  [Android – Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobilní (Android – Kotlin)   | ![Tento obrázek ukazuje logo Androidu.](media/sample-v2-code/logo_Android.png) | [Interaktivní](msal-authentication-flows.md#interactive) |  [Android – Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Aplikace démona

Následující ukázky ukazují aplikaci, která přistupuje k rozhraní Microsoft Graph API s vlastní identitou (bez uživatele).

| Klientská aplikace | Platforma | Tok a udělení | Volání Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konzola | ![Tento obrázek ukazuje logo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [dotnetcore-démon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webová aplikace | ![Na tomto obrázku vidíte logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [dotnet-démon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Konzola | ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png) | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [MS-identity – Java – démon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Konzola | ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png) | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [MS-identity-Python-démon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Bezobslužné aplikace

Následující ukázka ukazuje aplikaci veřejného klienta běžící na zařízení bez webového prohlížeče. Aplikace může být nástroj příkazového řádku, aplikace spuštěná v systému Linux nebo Mac nebo v aplikaci IoT. Ukázková aplikace přistupují k rozhraní Microsoft Graph API, a to jménem uživatele, který se přihlásí interaktivně na jiném zařízení (například mobilní telefon). Tato klientská aplikace používá knihovnu Microsoft Authentication Library (MSAL).

| Klientská aplikace | Platforma | Tok a udělení | Volání Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo .NET/C# (Desktop).](media/sample-v2-code/logo_NETcore.png) | [Tok kódu zařízení](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png) | [Tok kódu zařízení](msal-authentication-flows.md#device-code) |[MS-identity – Java – devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png) | [Tok kódu zařízení](msal-authentication-flows.md#device-code) |[MS-identity – Python – devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Webová rozhraní API

Následující ukázky ukazují, jak chránit webové rozhraní API pomocí koncového bodu Microsoft Identity Platform a jak volat rozhraní API pro příjem dat z webového rozhraní API.

| Platforma | Ukázka |
| -------- | ------------------- |
| ![Tento obrázek ukazuje logo ASP.NET Core.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | ASP.NET Core webové rozhraní API (služba) [dotnet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Na tomto obrázku vidíte logo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Webové rozhraní API (služba) [MS-identity-ASPNET-WebApi-OnBehalfOf](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png) | Webové rozhraní API (služba) [MS-identity-Java-WebApi](https://github.com/Azure-Samples/ms-identity-java-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions jako webová rozhraní API

Následující ukázky ukazují, jak chránit funkci Azure pomocí HttpTrigger a vystavit webové rozhraní API s koncovým bodem Microsoft Identity Platform a voláním rozhraní API pro příjem dat z webového rozhraní API.

| Platforma | Ukázka |
| -------- | ------------------- |
| ![Tento obrázek ukazuje logo ASP.NET Core.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | ASP.NET Core rozhraní Web API (služba) Azure function of [dotnet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Tento obrázek ukazuje logo Node. js.](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Webové rozhraní API (služba) služby [NodeJS a Passport-Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)</p>Python | Webové rozhraní API (služba) [Pythonu](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Tento obrázek ukazuje logo Node. js.](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Webové rozhraní API (služba) pro [NodeJS a Passport – Azure-AD s využitím jménem](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Další ukázky Microsoft Graph

Další informace o [ukázkách](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) a kurzech, které ukazují různé způsoby používání rozhraní Microsoft Graph API, včetně ověřování pomocí Azure AD, najdete v tématu [Microsoft Graph komunitních ukázek & kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Viz také

- [Příručka pro vývojáře Azure Active Directory (v 1.0)](../azuread-dev/v1-overview.md)
- [Azure AD Graph API koncepční a referenční dokumentace](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Pomocná knihovna Graph API Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
