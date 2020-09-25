---
title: Knihovny ověřování platformy Microsoft identity
description: Kompatibilní klientské knihovny a knihovny middlewaru serveru společně se související knihovnou, zdrojovou a ukázkovou odkazy pro koncový bod Microsoft Identity Platform
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 072407ae64a8dc6083ffdca23ba9c758b2b35868
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257805"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Knihovny ověřování platformy Microsoft identity

[Koncový bod platformy Microsoft Identity](../azuread-dev/azure-ad-endpoint-comparison.md) podporuje standardní protokoly OAuth 2,0 a OpenID Connect 1,0. Knihovna Microsoft Authentication Library (MSAL) je navržená tak, aby fungovala s koncovým bodem Microsoft Identity Platform. Můžete také použít Open source knihovny, které podporují OAuth 2,0 a OpenID Connect 1,0.

Doporučujeme, abyste používali knihovny zapsané odborníky na doménu protokolu, kteří používají metodologii SDL (Security Development Lifecycle). Takové metodologie zahrnují [tu, kterou Microsoft sleduje][Microsoft-SDL]. Pokud si zadáte kód pro protokoly, měli byste postupovat podle metodologie, jako je například Microsoft SDL. Věnujte velkou pozornost hlediskům zabezpečení v specifikacích standardů pro jednotlivé protokoly.

