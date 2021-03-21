---
title: Azure Active Directory knihovny ověřování | Microsoft Docs
description: Knihovna ADAL (Azure AD Authentication Library) umožňuje vývojářům klientských aplikací snadno ověřovat uživatele v cloudu nebo místní službě Active Directory (AD) a pak získat přístupové tokeny pro zabezpečení volání rozhraní API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: reference
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7b6ffd885e1be2dd59cea87cacd6e5fd5e0f8f49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861166"
---
# <a name="azure-active-directory-authentication-libraries"></a>Knihovny ADAL (Azure Active Directory Authentication Library)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Knihovna Azure Active Directory Authentication Library (ADAL) v 1.0 umožňuje vývojářům aplikací ověřovat uživatele v cloudu nebo místní službě Active Directory (AD) a získat tokeny pro zabezpečení volání rozhraní API. ADAL usnadňuje ověřování pro vývojáře prostřednictvím funkcí, jako je:

- Konfigurovatelná mezipaměť tokenů, která ukládá přístupové tokeny a aktualizuje tokeny
- Automatická aktualizace tokenu po vypršení platnosti přístupového tokenu a dostupnosti obnovovacího tokenu
- Podpora pro volání asynchronní metody

> [!NOTE]
> Hledáte knihovny pro Azure AD v 2.0 (MSAL)? Zarezervujte [Průvodce knihovnou MSAL](../develop/reference-v2-libraries.md).
>
>

## <a name="microsoft-supported-client-libraries"></a>Klientské knihovny podporované Microsoftem

| Platforma | Knihovna | Stáhnout | Zdrojový kód | Ukázka | Reference
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Windows Store, UWP, Xamarin iOS a Android |ADAL .NET V3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Desktopová aplikace](../develop/quickstart-v2-windows-desktop.md) |[Odkaz](/dotnet/api/microsoft.identitymodel.clients.activedirectory) |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Jednostránková aplikace](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[aplikace pro iOS](../develop/quickstart-v2-ios.md) | [Odkaz](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplikace pro Android](../develop/quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Webová aplikace Node.js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Odkaz](/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Webová aplikace Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Odkaz](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Webová aplikace v Pythonu](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Odkaz](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Knihovny serverů podporované Microsoftem

| Platforma | Knihovna | Stáhnout | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN pro AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[Aplikace MVC](../develop/quickstart-v2-aspnet-webapp.md) | |
| .NET |OWIN pro OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Webová aplikace](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN pro WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[Webová aplikace MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Rozšíření identity protokolu pro .NET 4,5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Obslužná rutina JWT pro .NET 4,5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webové rozhraní API](../develop/authentication-flows-app-scenarios.md)| |

## <a name="scenarios"></a>Scénáře

Tady jsou tři běžné scénáře použití ADAL v klientovi, který přistupuje ke vzdálenému prostředku:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Ověřování uživatelů nativní klientské aplikace spuštěné na zařízení

V tomto scénáři má vývojář mobilní klient nebo desktopovou aplikaci, která potřebuje přístup ke vzdálenému prostředku, jako je například webové rozhraní API. Webové rozhraní API nepovoluje anonymní volání a musí být voláno v kontextu ověřeného uživatele. Webové rozhraní API je předem nakonfigurované tak, aby důvěřovalo přístupové tokeny, které vydal konkrétní tenant služby Azure AD. Služba Azure AD je předem nakonfigurovaná tak, aby vydávala přístupové tokeny pro tento prostředek. Aby bylo možné webové rozhraní API vyvolat z klienta, vývojář použije ADAL k usnadnění ověřování pomocí Azure AD. Nejbezpečnější způsob, jak používat ADAL, je vykreslovat uživatelské rozhraní pro shromažďování přihlašovacích údajů uživatele (vykreslené jako okno prohlížeče).

ADAL usnadňuje ověření uživatele, získání přístupového tokenu a aktualizace tokenu z Azure AD a volání webového rozhraní API pomocí přístupového tokenu.

Ukázku kódu, který demonstruje tento scénář pomocí ověřování pro službu Azure AD, najdete v tématu [nativní klientská aplikace WPF pro webové rozhraní API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Ověřování důvěrné klientské aplikace spuštěné na webovém serveru

V tomto scénáři má vývojář aplikaci spuštěnou na serveru, který potřebuje přístup ke vzdálenému prostředku, jako je například webové rozhraní API. Webové rozhraní API nepovoluje anonymní volání, takže se musí volat z autorizované služby. Webové rozhraní API je předem nakonfigurované tak, aby důvěřovalo přístupové tokeny, které vydal konkrétní tenant služby Azure AD. Služba Azure AD je předem nakonfigurovaná tak, aby vydávala přístupové tokeny pro daný prostředek ke službě s přihlašovacími údaji klienta (ID klienta a tajný kód). ADAL usnadňuje ověřování služby pomocí Azure AD vrácení přístupového tokenu, který se dá použít k volání webového rozhraní API. ADAL také zpracovává správu životnosti přístupového tokenu tím, že ho ukládá do mezipaměti a v případě potřeby ho obnoví. Ukázku kódu, který demonstruje tento scénář, najdete v tématu [aplikace démona aplikace na webové rozhraní API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Ověřování důvěrné klientské aplikace spuštěné na serveru jménem uživatele

V tomto scénáři má vývojář webovou aplikaci spuštěnou na serveru, který potřebuje přístup ke vzdálenému prostředku, jako je například webové rozhraní API. Webové rozhraní API nepovoluje anonymní volání, takže se musí volat z autorizované služby jménem ověřeného uživatele. Webové rozhraní API je předem nakonfigurované tak, aby důvěřovalo přístupovým tokenům, které vydal konkrétní tenant Azure AD, a služba Azure AD je předem nakonfigurovaná tak, aby vydávala přístupové tokeny pro daný prostředek ke službě s přihlašovacími údaji klienta. Po ověření uživatele ve webové aplikaci může aplikace získat autorizační kód pro uživatele ze služby Azure AD. Webová aplikace potom může pomocí ADAL získat přístupový token a aktualizovat token jménem uživatele pomocí autorizačního kódu a přihlašovacích údajů klienta přidružených k aplikaci z Azure AD. Jakmile je webová aplikace držitelem přístupového tokenu, může volat webové rozhraní API, dokud nevyprší platnost tokenu. Po vypršení platnosti tokenu může webová aplikace používat ADAL k získání nového přístupového tokenu pomocí obnovovacího tokenu, který byl dříve přijat. Ukázku kódu, který demonstruje tento scénář, najdete v tématu [nativní klient pro webové rozhraní API k webovému rozhraní API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Viz také

- [Příručka pro vývojáře Azure Active Directory](v1-overview.md)
- [Scénáře ověřování pro Azure Active Directory](v1-authentication-scenarios.md)
- [Ukázky kódu Azure Active Directory](sample-v1-code.md)
