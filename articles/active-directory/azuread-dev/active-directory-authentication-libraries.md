---
title: Knihovny ověřování služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Knihovna ověřování Azure AD (ADAL) umožňuje vývojářům klientských aplikací snadno ověřovat uživatele do cloudu nebo místní služby Active Directory (AD) a pak získat přístupové tokeny pro zabezpečení volání rozhraní API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 32375c14d95dc9e100cbf496c8550f0a195a11dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154606"
---
# <a name="azure-active-directory-authentication-libraries"></a>Knihovny ADAL (Azure Active Directory Authentication Library)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory Authentication Library (ADAL) v1.0 umožňuje vývojářům aplikací ověřovat uživatele do cloudu nebo místní služby Active Directory (AD) a získat tokeny pro zabezpečení volání rozhraní API. ADAL usnadňuje vývojářům ověřování prostřednictvím funkcí, jako jsou:

- Konfigurovatelná mezipaměť tokenů, ve které jsou uloženy přístupové tokeny a obnovovací tokeny
- Automatická aktualizace tokenu po vypršení platnosti přístupového tokenu a dostupnosti obnovovacího tokenu
- Podpora volání asynchronních metod

> [!NOTE]
> Hledáte knihovny Azure AD v2.0 (MSAL)? Pokladna [knihovna Průvodce MSAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Klientské knihovny podporované společností Microsoft

| Platforma | Knihovna | Stáhnout | Zdrojový kód | Ukázka | Odkaz
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Windows Store, UPW, Xamarin iOS a Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Desktopová aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Odkaz](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory?view=azure-dotnet) |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Jednostránková aplikace](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Aplikace pro iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Odkaz](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplikace pro Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Webová aplikace Node.js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Odkaz](https://docs.microsoft.com/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Webová aplikace Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Odkaz](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Webová aplikace Pythonu](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Odkaz](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Serverové knihovny podporované společností Microsoft

| Platforma | Knihovna | Stáhnout | Zdrojový kód | Ukázka | Odkaz
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN pro AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[Aplikace MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN pro OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Webová aplikace](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN pro WS-Federace |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[Webová aplikace MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Rozšíření protokolu identity pro rozhraní .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Obslužná rutina JWT pro rozhraní .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Scénáře

Zde jsou tři běžné scénáře pro použití ADAL v klientovi, který přistupuje ke vzdálenému prostředku:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Ověřování uživatelů nativní klientské aplikace spuštěné na zařízení

V tomto scénáři má vývojář mobilní klient nebo desktopovou aplikaci, která potřebuje přístup ke vzdálenému prostředku, jako je například webové rozhraní API. Webové rozhraní API neumožňuje anonymní volání a musí být volána v kontextu ověřeného uživatele. Webové rozhraní API je předem nakonfigurované tak, aby důvěřovala přístupovým tokenům vydaným konkrétním klientem Azure AD. Azure AD je předem nakonfigurované pro vydávání přístupových tokenů pro tento prostředek. Chcete-li vyvolat webové rozhraní API z klienta, vývojář používá ADAL k usnadnění ověřování pomocí Azure AD. Nejbezpečnější způsob, jak používat ADAL je mít vykreslení uživatelského rozhraní pro shromažďování uživatelských pověření (vykreslenjako okno prohlížeče).

ADAL usnadňuje ověření uživatele, získání přístupového tokenu a obnovovacího tokenu z Azure AD a následné volání webového rozhraní API pomocí přístupového tokenu.

Ukázka kódu, který ukazuje tento scénář pomocí ověřování ve službě Azure AD, naleznete v [tématu nativní klientwpf aplikace na webové rozhraní API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Ověření důvěrné klientské aplikace spuštěné na webovém serveru

V tomto scénáři má vývojář aplikaci spuštěnou na serveru, který potřebuje přístup ke vzdálenému prostředku, jako je například webové rozhraní API. Webové rozhraní API neumožňuje anonymní volání, takže musí být volána z autorizované služby. Webové rozhraní API je předem nakonfigurované tak, aby důvěřovala přístupovým tokenům vydaným konkrétním klientem Azure AD. Azure AD je předem nakonfigurované pro vydávání přístupových tokenů pro tento prostředek do služby s přihlašovacími údaji klienta (ID klienta a tajný klíč). ADAL usnadňuje ověřování služby s Azure AD vrací přístupový token, který lze použít k volání webovérozhraní API. ADAL také zpracovává správu životnosti přístupového tokenu jeho ukládáním do mezipaměti a jeho obnovenípodle potřeby. Ukázka kódu, který ukazuje tento scénář, naleznete [v tématu Daemon console Application to Web API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Ověření důvěrné klientské aplikace spuštěné na serveru jménem uživatele

V tomto scénáři má vývojář webovou aplikaci spuštěnou na serveru, který potřebuje přístup ke vzdálenému prostředku, jako je například webové rozhraní API. Webové rozhraní API neumožňuje anonymní volání, takže musí být voláno z autorizovaného servisu jménem ověřeného uživatele. Webové rozhraní API je předem nakonfigurované tak, aby důvěřovaly přístupovým tokenům vydaným konkrétním klientem Azure AD, a Azure AD je předem nakonfigurovaná tak, aby vydávala přístupové tokeny pro tento prostředek službě s pověřeními klienta. Jakmile je uživatel ověřen ve webové aplikaci, aplikace může získat autorizační kód pro uživatele z Azure AD. Webová aplikace pak můžete použít ADAL získat přístupový token a aktualizovat token jménem uživatele pomocí autorizačního kódu a pověření klienta přidružené k aplikaci z Azure AD. Jakmile je webová aplikace v držení přístupového tokenu, může volat webové rozhraní API, dokud platnost tokenu nevyprší. Když vyprší platnost tokenu, může webová aplikace použít ADAL k získání nového přístupového tokenu pomocí tokenu aktualizace, který byl dříve přijat. Ukázka kódu, která ukazuje tento scénář, naleznete v [tématu Nativní klient webového rozhraní API webového rozhraní API webového rozhraní API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Viz také

- [Průvodce vývojářem služby Azure Active Directory](v1-overview.md)
- [Scénáře ověřování pro adresář Azure Active Directory](v1-authentication-scenarios.md)
- [Ukázky kódu služby Azure Active Directory](sample-v1-code.md)
