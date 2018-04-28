---
title: Knihovny ověřování služby Azure Active Directory | Microsoft Docs
description: Azure AD Authentication Library (ADAL) umožňuje klienta aplikace vývojářům snadno ověřování uživatelů do cloudu nebo místní služby Active Directory (AD) a potom získat přístupové tokeny zabezpečení volání rozhraní API.
services: active-directory
documentationcenter: ''
author: bryanla
manager: mtillman
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: e2c77b2e47ce3dd900b1b277e802a07cfed84702
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-authentication-libraries"></a>Knihovny ověřování služby Azure Active Directory
Azure Active Directory Authentication Library (ADAL) umožňuje vývojáři aplikace k ověřování uživatelů do cloudu nebo místní služby Active Directory (AD) a získat tokeny zabezpečení volání rozhraní API. ADAL usnadňuje ověřování pro vývojáře prostřednictvím funkcí, jako například:
 - Konfigurovat mezipamětí tokenů, aby úložiště přístup tokeny a obnovovacích tokenů
 - Automatická aktualizace tokenu, když vyprší platnost přístupového tokenu a obnovovací token je k dispozici
 - Podpora pro volání asynchronních metod
 - a další

> [!NOTE]
> Hledáte na Azure AD v2.0 knihovny (MSAL)? Najdete v článku věnovaném [MSAL knihovny průvodce](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

### <a name="client-libraries"></a>Klientské knihovny

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Windows Store, UWP Xamarin iOS a Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplikace na ploše](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referenční informace](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| Rozhraní .NET klienta Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Aplikace na ploše](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Jedna stránka aplikace](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[aplikace pro iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referenční informace](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[Centrální úložiště](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplikace pro Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Webová aplikace Java](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Webové aplikace Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) | |

### <a name="server-libraries"></a>Server knihovny

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN pro AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[Webu CodePlex](http://katanaproject.codeplex.com) |[Aplikace MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN pro OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[Webu CodePlex](http://katanaproject.codeplex.com) |[Webová aplikace](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webové rozhraní API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN pro WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[Webu CodePlex](http://katanaproject.codeplex.com) |[Webové aplikace MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Rozšíření protokolu identity pro .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Obslužná rutina JWT pro rozhraní .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>Knihovny klienta v2.0 (MSAL)

[Koncového bodu Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) kombinuje Azure AD a Accounts Microsoft za jeden koncový bod. Pro přístup k tomuto koncovému bodu, vývojáři mohou použít [podporované produkční preview MSAL knihovny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) místo ADAL.

| Platforma | Knihovna | Ke stažení | Zdrojový kód | Ukázka | Referenční informace
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Windows Store, UWP Xamarin iOS a Android |MSAL pro platformu .NET (preview) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplikace na ploše](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[Referenční informace](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) |
| JavaScript |MSAL pro jazyk JavaScript (preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Jedna stránka aplikace](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [Referenční informace](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) |
| iOS |MSAL pro iOS (preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [aplikace pro iOS](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [Referenční informace](https://azuread.github.io/docs/objc/) |
| Android |MSAL pro Android (preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikace pro Android](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [Referenční informace](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>Scénáře

Tady jsou tři běžné scénáře, ve kterých lze použít ADAL pro ověřování klienta, který používá vzdálený prostředek:  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Ověřování uživatelů nativní klientskou aplikaci spouštění v zařízení

V tomto scénáři má vývojář WPF klientskou aplikaci, která potřebuje přístup k vzdálený prostředek zabezpečené službou Azure AD, jako jsou webové rozhraní API. Mu má předplatné Azure, umí volat podřízené webové rozhraní API a zná klienta Azure AD, který používá webové rozhraní API. Výsledkem je kterou může použít ADAL k usnadnění ověřování s Azure AD, buď ve plně delegování zkušeností ověřování adal, nebo explicitně zpracovávat přihlašovací údaje uživatele. ADAL díky snadno ověření uživatele, získat přístupový a obnovovací token ze služby Azure AD a pak pomocí přístupového tokenu proveďte požaduje webovému rozhraní API.

Ukázka kódu, který ukazuje, tento scénář pomocí ověřování do služby Azure AD, najdete v části [nativní klientská aplikace WPF do webového rozhraní API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Ověřování důvěrné klientské aplikace běžící na webovém serveru

V tomto scénáři má vývojář aplikace běžící na serveru, která potřebuje přístup k vzdálený prostředek zabezpečené službou Azure AD, jako jsou webové rozhraní API. Mu má předplatné Azure, umí volat podřízené služby a ví, že klient Azure AD webového rozhraní API používá. Výsledkem je kterou může použít ADAL k usnadnění ověřování s Azure AD pomocí explicitně zpracování aplikace přihlašovací údaje. ADAL díky usnadňují načtení tokenu z Azure AD pomocí pověření klienta aplikace a pak používat k vytvoření tohoto tokenu k žádosti webové rozhraní API. ADAL také obstará, Správa životního cyklu přístupového tokenu mezipaměti a obnovení podle potřeby. Ukázka kódu, který ukazuje, tento scénář, najdete v části [démon konzole aplikace webového rozhraní API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Ověřování důvěrné klientské aplikace běží na serveru, jménem uživatele

V tomto scénáři má vývojář aplikace běžící na serveru, která potřebuje přístup k vzdálený prostředek zabezpečené službou Azure AD, jako jsou webové rozhraní API. Požadavek také musí být provedeny jménem uživatele Azure AD. Mu má předplatné Azure, umí volat podřízené webové rozhraní API a zná klienta používá služba Azure AD. Po ověření uživatele do webové aplikace, aplikace lze získat autorizační kód pro uživatele z Azure AD. Webové aplikace lze potom pomocí ADAL získat přístupový token a aktualizovat token jménem uživatele pomocí autorizační kód a klient pověření přidružené k aplikaci z Azure AD. Jakmile webová aplikace je k dispozici přístupový token, může zavolat webové rozhraní API do vypršení platnosti tokenu. Když vyprší platnost tokenu, webové aplikace můžete použít ADAL k získání nového tokenu přístupu pomocí aktualizace tokenu, který jste dříve získali. Ukázka kódu, který ukazuje, tento scénář, najdete v části [nativního klienta do webového rozhraní API do webového rozhraní API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Viz také

- [Příručka pro vývojáře Azure Active Directory](active-directory-developers-guide.md)
- [Scénáře ověřování pro Azure Active directory](active-directory-authentication-scenarios.md)
- [Ukázky kódu Azure Active Directory](active-directory-code-samples.md)
