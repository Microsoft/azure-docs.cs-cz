---
title: Knihovny ověřování identit platformy Microsoft | Dokumenty společnosti Microsoft
description: Kompatibilní klientské knihovny a knihovny middlewaru serveru spolu se souvisejícími odkazy na knihovnu, zdroj a ukázkové odkazy pro koncový bod platformy identit microsoftu.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: c8f0d5d948ff54522e951b7b5d18c7018ae6d34a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419808"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Knihovny ověřování platformy identit Microsoftu

Koncový [bod platformy identit Microsoft](active-directory-v2-compare.md) podporuje standardní protokoly OAuth 2.0 a OpenID Connect 1.0. Knihovna ověřování společnosti Microsoft (MSAL) je určena pro práci s koncovým bodem platformy identit microsoftu. Můžete také použít open source knihovny, které podporují OAuth 2.0 a OpenID Connect 1.0.

Doporučujeme používat knihovny napsané odborníky na doménu protokolu, kteří dodržují metodiku životního cyklu vývoje zabezpečení (SDL). Tyto metodiky zahrnují [ten, který společnost Microsoft následuje][Microsoft-SDL]. Pokud ručně kód pro protokoly, měli byste postupovat podle metodiky, jako je například Microsoft SDL. Věnujte velkou pozornost bezpečnostním aspektům ve specifikacích norem pro každý protokol.

> [!NOTE]
> Hledáte knihovnu ověřování služby Azure Active Directory (ADAL)? Podívejte se na [průvodce knihovnou ADAL](../azuread-dev/active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Typy knihoven

Koncový bod platformy identit microsoftu funguje se dvěma typy knihoven:

* **Klientské knihovny**: Nativní klienti a servery používají klientské knihovny k získání přístupových tokenů pro volání prostředku, jako je microsoft graph.
* **Knihovny middlewaru serveru**: Webové aplikace používají pro přihlášení uživatele knihovny middleware serveru. Webová api používají knihovny middlewaru serveru k ověření tokenů, které jsou odesílány nativními klienty nebo jinými servery.

## <a name="library-support"></a>Podpora knihovny

Knihovny se domýšlijí ve dvou kategoriích podpory:

* **Microsoft podporuje**: Společnost Microsoft poskytuje opravy pro tyto knihovny a provedl sdl due diligence na tyto knihovny.
* **Kompatibilní**: Společnost Microsoft testovala tyto knihovny v základních scénářích a potvrdila, že pracují s koncovým bodem platformy identit společnosti Microsoft. Společnost Microsoft neposkytuje opravy těchto knihoven a neprovedla kontrolu těchto knihoven. Problémy a požadavky na funkce by měly být směrovány do open source projektu knihovny.

Seznam knihoven, které pracují s koncovým bodem platformy identit microsoftu, najdete v následujících částech.

## <a name="microsoft-supported-client-libraries"></a>Klientské knihovny podporované společností Microsoft

Pomocí klientských ověřovacích knihoven získáte token pro volání chráněného webového rozhraní API.

| Platforma | Knihovna | Stáhnout | Zdrojový kód | Ukázka | Odkaz | Koncepční dokument | Plán |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Jednostránková aplikace](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Odkaz](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [Koncepční dokumenty](msal-overview.md)| [Plán](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Úhlová](media/sample-v2-code/logo_angular.png) | MSAL Úhlové | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [Úhlové SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [Odkaz](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [Koncepční dokumenty](msal-overview.md) | [Plán](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Desktopová aplikace](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Koncepční dokumenty](msal-overview.md) | [Plán](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [ukázky](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Plán](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL v Javě | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [ukázky](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Odkaz](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Plán](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS & macOS | MSAL iOS a macOS | [GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [aplikace pro iOS](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [aplikace pro macOS](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Odkaz](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Koncepční dokumenty](msal-overview.md) | |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Centrální úložiště](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikace pro Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Koncepční dokumenty](msal-overview.md) |[Plán](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Knihovny middlewaru podporovaných společností Microsoft

Pomocí middlewarových knihoven můžete chránit webové aplikace a webová api. Webové aplikace nebo webová api napsaná pomocí ASP.NET nebo ASP.NET Core používají middlewarové knihovny.

| Platforma | Knihovna | Stáhnout | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET zabezpečení |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHubu](https://github.com/aspnet/AspNetCore) |[Aplikace MVC](quickstart-v2-aspnet-webapp.md) |[Referenční dokumentace k rozhraní API technologie ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Rozšíření IdentityModel pro rozhraní .NET| |[GitHubu](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Aplikace MVC](quickstart-v2-aspnet-webapp.md) |[Odkaz](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHubu](https://github.com/AzureAD/passport-azure-ad) | [Webová aplikace](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Knihovny podporované společností Microsoft podle operačního systému / jazyka

Z hlediska podporovaných operačních systémů vs jazyky, mapování je následující:

|             | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Jádro, MSAL.Net (.NET FW, Core, UPW)| jádro ASP.NET, MSAL.Net (.NET Core) | ASP.NET jádro, MSAL.Net (macOS)       | MSAL.Net (Xamarin.iOS) | MSAL.Net (Xamarin.Android)|
| Swift <br> Objective-C |            |            | [MSAL pro iOS a MacOS](msal-overview.md) | [MSAL pro iOS a MacOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) Node.JS | Soubor Passport.node | Soubor Passport.node | Soubor Passport.node |

Viz také [Scénáře podle podporovaných platforem a jazyků](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Kompatibilní klientské knihovny

| Platforma | Název knihovny | Testovaná verze | Zdrojový kód | Ukázka |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | Verze 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[Spa](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Verze 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Knihovna Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | [Verze 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Knihovna Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Žádosti-OAuthlib](https://github.com/requests/requests-oauthlib) | [Verze 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Žádosti-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [Verze 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP Liga oauth2-klient](https://github.com/thephpleague/oauth2-client) | [Verze 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-klient](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[Omniauth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2: 1.4.0 |[Omniauth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Reagovat nativní App Auth](https://github.com/FormidableLabs/react-native-app-auth) | [Verze 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reagovat nativní App Auth](https://github.com/FormidableLabs/react-native-app-auth) | |

Pro všechny standardy kompatibilní knihovny můžete použít koncový bod platformy identit microsoftu. Je důležité vědět, kam jít pro podporu:

* V případě problémů a žádostí o nové funkce v kódu knihovny se obraťte na vlastníka knihovny.
* V případě problémů a požadavků na nové funkce v implementaci protokolu na straně služby se obraťte na společnost Microsoft.
* [Soubor požadavek na funkci](https://feedback.azure.com/forums/169401-azure-active-directory) pro další funkce, které chcete vidět v protokolu.
* Pokud zjistíte problém, kdy koncový bod platformy identit Microsoftu není kompatibilní s OAuth 2.0 nebo OpenID Connect 1.0, vytvořte [žádost o podporu.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)

## <a name="related-content"></a>Související obsah

Další informace o koncovém bodě platformy identit microsoftu najdete v přehledu [platformy identit microsoftu][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
