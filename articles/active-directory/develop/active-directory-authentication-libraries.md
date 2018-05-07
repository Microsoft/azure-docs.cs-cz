---
title: Knihovny ověřování služby Azure Active Directory | Microsoft Docs
description: Azure AD Authentication Library (ADAL) umožňuje klienta aplikace vývojářům snadno ověřování uživatelů do cloudu nebo místní služby Active Directory (AD) a potom získat přístupové tokeny zabezpečení volání rozhraní API.
services: active-directory
documentationcenter: ''
author: SaeedAkhter-MSFT
manager: mtillman
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1e82fa62c86c80b1e68fea995ca6f90861688d5c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="azure-active-directory-authentication-libraries"></a>Knihovny ověřování služby Azure Active Directory

Azure Active Directory Authentication Library (ADAL) verze 1.0 umožňuje vývojáři aplikace k ověřování uživatelů do cloudu nebo místní služby Active Directory (AD) a získat tokeny zabezpečení volání rozhraní API. ADAL usnadňuje ověřování pro vývojáře prostřednictvím funkcí, jako například:

- Konfigurovat mezipamětí tokenů, aby úložiště přístup tokeny a obnovovacích tokenů
- Automatická aktualizace tokenu, když vyprší platnost přístupového tokenu a obnovovací token je k dispozici
- Podpora pro volání asynchronních metod

> [!NOTE]
> Hledáte na Azure AD v2.0 knihovny (MSAL)? Najdete v článku věnovaném [MSAL knihovny průvodce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Knihovny Microsoft podporované klienta

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Windows Store, UWP Xamarin iOS a Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplikace na ploše](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referenční informace](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| Rozhraní .NET klienta Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Aplikace na ploše](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Jedna stránka aplikace](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[aplikace pro iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referenční informace](https://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[Centrální úložiště](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplikace pro Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Webová aplikace Node.js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)| |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Webová aplikace Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Webové aplikace Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) | |

## <a name="microsoft-supported-server-libraries"></a>Podporované Microsoft Server knihovny

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN pro AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[Webu CodePlex](http://katanaproject.codeplex.com) |[Aplikace MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN pro OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[Webu CodePlex](http://katanaproject.codeplex.com) |[Webová aplikace](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN pro WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[Webu CodePlex](http://katanaproject.codeplex.com) |[Webové aplikace MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Rozšíření protokolu identity pro .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Obslužná rutina JWT pro rozhraní .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webové rozhraní API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Scénáře

Tady jsou tři běžné scénáře použití ADAL v klientovi, který používá vzdálený prostředek:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Ověřování uživatelů nativní klientskou aplikaci spouštění v zařízení

V tomto scénáři má vývojář klient mobilní nebo desktopová aplikace, která potřebuje přístup k vzdálený prostředek, jako jsou webové rozhraní API. Webové rozhraní API nepovoluje anonymní volání a musí být voláno v kontextu ověřeného uživatele. Webové rozhraní API je předem nakonfigurovaná tak, aby důvěřoval vystavený konkrétní tokeny přístupu ke klientovi Azure AD. Azure AD je předem nakonfigurovaný k vydání tokenů přístupu pro tento prostředek. K vyvolání webové rozhraní API z klienta, vývojář využívá ADAL k usnadnění ověřování s Azure AD. Nejbezpečnější způsob použití knihovny ADAL je na něm při vykreslení uživatelského rozhraní pro shromažďování přihlašovací údaje uživatele (se vykresluje jako okno prohlížeče).

ADAL usnadňuje ověření uživatele, získat přístupový a obnovovací token ze služby Azure AD a pak zavolají webové rozhraní API pomocí přístupu tokenu.

Ukázka kódu, který ukazuje, tento scénář pomocí ověřování do služby Azure AD, najdete v části [nativní klientská aplikace WPF do webového rozhraní API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Ověřování důvěrné klientské aplikace běžící na webovém serveru

V tomto scénáři má vývojář aplikace běžící na serveru, která potřebuje přístup k vzdálený prostředek, jako jsou webové rozhraní API. Webová rozhraní API nepovoluje anonymní volá, musí být volána z autorizovanou službou. Webové rozhraní API je předem nakonfigurovaná tak, aby důvěřoval vystavený konkrétní tokeny přístupu ke klientovi Azure AD. Azure AD je předem nakonfigurovaný k vydávání přístupových tokenů pro tento prostředek služby s pověření klienta (ID klienta a tajný klíč). ADAL usnadňuje ověřování služby s Azure AD vrácení přístupový token, který slouží k volání webového rozhraní API. ADAL také obstará, Správa životního cyklu přístupového tokenu mezipaměti a obnovení podle potřeby. Ukázka kódu, který ukazuje, tento scénář, najdete v části [démon konzole aplikace webového rozhraní API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Ověřování důvěrné klientské aplikace běží na serveru, jménem uživatele

V tomto scénáři má vývojář webová aplikace běží na serveru, která potřebuje přístup k vzdálený prostředek, jako jsou webové rozhraní API. Webové rozhraní API nepovoluje anonymní volání, takže musí být volána z autorizovanou službou zastoupení ověřeného uživatele. Webové rozhraní API je předem nakonfigurovaná tak, aby důvěřoval přístupové tokeny vydané službou konkrétní Azure AD je předem nakonfigurovaný k vydávat přístupových tokenů pro tento prostředek služby pomocí pověření klienta klienta a Azure AD. Jakmile je uživatel ověřený ve webové aplikaci, aplikace můžete získat autorizační kód pro uživatele z Azure AD. Webové aplikace lze potom pomocí ADAL získat přístupový token a aktualizovat token jménem uživatele pomocí autorizační kód a klient pověření přidružené k aplikaci z Azure AD. Jakmile webová aplikace je k dispozici přístupový token, může zavolat webové rozhraní API do vypršení platnosti tokenu. Když vyprší platnost tokenu, webové aplikace můžete použít ADAL k získání nového tokenu přístupu pomocí aktualizace tokenu, který jste dříve získali. Ukázka kódu, který ukazuje, tento scénář, najdete v části [nativního klienta do webového rozhraní API do webového rozhraní API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Viz také

- [Příručka pro vývojáře Azure Active Directory](active-directory-developers-guide.md)
- [Scénáře ověřování pro Azure Active directory](active-directory-authentication-scenarios.md)
- [Ukázky kódu Azure Active Directory](active-directory-code-samples.md)