> [!NOTE]
> Hledáte knihovnu Azure Active Directory Authentication Library (ADAL)? Projděte si [příručku ke knihovně ADAL](../azuread-dev/active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Typy knihoven

Koncový bod Microsoft Identity Platform pracuje se dvěma typy knihoven:

* **Klientské knihovny**: nativní klienti a servery používají klientské knihovny k získání přístupových tokenů pro volání prostředku, jako je Microsoft Graph.
* **Knihovny middlewarů serveru**: webové aplikace používají pro přihlášení uživatele knihovny middlewaru serveru. Webová rozhraní API pomocí knihoven middlewaru serveru ověřují tokeny, které jsou posílány nativními klienty nebo jinými servery.

## <a name="library-support"></a>Podpora knihoven

Knihovny přicházejí ve dvou kategoriích podpory:

* **Microsoft podporuje**: Microsoft poskytuje opravy pro tyto knihovny a na těchto knihovnách dokončila provedenou péči SDL.
* **Kompatibilní**: Společnost Microsoft testovala tyto knihovny v základních scénářích a potvrzuje, že pracují s koncovým bodem Microsoft Identity Platform. Microsoft neposkytuje opravy pro tyto knihovny a neprovádí revize těchto knihoven. Žádosti o problémy a funkce by měly být směrovány na open source projekt knihovny.

Seznam knihoven, které pracují s koncovým bodem Microsoft Identity Platform, najdete v následujících částech.

## <a name="microsoft-supported-client-libraries"></a>Klientské knihovny podporované Microsoftem

Použijte knihovny ověřování klientů k získání tokenu pro volání chráněného webového rozhraní API.

| Platforma | Knihovna | Stáhnout | Zdrojový kód | Ukázka | Referenční informace | Koncepční dokument | Plán |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Jednostránková aplikace](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Odkaz](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [Koncepční dokumentace](msal-overview.md)| [Plán](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Úhlová](media/sample-v2-code/logo_angular.png) | MSALý úhlový | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [Úhlové SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [Odkaz](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [Koncepční dokumentace](msal-overview.md) | [Plán](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Desktopová aplikace](/windows/apps/desktop/) | [MSAL.NET](/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Koncepční dokumentace](msal-overview.md) | [Plán](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Ukázky](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Plán](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL v Javě | [Maven](https://search.maven.org/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Ukázky](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Odkaz](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Plán](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS a macOS | MSAL iOS a macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [aplikace pro iOS](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [aplikace MacOS](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Odkaz](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Koncepční dokumentace](msal-overview.md) | |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Centrální úložiště](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikace pro Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Koncepční dokumentace](msal-overview.md) |[Plán](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Knihovny middlewaru serveru podporované Microsoftem

Pomocí knihoven middlewaru můžete chránit webové aplikace a webová rozhraní API. Webové aplikace nebo webová rozhraní API napsaná pomocí ASP.NET nebo ASP.NET Core používají knihovny middlewaru.

| Platforma | Knihovna | Stáhnout | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Zabezpečení ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[Aplikace MVC](quickstart-v2-aspnet-webapp.md) |[Referenční dokumentace k rozhraní API technologie ASP.NET](/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Rozšíření IdentityModel pro .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Aplikace MVC](quickstart-v2-aspnet-webapp.md) |[Odkaz](/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webová aplikace](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Knihovny podporované Microsoftem podle operačního systému/jazyka

V případě podporovaných operačních systémů vs se jedná o mapování v následujících jazycích:

| Platforma    | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (macOS)       | MSAL.Net (Xamarin. iOS) | MSAL.Net (Xamarin. Android)|
| Swift <br> Objective-C |            |            | [MSAL pro iOS a MacOS](msal-overview.md) | [MSAL pro iOS a MacOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.Js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passport. Node | Passport. Node | Passport. Node |

Další informace najdete v tématu [scénáře podle podporovaných platforem a jazyků](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages) .

## <a name="compatible-client-libraries"></a>Kompatibilní klientské knihovny

| Platforma | Název knihovny | Testovaná verze | Zdrojový kód | Ukázka |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 1.13.5 verze |[Hello.js](https://github.com/MrSwitch/hello.js) |[OVĚŘOVÁNÍ](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
|![Vue](media/sample-v2-code/logo_vue.png)|[Vue MSAL](https://github.com/mvertopoulos/vue-msal) | 3.0.3 verze |[Vue – msal](https://github.com/mvertopoulos/vue-msal) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Zapisovatelný Java](https://github.com/scribejava/scribejava) | [3.2.0 verze](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Knihovna Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | [3.0.2 verze](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Knihovna Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Požadavky – OAuthlib](https://github.com/requests/requests-oauthlib) | [1.2.0 verze](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Požadavky – OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [OpenID – klient](https://github.com/panva/node-openid-client) | [2.4.5 verze](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [OpenID – klient](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [League PHP OAuth2 – klient](https://github.com/thephpleague/oauth2-client) | [1.4.2 verze](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [OAuth2 – klient](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-OAuth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS & Android  | [Reakce na nativní ověřování aplikací](https://github.com/FormidableLabs/react-native-app-auth) | [4.2.0 verze](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reakce na nativní ověřování aplikací](https://github.com/FormidableLabs/react-native-app-auth) | |

Pro všechny knihovny vyhovující standardům můžete použít koncový bod Microsoft Identity Platform. Je důležité, abyste věděli, kde je potřeba získat podporu:

* Pro problémy a nové žádosti o funkce v kódu knihovny se obraťte na vlastníka knihovny.
* Problémy a požadavky na nové funkce v rámci implementace protokolu na straně služby vám poskytne společnost Microsoft.
* Zadáte [žádost o funkci](https://feedback.azure.com/forums/169401-azure-active-directory) pro další funkce, které chcete v protokolu zobrazit.
* Pokud zjistíte problém, kde koncový bod platformy Microsoft identity není kompatibilní s OAuth 2,0 nebo OpenID Connect 1,0, [vytvořte žádost o podporu](../../azure-portal/supportability/how-to-create-azure-support-request.md) .

## <a name="related-content"></a>Související obsah

Další informace o koncovém bodu Microsoft Identity Platform najdete v tématu [Přehled platformy Microsoft Identity][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: ./tutorial-v2-windows-desktop.md
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
[ServerLib-Net4-Owin-Oidc-Sample]: ./tutorial-v2-asp-webapp.md
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
