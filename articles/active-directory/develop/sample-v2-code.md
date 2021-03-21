---
title: Ukázky kódu pro Microsoft Identity Platform
description: Poskytuje rejstřík dostupných ukázek kódu Microsoft Identity Platform uspořádaných podle scénáře.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: dc740acb366212640015ca0fb527d34fade1097d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010758"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft Identity Platform Code Samples (koncový bod verze 2.0)

Platforma Microsoft Identity můžete použít k těmto akcím:

- Přidejte k webovým aplikacím a webovým rozhraním API ověřování a autorizaci.
- Vyžadování přístupového tokenu pro přístup k chráněnému webovému rozhraní API.

Tento článek stručně popisuje a poskytuje odkazy na ukázky pro platformu Microsoft Identity Platform. Tyto ukázky ukazují, jak je to hotové, a také poskytují fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázka kódu najdete podrobná témata týkající se souboru Readme, která vám pomůžou s požadavky, instalací a nastavením. Komentáře v kódu vám pomůžou pochopit důležité oddíly.

Pokud chcete pochopit základní scénář pro každý typ vzorku, přečtěte si téma [typy aplikací pro platformu Microsoft Identity](v2-app-types.md).

Můžete také přispívat na ukázky na GitHubu. Další informace najdete v tématu [Microsoft Azure Active Directory Samples and Documentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Jednostránkové aplikace

Tyto ukázky ukazují, jak napsat jednostránkovou aplikaci zabezpečenou technologií Microsoft Identity Platform. Tyto ukázky používají jeden z typů MSAL.js.

| Platforma | Description | Odkaz |
| -------- | --------------------- | -------- |
| ![Tento obrázek ukazuje JavaScript logo JavaScript ](media/sample-v2-code/logo_js.png) [(MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Microsoft Graph volání SPA |[JavaScript – graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Tento obrázek ukazuje JavaScript logo JavaScript ](media/sample-v2-code/logo_js.png) [(MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) . | Zabezpečené ověřování hesla – volání Microsoft Graph pomocí toku kódu ověřování s/PKCE |[JavaScript – v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![Tento obrázek ukazuje JavaScript logo JavaScript ](media/sample-v2-code/logo_js.png) [(MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | B2C, volání hesla |[B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Tento obrázek ukazuje JavaScript logo JavaScript ](media/sample-v2-code/logo_js.png) [(MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) . | SPA volá B2C pomocí toku kódu ověřování s/PKCE. |[B2C-JavaScript – Spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![Tento obrázek ukazuje JavaScript logo JavaScript ](media/sample-v2-code/logo_js.png) [(MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) . | SPA volá vlastní webové rozhraní API, které zase volá Microsoft Graph  | [MS-identity-JavaScript-tutorial-chapter4-OBO](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph) |
| ![Tento obrázek znázorňuje úhlové logo ](media/sample-v2-code/logo_angular.png) [(MSALý úhlové)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) .| Microsoft Graph volání SPA  | [Active-Directory-JavaScript-singlepageapp-úhlová](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Na tomto obrázku je znázorněno úhlové logo ](media/sample-v2-code/logo_angular.png) [(MSAL, úhlové 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) .| Zabezpečené ověřování hesla – volání Microsoft Graph pomocí toku kódu ověřování s/PKCE | [MS-identity-JavaScript-úhlové Spa](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa) |
| ![Na tomto obrázku je znázorněno úhlové logo ](media/sample-v2-code/logo_angular.png) [(MSAL, úhlové 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) .| SPA volá vlastní webové rozhraní API. | [MS-identity-JavaScript-úhlové ověřování – aspnetcore-WebApi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Tento obrázek znázorňuje úhlové logo ](media/sample-v2-code/logo_angular.png) [(MSALý úhlové)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) . | B2C, volání hesla |[Active-Directory-B2C-JavaScript-úhlů-Spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Na tomto obrázku je znázorněno úhlové logo ](media/sample-v2-code/logo_angular.png) [(MSAL, úhlové 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) . | SPA volá vlastní webové rozhraní API s aplikačními rolemi a skupinami zabezpečení. |[MS-identity-JavaScript-úhlová-Spa-dotnetcore-WebApi-role-skupiny](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |
| ![Tento obrázek ukazuje reakci na reakce loga ](media/sample-v2-code/logo_react.png) [(MSAL reaguje)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react) .| Zabezpečené ověřování hesla – volání Microsoft Graph pomocí toku kódu ověřování s/PKCE | [MS-identity-JavaScript – reakce – Spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) |
| ![Tento obrázek ukazuje reakci na reakce loga ](media/sample-v2-code/logo_react.png) [(MSAL reaguje)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react) .| SPA volá vlastní webové rozhraní API. | [MS-identity – JavaScript – reakce – kurz](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api) |
| ![Tento obrázek ukazuje reakci na reakci loga ](media/sample-v2-code/logo_react.png) [(MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) .| SPA volá vlastní webové rozhraní API, které zase volá Microsoft Graph  | [MS-identity-JavaScript-reakci-Spa-dotnetcore-WebApi-OBO](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Tento obrázek ukazuje Blazor logo ](media/sample-v2-code/logo-blazor.png) [Blazor WebAssembly (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | Kurz Blazor WebAssembly pro přihlašování uživatelů a volání rozhraní API pomocí Azure Active Directory |[MS-identity – blazor-wasm](https://github.com/Azure-Samples/ms-identity-blazor-wasm) |

## <a name="web-applications"></a>Webové aplikace

Následující ukázky ilustrují webové aplikace, které přihlásí uživatele. Některé ukázky také ukazují, že aplikace volá Microsoft Graph, nebo vaše vlastní webové rozhraní API s identitou uživatele.

| Platforma | Pouze přihlášení uživatelé | Přihlašování uživatelů a volání Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Tento obrázek ukazuje logo ASP.NET Core.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [Kurz pro uživatele ASP.NET Core WebApp přihlašování uživatelů](https://aka.ms/aspnetcore-webapp-sign-in) | Stejná ukázka v [ASP.NET Core volání webové aplikace Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fáze</p>Pokročilý ukázkový [přístup k mezipaměti tokenů pro přihlášeného uživatele z aplikací, rozhraní API a služeb na pozadí](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) |
| ![Tento obrázek ukazuje logo rozhraní ASP.NET Framework.](media/sample-v2-code/logo_NETframework.png)</p>ASP.NET Core | [AD FS k migraci aplikací Azure AD PlayBook pro vývojáře](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) , kde se dozvíte, jak bezpečně a bezpečně migrovat aplikace integrované s Active Directory Federation Services (AD FS) (AD FS) do Azure Active Directory (Azure AD) | |
| ![Tento obrázek ukazuje logo rozhraní ASP.NET Framework.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Rychlý Start ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-WebApp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet – s omezením admin-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[MSGraph – školení – aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png)  |[Kurz Java servlet – kapitola 1,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Přihlášení pomocí AAD| |
| ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png)  |[Kurz Java servlet – kapitola 1,2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Přihlaste se pomocí B2C |
| ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png)  | | [Kurz Java servlet – kapitola 2,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Přihlaste se pomocí AAD a grafu volání.|
| ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png)  |[Kurz Java servlet – kapitola 3,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Přihlaste se pomocí AAD a řízení přístupu pomocí deklarace identity rolí.| |
| ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png)  | | [Kurz Java servlet – kapitola 3,2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Přihlaste se pomocí AAD a řízení přístupu pomocí deklarací skupin|
| ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png) | |[Kurz Java servlet – kapitola 4,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Nasadit do Azure App Service|
| ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png)  | | [MS-identity – Java – WebApp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png)  | [MS-identity-B2C-Java-servlet-WebApp-Authentication](https://github.com/Azure-Samples/ms-identity-b2c-java-servlet-webapp-authentication)|  |
| ![Tento obrázek ukazuje logo Node.js.](media/sample-v2-code/logo_nodejs.png)</p>Node.js (uzel MSAL) | [Kurz pro uživatele přihlašování k aplikaci Express Web App](https://github.com/Azure-Samples/ms-identity-node) | |
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)  | [Kurz na baňce Python – kapitola 1,1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Přihlášení pomocí AAD  |  |
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)  | [Kurz na baňce Python – kapitola 1,2](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Přihlaste se pomocí B2C                    |  |
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)  | | [Kurz na baňce Python – kapitola 2,1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Přihlaste se pomocí AAD a grafu volání. |
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)  | |[Kurz na baňce Python – kapitola 3,1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Nasadit do Azure App Service  |
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)  | [Kurz Python Django – kapitola 1,1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)   Přihlášení pomocí AAD  | |
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)  | [Kurz Python Django – kapitola 1,2](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Přihlaste se pomocí B2C                    |  |
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)  | | [Kurz Python Django – kapitola 2,1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)  Přihlaste se pomocí AAD a grafu volání.|
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)  | | [Kurz Python Django – kapitola 3,1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Nasadit do Azure App Service                    |
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)  | | [Webová aplikace Python Flask](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Tento obrázek ukazuje logo Ruby](media/sample-v2-code/logo_ruby.png) |                   | [MSGraph – školení – rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |
| ![Na tomto obrázku vidíte logo Blazor](media/sample-v2-code/logo-blazor.png)</p>Blazor Server | [Kurz přihlašování k aplikacím Blazor Server App-in Users](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC) | [Microsoft Graph volání aplikace serveru Blazor](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph)</p>Kurz k Chapterwise: [aplikace Blazor serveru pro přihlašování uživatelů a volání rozhraní API pomocí Azure Active Directory](https://github.com/Azure-Samples/ms-identity-blazor-server) |

## <a name="desktop-and-mobile-public-client-apps"></a>Desktopové a mobilní veřejné klientské aplikace

Následující ukázky ukazují veřejné klientské aplikace (desktopové nebo mobilní aplikace), které přistupují k rozhraní Microsoft Graph API nebo vlastní webové rozhraní API v názvu uživatele. Kromě ukázek na *ploše (konzole) se sadou WAM* používají všechny tyto klientské aplikace knihovnu Microsoft Authentication Library (MSAL).

| Klientská aplikace | Platforma | Tok a udělení | Volání Microsoft Graph | Volá ASP.NET Core webové rozhraní API. |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Kurz pro stolní počítače (.NET Core) – volitelně použijte:</p>– mezipaměť tokenů pro různé platformy</p>– vlastní webové uživatelské rozhraní | ![Tento obrázek ukazuje logo .NET/C#](media/sample-v2-code/logo_NETcore.png) | [Autorizační kód](msal-authentication-flows.md#authorization-code)| [MS-identity-dotnet-Desktop-kurz](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| Plocha (WPF)      | ![Na tomto obrázku vidíte logo Desktop/C# pro .NET.](media/sample-v2-code/logo_NET.png) | [Autorizační kód](msal-authentication-flows.md#authorization-code)| [dotnet – plocha-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet – nativní – aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Plocha (konzola)   | ![Obrázek, který zobrazuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NET.png) | [Integrované ověřování systému Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet – IWA-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png) | [Integrované ověřování systému Windows](msal-authentication-flows.md#integrated-windows-authentication) |[MS-identity – Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Plocha (konzola)   | ![Toto je logo .NET/C# (Desktop).](media/sample-v2-code/logo_NETcore.png) | [Uživatelské jméno a heslo](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Plocha (konzola) se službou WAM  | ![Toto je logo pro .NET/C# (Desktop).](media/sample-v2-code/logo_NETcore.png) | Interaktivní s nástrojem [Web Account Manager](/windows/uwp/security/web-account-manager) (WAM) |[dotnet – nativní – UWP – WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png) | [Uživatelské jméno a heslo](msal-authentication-flows.md#usernamepassword) |[MS-identity – Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png) | [Uživatelské jméno a heslo](msal-authentication-flows.md#usernamepassword) |[MS-identity – Python – Desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Plocha (elektronická)   | ![Tento obrázek ukazuje logo Node.js.](media/sample-v2-code/logo_nodejs.png)</p>Node.js (uzel MSAL) | [Autorizační kód (PKCE)](msal-authentication-flows.md#authorization-code) |[MS-identity-JavaScript-NodeJS-Desktop](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| Mobilní zařízení (Android, iOS, UWP)   | ![Tento obrázek ukazuje logo .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Autorizační kód](msal-authentication-flows.md#authorization-code) |[Xamarin-Native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobilní zařízení (iOS)       | ![Tento obrázek znázorňuje iOS/objektivní-C nebo SWIFT.](media/sample-v2-code/logo_iOS.png) | [Autorizační kód](msal-authentication-flows.md#authorization-code) |[iOS-SWIFT-objc-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [iOS – nativní-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Plocha (macOS)       | macOS | [Autorizační kód](msal-authentication-flows.md#authorization-code) |[macOS-SWIFT-objc-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobilní (Android – Java)   | ![Tento obrázek ukazuje logo Androidu.](media/sample-v2-code/logo_Android.png) | [Autorizační kód](msal-authentication-flows.md#authorization-code) |  [Android – Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobilní (Android – Kotlin)   | ![Tento obrázek ukazuje logo Androidu.](media/sample-v2-code/logo_Android.png) | [Autorizační kód](msal-authentication-flows.md#authorization-code) |  [Android – Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Aplikace démona

Následující ukázky ukazují aplikaci, která přistupuje k rozhraní Microsoft Graph API s vlastní identitou (bez uživatele).

| Klientská aplikace | Platforma | Tok a udělení | Volání Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konzola | ![Tento obrázek ukazuje logo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [dotnetcore-démon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webová aplikace | ![Snímek obrazovky zobrazující logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [dotnet-démon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Konzola | ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png) | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [MS-identity – Java – démon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Konzola | ![Tento obrázek ukazuje logo Node.js.](media/sample-v2-code/logo_nodejs.png)</p>Node.js (uzel MSAL)| [Pověření klienta](msal-authentication-flows.md#client-credentials) | [MS-identity-JavaScript-NodeJS-Console](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| Konzola | ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png) | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [MS-identity-Python-démon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Bezobslužné aplikace

Následující ukázka ukazuje aplikaci veřejného klienta běžící na zařízení bez webového prohlížeče. Aplikace může být nástroj příkazového řádku, aplikace spuštěná v systému Linux nebo Mac nebo v aplikaci IoT. Ukázková aplikace přistupují k rozhraní Microsoft Graph API, a to jménem uživatele, který se přihlásí interaktivně na jiném zařízení (například mobilní telefon). Tato klientská aplikace používá knihovnu Microsoft Authentication Library (MSAL).

| Klientská aplikace | Platforma | Tok a udělení | Volání Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo .NET/C# (Desktop).](media/sample-v2-code/logo_NETcore.png) | [Tok kódu zařízení](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png) | [Tok kódu zařízení](msal-authentication-flows.md#device-code) |[MS-identity – Java – devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Plocha (konzola)   | ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png) | [Tok kódu zařízení](msal-authentication-flows.md#device-code) |[MS-identity – Python – devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>Víceklientské aplikace SaaS

Následující ukázky ukazují, jak nakonfigurovat aplikaci tak, aby přijímala přihlášení z libovolného tenanta Azure Active Directory (Azure AD). Konfigurace vaší aplikace pro *více tenantů* znamená, že můžete nabídnout aplikaci SaaS ( **software jako služba** ) pro mnoho organizací, aby se jejich uživatelé mohli po poskytnutí souhlasu přihlašovat k vaší aplikaci.

| Platforma | Description | Odkaz |
| -------- | --------------------- | -------- |
| ![Na tomto obrázku je znázorněno úhlové logo ](media/sample-v2-code/logo_angular.png) [(MSAL, úhlové 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) . | Volání SPA s více klienty Graph API |[MS-identity-JavaScript-úhlů-Spa-ASPNET-WebApi-víceklientské tenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Na tomto obrázku je znázorněno úhlové logo ](media/sample-v2-code/logo_angular.png) [(MSAL, úhlové 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) . | Víceklientské ověřování SPA volá vlastní webové rozhraní API pro více tenantů. |[MS-identity-JavaScript-úhlů-Spa-ASPNET-WebApi-víceklientské tenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![Tento obrázek ukazuje ASP.NET Core logo ](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) . | Volání webové aplikace ASP.NET Core MVC Graph API |[Active-Directory-aspnetcore-WebApp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![Tento obrázek ukazuje ASP.NET Core logo ](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) . | Webová aplikace ASP.NET Core MVC volá ASP.NET Core webové rozhraní API. |[Active-Directory-aspnetcore-WebApp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Webová rozhraní API

Následující ukázky ukazují, jak chránit webové rozhraní API pomocí platformy Microsoft Identity Platform a jak volat rozhraní API pro příjem dat z webového rozhraní API.

| Platforma | Ukázka |
| -------- | ------------------- |
| ![Tento obrázek ukazuje logo ASP.NET Core.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core webové rozhraní API (služba) [dotnet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Na tomto obrázku vidíte logo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Webové rozhraní API (služba) [MS-identity-ASPNET-WebApi-OnBehalfOf](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo_java.png) | Webové rozhraní API (služba) [MS-identity-Java-WebApi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Tento obrázek ukazuje logo Node.js.](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Webové rozhraní API (služba) [Active-Directory-JavaScript-NodeJS-WebApi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Tento obrázek ukazuje logo Node.js.](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Webové rozhraní API B2C (služba) [služby Active-Directory-B2C-JavaScript-NodeJS-WebApi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions jako webová rozhraní API

Následující ukázky ukazují, jak chránit funkci Azure pomocí HttpTrigger a vystavit webové rozhraní API s platformou Microsoft identity a jak volat rozhraní API pro příjem dat z webového rozhraní API.

| Platforma | Ukázka |
| -------- | ------------------- |
| ![Tento obrázek ukazuje logo ASP.NET Core.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core rozhraní Web API (služba) Azure function of [dotnet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo_python.png)</p>Python | Webové rozhraní API (služba) [Pythonu](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Tento obrázek ukazuje logo Node.js.](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Webové rozhraní API (služba) [Node.js a Passport-Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Tento obrázek ukazuje logo Node.js.](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Webové rozhraní API (služba) [Node.js a Passport – Azure-AD s využitím jménem](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Další ukázky Microsoft Graph

Další informace o [ukázkách](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) a kurzech, které ukazují různé způsoby používání rozhraní Microsoft Graph API, včetně ověřování pomocí Azure AD, najdete v tématu [Microsoft Graph komunitních ukázek & kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Viz také

[Koncepční a referenční informace k rozhraní Microsoft Graph API](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)
