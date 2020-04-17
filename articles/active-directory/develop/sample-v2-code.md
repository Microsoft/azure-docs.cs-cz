---
title: Ukázky kódu pro platformu identit Microsoftu
description: Poskytuje index dostupných vzorků kódu platformy microsoft identit (v2.0 koncový bod), uspořádané podle scénáře.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 15578b6adc19eb3513e0d7e1d3d2c400c9170250
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535974"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Ukázky kódu platformy identity Microsoftu (koncový bod v2.0)

Platformu identit Microsoftu můžete použít k:

- Přidejte ověřování a autorizaci do webových aplikací a webových api.
- Vyžadovat přístupový token pro přístup k chráněnému webovému rozhraní API.

Tento článek stručně popisuje a poskytuje odkazy na ukázky pro koncový bod platformy identity Microsoftu. Tyto ukázky ukazují, jak se to dělá, a také poskytují fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázkového kódu najdete podrobná témata readme, která vám pomohou s požadavky, instalací a nastavením. Komentáře v kódu vám pomohou porozumět kritickým částem.

> [!NOTE]
> Pokud máte zájem o ukázky v1.0, najdete v [tématu ukázky kódu Azure AD (v1.0 koncový bod).](../azuread-dev/sample-v1-code.md)

Chcete-li porozumět základnímu scénáři pro každý typ ukázky, [přečtěte si informace o typech aplikací pro koncový bod platformy identit microsoftu](v2-app-types.md).

