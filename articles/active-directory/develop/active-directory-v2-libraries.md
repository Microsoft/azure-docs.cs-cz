---
title: Knihovny ověřování Azure Active Directory v2.0 | Microsoft Docs
description: Kompatibilní klientské knihovny a knihovny middlewaru serveru a související knihovny, zdroje a odkazy ukázky pro koncový bod v2.0 Azure Active Directory.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/22/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 0d9e2831f9d8676eb3e7fac91c58f3977f2e0f32
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Knihovny ověřování Azure Active Directory v2.0
[Koncového bodu v2.0 Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) podporuje standardní protokoly OAuth 2.0 a OpenID Connect 1.0. Můžete použít různé knihovny od společnosti Microsoft a jiných organizací s koncovým bodem v2.0.

Když vytvoříte aplikaci, která používá koncový bod v2.0, doporučujeme používat knihovny, které jsou zapsány odborníky protokolu domény, kteří podle metodika životního cyklu SDL (Security Development), jako je třeba [ten následuje Microsoft][Microsoft-SDL]. Pokud se rozhodnete k ruční kódu podporu pro protokoly, doporučujeme, postupujte podle SDL metodika a zaměřit na aspekty zabezpečení do specifikací standardy pro každý protokol.

> [!NOTE]
> Hledáte verze 1.0 knihovny Azure AD (ADAL)? Najdete v článku věnovaném [knihovna ADAL průvodce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Knihovny typů
Koncového bodu Azure AD v2.0 funguje s dva typy knihovny:

* **Knihovny klienta**. Nativní klienty a servery používat k získání přístupových tokenů pro volání prostředku, jako je například Microsoft Graph knihovny klienta.
* **Server knihovny middleware**. Webové aplikace používat server knihovny middleware pro přihlášení uživatele. Webová rozhraní API slouží k ověření tokeny, které se odesílají klienti nativní nebo jinými servery knihovny middlewaru serveru.

## <a name="library-support"></a>Podpora knihovny
Protože všechny standardům knihovny můžete vybrat, kdy používáte koncový bod v2.0, je důležité vědět, kde lze získat podporu. Pro problémy a žádosti o funkce v kódu knihovny obraťte se na vlastníka knihovny. Problémy a žádosti o funkce v implementaci protokolu na straně služby od společnosti Microsoft.

Knihovny se ve dvou kategoriích podpory:

* **Podporované Microsoft**. Společnost Microsoft poskytuje opravy pro tyto knihovny a provedla SDL kvůli opatrností na tyto knihovny.
* **Kompatibilní**. Společnost Microsoft tyto knihovny otestována základní scénáře a potvrdit, že fungují s koncovým bodem v2.0. Společnost Microsoft neposkytuje opravy pro tyto knihovny a nebyl provádí kontrolu tyto knihovny. Problémy a žádosti o funkce se mají směrovat knihovny open-source projekt.

Seznam knihovny, které pracují s koncovým bodem v2.0 naleznete v dalších částech v tomto článku.


## <a name="microsoft-supported-client-libraries"></a>Knihovny Microsoft podporované klienta

> [!IMPORTANT]
> Knihovny preview MSAL jsou vhodné pro použití v provozním prostředí. Poskytujeme produkční úrovni podporu pro tyto knihovny jako naše aktuální produkční knihovny (ADAL). Ve verzi Preview můžeme provést změny rozhraní API MSAL, formát vnitřní mezipaměti a další mechanismy tyto knihovny bez upozornění, která bude nutné provést společně s opravy chyb nebo vylepšení funkcí. To může mít vliv na vaše aplikace. Ke změně formátu mezipaměti, může mít vliv uživatelům, jako je například požadavek, aby znovu přihlásit. O rozhraní API změnu může vyžadovat aktualizaci vašeho kódu. Když jsme poskytují obecné dostupnosti verze, bude vyžadovat aktualizace na verzi obecné dostupnosti šest měsíců, jako aplikace napsané v náhledu verzi knihovny může přestane fungovat.

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Windows Store, UWP Xamarin iOS a Android | MSAL .NET (Preview) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplikace na ploše](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (Preview) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Jedna stránka aplikace](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | MSAL (Preview) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplikace pro iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (Preview) | [Centrální úložiště](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikace pro Android](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Knihovny Microsoft podporované server middleware

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | Middleware OWIN OpenID Connect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[Webu CodePlex](http://katanaproject.codeplex.com) |[Aplikace MVC](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | Middleware OWIN nosiče OAuth pro AzureAD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[Webu CodePlex](http://katanaproject.codeplex.com) |  | |
| .NET 4.x | Obslužná rutina JWT pro rozhraní .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | Middleware OpenID Connect ASP.NET |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Zabezpečení technologie ASP.NET (Githubu)][ServerLib-NetCore-Owin-Oidc-Repo] |[Aplikace MVC](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | Middleware nosiče OAuth ASP.NET |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Zabezpečení technologie ASP.NET (Githubu)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | Obslužná rutina JWT pro .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webové aplikace](active-directory-v2-devquickstarts-node-web.md)| |

## <a name="compatible-client-libraries"></a>Kompatibilní klient knihovny
| Platforma | Název knihovny | Otestované verze | Zdrojový kód | Ukázka |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Ukázka nativní aplikace](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Ukázka nativní aplikace](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribejava Scribe Java](https://github.com/scribejava/scribejava) | [Verze 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | |
| PHP | [PHP ligy oauth2 klienta](https://github.com/thephpleague/oauth2-client) | [Verze 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2 klienta](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Související obsah
Další informace o koncový bod v2.0 Azure AD, najdete v článku [přehled v2.0 modelu aplikace Azure AD][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
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

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
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
