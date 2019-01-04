---
title: Knihovny ověřování Azure Active Directory v2.0 | Dokumentace Microsoftu
description: Kompatibilní klientské knihovny a server knihovny middleware a související knihovny, zdroje a odkazy na ukázky, pro koncový bod služby Azure Active Directory v2.0.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2018
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: a7034e65b0c1e804326e5c087b320f8e7ffde907
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557086"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Knihovny ověřování Azure Active Directory v2.0

[Koncový bod Azure Active Directory (Azure AD) verze 2.0](active-directory-v2-compare.md) podporuje standardních oborových protokolů OAuth 2.0 a OpenID Connect 1.0. Knihovna Microsoft Authentication Library (MSAL) je navržené pro práci s koncovým bodem v2.0 Azure AD. Je také možné použít open source knihoven, které podporují OAuth 2.0 a OpenID Connect 1.0.

Doporučuje se, že používáte knihovny napsané pomocí protokolu odborníci, kteří provádějí Security Development Lifecycle (SDL) metody, jako je třeba [společností Microsoft a potom je][Microsoft-SDL]. Pokud se rozhodnete ručně kód pro protokoly, postupujte jako SDL od Microsoftu a platit podle zavřete pozornost informace o zabezpečení ve specifikacích standardy pro každý protokol metody.

> [!NOTE]
> Hledáte služby Azure AD v1.0 library (ADAL)? Rezervace [knihovnu ADAL průvodce](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Typy knihoven

Koncový bod Azure AD v2.0 pracuje se dvěma typy knihoven:

* **Klientské knihovny**: Nativní klienty a servery získat přístupové tokeny pro volání prostředku, jako je například Microsoft Graphu pomocí klientských knihoven.
* **Server knihovny middleware**: Webových aplikací pomocí serveru knihovnami middleware pro přihlášení uživatele. Webová rozhraní API používat server knihovny middleware pro ověření tokenů, které se odesílají nativní klienty nebo jiné servery.

## <a name="library-support"></a>Podpora knihovny

Knihovny jsou ve dvou kategoriích podpory:

* **Microsoft nepodporuje**: Microsoft poskytuje opravy pro tyto knihovny a dokončení procesu SDL vypršení platnosti co nejopatrněji na těchto knihovnách.
* **Kompatibilní**: Microsoft testovala tyto knihovny v základní scénáře a potvrdit, že fungují s koncovým bodem v2.0. Společnost Microsoft neposkytuje oprav pro tyto knihovny a přehled těchto knihoven, jako jednička. Problémy a žádosti o funkce směrovat do projektu open source knihovny.

Seznam knihoven, které pracují s koncovým bodem v2.0 najdete v další části tohoto článku.

## <a name="microsoft-supported-client-libraries"></a>Microsoft nepodporuje klientské knihovny

Klientské knihovny pro ověřování se používají k získání tokenu volat chráněné webové rozhraní API

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace | Koncepční doc | Plán |
| --- | --- | --- | --- | --- | --- | --- | ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js (Preview) | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [Jednostránková aplikace](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  | [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki)|
|![Angular JS](media/sample-v2-code/logo_angular.png) | Knihovna MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Úhlová](media/sample-v2-code/logo_angular.png) | Knihovna MSAL Angular(Preview) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | Knihovna MSAL .NET (Preview) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplikace klasické pracovní plochy](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [Plán](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![iOS / Objective C nebo swiftu.](media/sample-v2-code/logo_iOS.png) | Knihovna MSAL obj_c (Preview) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [aplikace pro iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android / Javu](media/sample-v2-code/logo_Android.png) | Knihovna MSAL (ve verzi Preview) | [ Centrální úložiště](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikace pro Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Server nepodporuje Microsoft middleware knihovny

Middleware knihovny se používají k ochraně webových aplikací a webových rozhraní API. Pro webovou aplikaci nebo webové rozhraní API, napsané v ASP.NET nebo ASP.NET Core, jsou knihovny middleware použitý technologií ASP.NET nebo ASP.NET Core

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Zabezpečení technologie ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[Zabezpečení technologie ASP.NET (GitHub)](https://github.com/aspnet/Security) |[Aplikace MVC](quickstart-v2-aspnet-webapp.md) |[Reference k rozhraní API technologie ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Rozšíření IdentityModel pro .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Aplikace MVC](quickstart-v2-aspnet-webapp.md) |[Referenční informace](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webové aplikace](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>Kompatibilní klientské knihovny

| Platforma | Název knihovny | Otestované verze | Zdrojový kód | Ukázka |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[JEDNOSTRÁNKOVÁ APLIKACE](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Zapisovatel Java](https://github.com/scribejava/scribejava) | [Verze 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP ligy oauth2 klienta](https://github.com/thephpleague/oauth2-client) | [Verze 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [Klient oauth2](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
![iOS](media/sample-v2-code/logo_iOS.png) |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Ukázky v nativní aplikaci](active-directory-v2-devquickstarts-ios.md) |

Pro všechny kompatibilní se standardy knihovny můžete použít koncovým bodem v2.0, proto je důležité vědět, kde lze získat podporu.

* Pro problémy a žádostí o nové funkce v kódu knihovny obraťte se na vlastníka knihovny.
* Pro problémy a žádostí o nové funkce v implementaci protokolu na straně služby získáte od Microsoftu.
* [Soubor žádosti o funkci](https://feedback.azure.com/forums/169401-azure-active-directory) pro další funkce, které byste chtěli vidět v protokolu.
* [Vytvořit žádost o podporu](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Pokud narazíte na problém, kdy koncový bod Azure AD v2.0 není kompatibilní s OAuth 2.0 nebo OpenID Connect 1.0.

## <a name="related-content"></a>Související obsah

Další informace o koncový bod Azure AD v2.0, najdete v článku [Přehled služby Azure AD app modelu v2.0][AAD-App-Model-V2-Overview].

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
