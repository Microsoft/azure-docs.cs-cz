---
title: Knihovny ověřování služby Azure Active Directory | Dokumentace Microsoftu
description: Azure AD Authentication Library (ADAL) umožňuje vývojářům aplikací snadno ověřte uživatele, do cloudu klienta nebo v místním Active Directory (AD) a potom získat přístupové tokeny pro volání rozhraní API zabezpečení.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8c25e07d9844b077de80ae39b81a4e5823b0d344
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785052"
---
# <a name="azure-active-directory-authentication-libraries"></a>Knihovny ověřování služby Azure Active Directory

Azure Active Directory Authentication Library (ADAL) verze 1.0 vývojářům aplikací umožňuje ověřování uživatelů u cloudových nebo místní služby Active Directory (AD) a získat tokeny pro volání rozhraní API zabezpečení. ADAL usnadňuje ověřování pro vývojáře prostřednictvím funkcí, jako například:

- Konfigurovatelné mezipaměť tokenu, že úložiště přístupové tokeny a obnovovacích tokenů
- Automatické obnovení tokenu, když vyprší platnost přístupového tokenu a tokenu obnovení je k dispozici
- Podpora pro volání asynchronní metody

> [!NOTE]
> Hledáte služby Azure AD v2.0 knihovny (MSAL)? Rezervace [průvodce Knihovna MSAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Microsoft nepodporuje klientské knihovny

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Windows Store, UPW, Xamarin iOS a Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplikace klasické pracovní plochy](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referenční informace](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory?view=azure-dotnet) |
| .NET klienta, Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Aplikace klasické pracovní plochy](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Jednostránková aplikace](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[aplikace pro iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referenční informace](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Centrální úložiště](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplikace pro Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Webová aplikace Node.js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referenční informace](https://docs.microsoft.com/javascript/api/adal-node/?view=azure-node-latest) |
| Java |ADAL4J |[Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Webová aplikace Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referenční informace](http://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Webová aplikace v Pythonu](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referenční informace](http://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Microsoft nepodporuje Server knihovny

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN pro Azure AD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Aplikace MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN pro OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Webová aplikace](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN pro WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Webová aplikace MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Identita rozšíření protokolu pro rozhraní .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Obslužná rutina tokenů JWT pro rozhraní .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webové rozhraní API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Scénáře

Tady jsou tři běžné scénáře pro použití knihovny ADAL v klientovi, který přistupuje k vzdálenému prostředku:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Ověřování uživatelů nativní klientské aplikace běžící na zařízeních

V tomto scénáři má vývojář mobilního klienta nebo aplikace klasické pracovní plochy, která potřebuje přístup k vzdálenému prostředku, jako je například webové rozhraní API. Webové rozhraní API nepovoluje anonymní volání a musí být volána v rámci ověřeného uživatele. Webové rozhraní API je nakonfigurovaná tak, aby důvěřovat přístupové tokeny vydané určitou tenanta Azure AD. Azure AD je nakonfigurovaná tak, aby vystavovat přístupové tokeny pro daný prostředek. K vyvolání webové rozhraní API z klienta, vývojář používá ADAL pro usnadnění ověřování pomocí Azure AD. Nejbezpečnější způsob použití knihovny ADAL je jeho vykreslení uživatelského rozhraní pro shromažďování uživatelských přihlašovacích údajů (se vykresluje jako okno prohlížeče).

ADAL umožňuje snadno ověřit uživatele, získat přístupový a aktualizační token ze služby Azure AD a pak volat webové rozhraní API pomocí přístup k tokenu.

Ukázka kódu, který ukazuje tento scénář s využitím ověřování do služby Azure AD, najdete v části [nativní klientská aplikace WPF webového rozhraní API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Ověřování důvěrnému klientovi aplikace běžící na webovém serveru

V tomto scénáři má vývojář aplikace běžící na serveru, který potřebuje přístup k vzdálenému prostředku, jako je například webové rozhraní API. Volá webové rozhraní API nepovoluje anonymní, proto musí být volána z autorizovanou službou. Webové rozhraní API je nakonfigurovaná tak, aby důvěřovat přístupové tokeny vydané určitou tenanta Azure AD. Azure AD je nakonfigurovaná tak, aby vydání přístupové tokeny pro daný prostředek ke službě pomocí přihlašovacích údajů klienta (ID klienta a tajný klíč). ADAL usnadňuje ověřování služby Azure AD vrací přístupový token, který slouží k volání webového rozhraní API. Správa životního cyklu přístupový token pomocí ukládání do mezipaměti a obnovovat podle potřeby také zpracovává ADAL. Ukázka kódu, který ukazuje tento scénář, naleznete v tématu [démon konzoly aplikace webového rozhraní API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Ověřování důvěrné klientská aplikace běží na serveru, jménem uživatele

V tomto scénáři má vývojář webové aplikaci spuštěné na serveru, který potřebuje přístup k vzdálenému prostředku, jako je například webové rozhraní API. Webové rozhraní API nepovoluje anonymní volání, takže musí být volána z autorizovanou službou jménem ověřeného uživatele. Webové rozhraní API je předem nakonfigurovaný důvěřovat přístupové tokeny vydané službou konkrétní Azure AD je nakonfigurovaná tak, aby vystavovat přístupové tokeny pro daný prostředek s přihlašovacími údaji klientů ke službě tenanta a Azure AD. Po ověření uživatele ve webové aplikaci, můžete aplikaci získání autorizačního kódu pro uživatele ze služby Azure AD. Webové aplikace pak můžete použít knihovnu ADAL pro získání přístupového tokenu a obnovovací token jménem uživatele pomocí autorizační kód a klienta přihlašovacích údajů přidružených k aplikaci ze služby Azure AD. Jakmile webová aplikace je ve vlastnictví přístupový token, dokud nevyprší platnost tokenu může volat webové rozhraní API. Když vyprší platnost tokenu, webové aplikace můžete použít knihovnu ADAL se získat nový přístupový token s použitím aktualizace tokenu, který byl dříve dostali. Ukázka kódu, který ukazuje tento scénář, naleznete v tématu [nativního klienta k webovému rozhraní API pro webové rozhraní API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Viz také

- [Příručka pro vývojáře Azure Active Directory](azure-ad-developers-guide.md)
- [Scénáře ověřování pro Azure Active directory](authentication-scenarios.md)
- [Ukázky kódu pro Azure Active Directory](sample-v1-code.md)
