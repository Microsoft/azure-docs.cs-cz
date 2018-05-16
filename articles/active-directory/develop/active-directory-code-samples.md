---
title: Ukázky kódu Azure Active Directory | Microsoft Docs
description: Poskytuje index služby Azure Active Directory (koncový bod v1) ukázky kódu, uspořádané podle scénáře.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 299e0d4fa53f9b8a2aef2fc881b136aa41aacfe4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Ukázky kódu Azure Active Directory (koncový bod V1)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) můžete použít k přidání ověřování a autorizace do webových aplikací a webových rozhraní API.

Tato část obsahuje odkazy na ukázky, které můžete použít další informace o Azure AD V1 koncový bod. Tyto ukázky vám ukážou, jak se provádí společně s fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázkový kód, najdete podrobné čtení-mi témata, které pomáhají s požadavky, instalace a nastavení. A kód je označeno jako komentář vám pomohou pochopit kritické oddíly.

> [!NOTE]
> Pokud vás zajímá ukázky kódu Azure AD V2, přečtěte si téma [v2.0 ukázky kódu ve scénáři](active-directory-v2-code-samples.md).

Abyste pochopili základní scénáře pro každý typ ukázka, najdete v části [scénáře ověřování pro Azure AD](active-directory-authentication-scenarios.md).

Také můžete přispívat k naše ukázky na Githubu. Další informace, jak zjistit, [Microsoft Azure Active Directory ukázky a dokumentace](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Desktop a mobile veřejné klientské aplikace volání Microsoft Graph nebo webového rozhraní API

Následující ukázky zobrazit veřejné klientské aplikace (desktop nebo mobile aplikace), které přístup k Microsoft Graph nebo webového rozhraní API jménem uživatele.

klientské aplikace | Platforma | Tok/Grant | Volání Microsoft Graph | Volání rozhraní API technologie ASP.NET nebo webové jádro ASP.NET 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | .NET / C# | Interaktivní | [DotNet. nativní multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet. nativní plochy](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [DotNet. nativní aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet-webapi ruční – – ověřování jwt](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobile (UWP)            | .NET / C#  | Interaktivní | [DotNet nativní uwp wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [úložiště windows DotNet](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (jednoho klienta webového rozhraní API) </p> [DotNet webapi víceklientské windows úložiště](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (víceklientské webového rozhraní API)|
Mobile (Android, iOS, UWP)   | .NET / C# (Xamarin) | Interaktivní | [DotNet. nativní multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | Android/Java | Interaktivní |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | iOS/Objective C | Interaktivní |   [nativeClient iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Plocha (konzola)          | .NET / C# | Uživatelské jméno nebo heslo </p> Integrované ověřování systému Windows | | [nativní bezobslužných pro DotNet.](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Plocha (konzola)           | .NET core / C# | Profil zařízení | | [deviceprofile DotNet.](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Webové aplikace

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webové aplikace přihlášení uživatele, volání Microsoft Graph nebo webového rozhraní API s identitu uživatele

 Platforma | Pouze přihlášení uživatele | Volání Microsoft Graph nebo AAD Graph| Volá jiné technologie ASP.NET nebo ASP.NET Core 2.0 webového rozhraní API
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet.](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [WebApp-WSFederation-dotNet.](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [DotNet webapp víceklientské openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD graf) |
Jádro ASP.NET 2.0 | [DotNet webapp openidconnect aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [WebApp-webapi víceklientské openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD graf) | [DotNet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [DotNet-webapp-webapi-oauth2-identity uživatele](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [Python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [Java. webapp openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [PHP-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webové aplikace demonstraci řízení přístupu na základě role (autorizace)

Následující ukázky ukazují, jak implementovat řízení přístupu na základě rolí, které se používá k omezení oprávnění některých funkcí webové aplikace na určité uživatele. V závislosti na tom, jestli patří do skupiny Azure Active Directory nebo role mají oprávnění uživatele.

Platforma | Ukázka | Popis
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [DotNet. webapp groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Webové aplikace MVC rozhraní .NET 4.5, která používá Azure AD **skupiny** pro ověřování
ASP.NET 4.5 | [DotNet. webapp roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Webové aplikace MVC rozhraní .NET 4.5, která používá Azure AD **role** pro ověřování

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Démon procesu aplikace (přístup k webovým rozhraním API s identitu aplikace)

Následující ukázky zobrazit plochy nebo webové aplikace, které přístup k Microsoft Graph nebo webové rozhraní API bez uživatele (s identitou aplikace).

klientské aplikace | Platforma | Tok/Grant | Volání Microsoft Graph | Volání rozhraní API technologie ASP.NET nebo webové jádro ASP.NET 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Démon procesu aplikace (konzola)          | .NET / C#  | Pověření klienta s tajný klíč aplikace nebo certifikát | | [Démon DotNet.](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [DotNet démon certifikát pověření](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Démon procesu aplikace (konzola)         | .NET core / C# | Pověření klienta s certifikátem| | [dotnetcore démon certifikát pověření](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Plocha            | Java | Přihlašovací údaje klienta |   [bezobslužných Java – nativní](https://github.com/azure-samples/active-directory-java-native-headless) |
ASP.NET – webové aplikace  | .NET / C# | Přihlašovací údaje klienta |    | [DotNet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webová rozhraní API

### <a name="web-api-protected-by-azure-active-directory"></a>Webové rozhraní API, které jsou chráněné službou Azure Active Directory

Následující příklad ukazuje, jak chránit node.js webového rozhraní API s Azure AD.

Platforma | Ukázka | Popis
 -------- | ------------------- | ---------------------
Node.js | [uzel webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  NodeJS webové rozhraní API, která je zabezpečená pomocí služby Azure AD a přístupových tokenů OAuth 2.0.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webové rozhraní API volání Microsoft Graph nebo jiného webového rozhraní API

Následující ukázky ukazují webového rozhraní API, která volá jinou webové rozhraní API. Druhý příklad ukazuje způsob zpracování podmíněného přístupu.

 Platforma |  Volání Microsoft Graph | Volá jiné technologie ASP.NET nebo ASP.NET Core 2.0 webového rozhraní API
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [DotNet. webapi onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[DotNet. webapi onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Jednostránkové aplikace

Tento příklad ukazuje, jak psát jednostránkové aplikace zabezpečené s Azure AD.

 Platforma |  Volání Microsoft Graph | Volá vlastní rozhraní API
 -------- |  --------------------- | -------------------------
JavaScript (úhlová) nebo ASP.NET 4.x |  | [angularjs singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)

## <a name="other-microsoft-graph-samples"></a>Další ukázky Microsoft Graph

Ukázky a výukové programy, které ukazují různé vzorce pro Microsoft Graph API, včetně ověřování s Azure AD, najdete v části [Microsoft Graph komunity ukázky & kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Další informace najdete v tématech

[Příručka pro vývojáře Azure Active Directory](active-directory-developers-guide.md)

[Azure AD Graph API koncepční a referenční informace](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API pomocné knihovny](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
