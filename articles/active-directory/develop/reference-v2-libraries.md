---
title: Knihovny ověřování Azure Active Directory v2.0 | Dokumentace Microsoftu
description: Kompatibilní klientské knihovny a server knihovny middleware a související knihovny, zdroje a odkazy na ukázky, pro koncový bod služby Azure Active Directory v2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 1450cffca7a4cfa57856c75cdcc9e106958ea043
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601672"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Knihovny ověřování Azure Active Directory v2.0

[Koncový bod Azure Active Directory (Azure AD) verze 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) podporuje standardních oborových protokolů OAuth 2.0 a OpenID Connect 1.0. Knihovna Microsoft Authentication Library (MSAL) je navržené pro práci s koncovým bodem v2.0 Azure AD. Je také možné použít open source knihoven, které podporují OAuth 2.0 a OpenID Connect 1.0.

Doporučuje se, že používáte knihovny napsané pomocí protokolu odborníci, kteří provádějí Security Development Lifecycle (SDL) metody, jako je třeba [společností Microsoft a potom je][Microsoft-SDL]. Pokud se rozhodnete zajistit podporu ručně kódu pro protokoly, postupujte podle metody jako SDL od Microsoftu a platit podle zavřete pozornost informace o zabezpečení ve specifikacích standardy pro každý protokol.

> [!NOTE]
> Hledáte verze 1.0 knihovny služby Azure AD (ADAL)? Rezervace [knihovnu ADAL průvodce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Typy knihoven

Koncový bod Azure AD v2.0 pracuje se dvěma typy knihoven:

* **Klientské knihovny**. Nativní klienty a servery získat přístupové tokeny pro volání prostředku, jako je například Microsoft Graphu pomocí klientských knihoven.
* **Server knihovny middleware**. Webových aplikací pomocí serveru knihovnami middleware pro přihlášení uživatele. Webová rozhraní API používat server knihovny middleware pro ověření tokenů, které se odesílají nativní klienty nebo jiné servery.

## <a name="library-support"></a>Podpora knihovny

Můžete použít libovolné standardům knihovny a při použití koncového bodu v2.0, proto je důležité vědět, kde lze získat podporu. Pro problémy a žádosti o funkce v kódu knihovny obraťte se na vlastníka knihovny. Pro problémy a žádosti o funkce v implementaci protokolu na straně služby získáte od Microsoftu. [Soubor žádosti o funkci](https://feedback.azure.com/forums/169401-azure-active-directory) pro další funkce, které byste chtěli vidět v protokolu. [Vytvořit žádost o podporu](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request) Pokud narazíte na problém, kdy koncový bod Azure AD v2.0 není kompatibilní s OAuth 2.0 nebo OpenID Connect 1.0.

Knihovny jsou ve dvou kategoriích podpory:

* **Microsoft nepodporuje**. Microsoft poskytuje opravy pro tyto knihovny a dokončení procesu SDL vypršení platnosti co nejopatrněji na těchto knihovnách.
* **Kompatibilní**. Microsoft testovala tyto knihovny v základní scénáře a potvrdit, že fungují s koncovým bodem v2.0. Společnost Microsoft neposkytuje oprav pro tyto knihovny a přehled těchto knihoven, jako jednička. Problémy a žádosti o funkce směrovat do projektu open source knihovny.

Seznam knihoven, které pracují s koncovým bodem v2.0 najdete v další části tohoto článku.

## <a name="microsoft-supported-client-libraries"></a>Microsoft nepodporuje klientské knihovny

> [!IMPORTANT]
> Knihovny knihovna MSAL ve verzi preview jsou vhodné pro použití v produkčním prostředí. Společnost Microsoft poskytuje produkční úrovni podporu těchto knihoven jako aktuální knihovny produkčního prostředí (ADAL). Během období preview očekávat, že změny rozhraní API MSAL, formát vnitřní mezipaměti a další mechanismy tyto knihovny bez předchozího upozornění, které budete muset provést spolu s opravy chyb a vylepšení funkcí. To může mít vliv na vaše aplikace. Ke změně formátu mezipaměti může vyžadovat, uživatelům se znovu přihlásit. O změnu rozhraní API mohou vyžadovat aktualizaci kódu. Při obecné dostupnosti (GA) verzi k dispozici, musíte aktualizovat všechny aplikace ve verzi preview knihovny do šesti měsíců nebo se může přestat pracovat.

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Windows Store, UPW, Xamarin iOS a Android | Knihovna MSAL .NET (Preview) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplikace klasické pracovní plochy](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (Preview) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Aplikace s jedním stránky](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | Knihovna MSAL (ve verzi Preview) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplikace pro iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | Knihovna MSAL (ve verzi Preview) | [Centrální úložiště](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikace pro Android](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Server nepodporuje Microsoft middleware knihovny

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | Middleware OWIN OpenID Connect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/) |[Aplikace MVC](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | Middleware OWIN nosiče OAuth pro Azure AD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/) |  | |
| .NET 4.x | Obslužná rutina tokenů JWT pro rozhraní .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | Middleware ASP.NET OpenID Connect |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Zabezpečení technologie ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Aplikace MVC](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | Middleware nosiče OAuth technologie ASP.NET |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Zabezpečení technologie ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | Obslužná rutina tokenů JWT pro .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webové aplikace](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)| |

## <a name="compatible-client-libraries"></a>Kompatibilní klientské knihovny

| Platforma | Název knihovny | Otestované verze | Zdrojový kód | Ukázka |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Ukázky v nativní aplikaci](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Ukázky v nativní aplikaci](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[JEDNOSTRÁNKOVÁ APLIKACE](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Zapisovatel Java scribejava](https://github.com/scribejava/scribejava) | [Verze 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| PHP | [PHP ligy oauth2 klienta](https://github.com/thephpleague/oauth2-client) | [Verze 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [Klient oauth2](https://github.com/thephpleague/oauth2-client/) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Související obsah
Další informace o koncový bod Azure AD v2.0, najdete v článku [Přehled služby Azure AD app modelu v2.0][AAD-App-Model-V2-Overview].

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