Můžete také přispět k ukázkám na GitHubu. Informace o tom najdete v [tématu Ukázky a dokumentace služby Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Jednostránkové aplikace

Tyto ukázky ukazují, jak napsat jednostránkovou aplikaci zabezpečenou platformou identit microsoftu. Tyto vzorky používají jednu z příchutí MSAL.js.

| Platforma | Popis | Odkaz |
| -------- | --------------------- | -------- |
| ![Tento obrázek znázorňuje logo](media/sample-v2-code/logo_js.png) [JavaScript u Java (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Volání Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Tento obrázek znázorňuje logo](media/sample-v2-code/logo_js.png) [JavaScript u Java (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Volání B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Tento obrázek znázorňuje logo](media/sample-v2-code/logo_js.png) [JavaScript u Java (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Volání vlastního webového rozhraní API |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Tento obrázek znázorňuje angular logo](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Volání Microsoft Graph  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Tento obrázek znázorňuje angular logo](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Volání B2C |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>Webové aplikace

Následující ukázky ilustrují webové aplikace, které přihlašují uživatele. Některé ukázky také ukazují aplikace volající Microsoft Graph nebo vlastní webové rozhraní API s identitou uživatele.

| Platforma | Pouze znaky u uživatelů | Přihlášení uživatelů a volání Microsoft Graphu |
| -------- | ------------------- | --------------------------------- |
| ![Tento obrázek znázorňuje logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET jádro 2.2 | [ASP.NET základní webapp přihlášení uživatelé tutorial](https://aka.ms/aspnetcore-webapp-sign-in) | Stejná ukázka ve [webové aplikaci ASP.NET Core volá](https://aka.ms/aspnetcore-webapp-call-msgraph) fázi Microsoft Graphu |
| ![Tento obrázek znázorňuje logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [rychlý start ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-omezené-obory-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Tento obrázek znázorňuje logo Javy](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Tento obrázek znázorňuje logo Pythonu](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Na tomto obrázku je logo Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-školení-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Desktopové a mobilní veřejné klientské aplikace

Následující ukázky zobrazují veřejné klientské aplikace (desktopové nebo mobilní aplikace), které přistupují k rozhraní Microsoft Graph API nebo k vlastnímu webovému rozhraní API jménem uživatele. Všechny tyto klientské aplikace používají Microsoft Authentication Library (MSAL).

| Klientská aplikace | Platforma | Tok/Grant | Volání Microsoft Graph | Volá webové rozhraní API ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Plocha (WPF)      | ![Tento obrázek znázorňuje logo .NET/C#](media/sample-v2-code/logo_NET.png) | [Interaktivní](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-nativní-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Plocha (konzola)   | ![Tento obrázek znázorňuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NET.png) | [Integrované ověřování systému Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Plocha (konzola)   | ![Tento obrázek znázorňuje logo Javy](media/sample-v2-code/logo_java.png) | [Integrované ověřování systému Windows](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Plocha (konzola)   | ![Tento obrázek znázorňuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Uživatelské jméno/heslo](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Plocha (konzola) s WAM  | ![Tento obrázek znázorňuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [interaktivní s WAM](msal-authentication-flows.md#interactive) |[dotnet-nativní-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Plocha (konzola)   | ![Tento obrázek znázorňuje logo Javy](media/sample-v2-code/logo_java.png) | [Uživatelské jméno/heslo](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Plocha (konzola)   | ![Tento obrázek znázorňuje logo Pythonu](media/sample-v2-code/logo_python.png) | [Uživatelské jméno/heslo](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Mobilní (Android, iOS, UPW)   | ![Tento obrázek znázorňuje logo .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Interaktivní](msal-authentication-flows.md#interactive) |[xamarin-nativní-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobilní (iOS)       | ![Tento obrázek znázorňuje iOS/Objective-C nebo Swift](media/sample-v2-code/logo_iOS.png) | [Interaktivní](msal-authentication-flows.md#interactive) |[ios-swift-objc-nativní-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-nativní-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Plocha (macOS)       | macOS | [Interaktivní](msal-authentication-flows.md#interactive) |[macOS-swift-objc-nativní-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobilní (Android-Java)   | ![Tento obrázek znázorňuje logo Androidu](media/sample-v2-code/logo_Android.png) | [Interaktivní](msal-authentication-flows.md#interactive) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobilní (Android-Kotlin)   | ![Tento obrázek znázorňuje logo Androidu](media/sample-v2-code/logo_Android.png) | [Interaktivní](msal-authentication-flows.md#interactive) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Aplikace daemonu

Následující ukázky ukazují aplikaci, která přistupuje k rozhraní Microsoft Graph API s vlastní identitou (bez uživatele).

| Klientská aplikace | Platforma | Tok/Grant | Volání Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konzola | ![Tento obrázek znázorňuje logo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webová aplikace | ![Tento obrázek znázorňuje logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Konzola | ![Tento obrázek znázorňuje logo Javy](media/sample-v2-code/logo_java.png) | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Konzola | ![Tento obrázek znázorňuje logo Pythonu](media/sample-v2-code/logo_python.png) | [Pověření klienta](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Bezhlavé aplikace

Následující ukázka ukazuje veřejnou klientskou aplikaci spuštěnou na zařízení bez webového prohlížeče. Aplikace může být nástroj příkazového řádku, aplikace běžící na Linuxu nebo Macu nebo aplikace IoT. Ukázka obsahuje aplikaci, která přistupuje k rozhraní Microsoft Graph API, jménem uživatele, který se interaktivně přihlásí na jiném zařízení (například na mobilním telefonu). Tato klientská aplikace používá Microsoft Authentication Library (MSAL).

| Klientská aplikace | Platforma | Tok/Grant | Volání Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Plocha (konzola)   | ![Tento obrázek znázorňuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Tok kódu zařízení](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Plocha (konzola)   | ![Tento obrázek znázorňuje logo Javy](media/sample-v2-code/logo_java.png) | [Tok kódu zařízení](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Plocha (konzola)   | ![Tento obrázek znázorňuje logo Pythonu](media/sample-v2-code/logo_python.png) | [Tok kódu zařízení](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Webová rozhraní API

Následující ukázky ukazují, jak chránit webové rozhraní API s koncovým bodem platformy identit microsoftu a jak volat rozhraní API pro příjem dat z webového rozhraní API.

| Platforma | Ukázka |
| -------- | ------------------- |
| ![Tento obrázek znázorňuje logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET jádro 2.2 | ASP.NET Základní webové rozhraní API (služba) [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Tento obrázek znázorňuje logo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Webové rozhraní API (služba) [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Tento obrázek znázorňuje logo Javy](media/sample-v2-code/logo_java.png) | Webové ROZHRANÍ API (služba) [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Tento obrázek znázorňuje logo Node.js](media/sample-v2-code/logo_nodejs.png) | Webové rozhraní API (služba) [služby active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Tento obrázek znázorňuje logo Node.js](media/sample-v2-code/logo_nodejs.png) | Webové rozhraní B2C (služba) [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure funguje jako webová api

Následující ukázky ukazují, jak chránit funkci Azure pomocí HttpTrigger a vystavení webovérozhraní API s koncovým bodem platformy identity Microsoftu a jak volat rozhraní API pro příjem dat z webového rozhraní API.

| Platforma | Ukázka |
| -------- | ------------------- |
| ![Tento obrázek znázorňuje logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET jádro 2.2 | ASP.NET základní webové rozhraní API (služba) Azure funkce [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Tento obrázek znázorňuje logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Webové rozhraní API (služba) [nodeJs a passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Tento obrázek znázorňuje logo Pythonu](media/sample-v2-code/logo_python.png)</p>Python | Webové ROZHRANÍ API (služba) [Pythonu](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Tento obrázek znázorňuje logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Webové rozhraní API (služba) [nodejs a passport-azure-ad pomocí jménem](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Další ukázky aplikace Microsoft Graph

Další informace o [ukázkách](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) a kurzech, které ukazují různé vzorce využití rozhraní Microsoft Graph API, včetně ověřování pomocí Azure AD, najdete [v tématu ukázky komunity Microsoft Graph & kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Viz také

- [Průvodce vývojáři služby Azure Active Directory (v1.0)](../azuread-dev/v1-overview.md)
- [Koncepční a referenční informace rozhraní API rozhraní Microsoft Graph](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
