---
title: Ukázky kódu pro Azure Active Directory | Dokumentace Microsoftu
description: Poskytuje index služby Azure Active Directory (koncového bodu v1) ukázky kódu, uspořádané podle scénáře.
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
ms.openlocfilehash: 221ff82e9c8d5164dfc7d7ee25623e6f73f59e13
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581206"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Ukázky kódu Azure Active Directory (koncového bodu V1)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) můžete použít k přidání ověřování a autorizace do webových aplikací a webových rozhraní API.

Tato část obsahuje odkazy na ukázky, která vám pomůže získat další informace o koncový bod Azure AD V1. Tyto ukázky předvádějí, jak se to dělá spolu s fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázky kódu najdete podrobné čtení-mě témata, která pomůžou s požadavky, instalací a nastavování. A kód je zakomentovaný, které vám pomůžou porozumět kritických oddílů.

> [!NOTE]
> Pokud vás zajímají ukázky kódu Azure AD V2, přečtěte si téma [ukázky kódu v2.0 podle scénáře](sample-v2-code.md).

Základní scénář pro každý typ vzorku najdete v tématu [scénáře ověřování pro službu Azure AD](authentication-scenarios.md).

Také můžete přispívat do našich ukázek na Githubu. Další informace o postupu [Microsoft Azure Active Directory ukázky a dokumentaci](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Desktop a mobile veřejné klientské aplikace volání Microsoft Graphu nebo rozhraní Web API

Následující ukázky ukazují veřejné klientské aplikace (desktop a mobilní aplikace), s přístupem k Microsoft Graphu nebo webové rozhraní API jménem uživatele.

Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu | Volání rozhraní API technologie ASP.NET nebo ASP.NET Core 2.0 Web
------------------ | -------- | ---------- | -------------------- | -------------------------
Plochy (WPF)           | .NET / C# | Interaktivní | [DotNet – nativní multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet – nativní desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [DotNet – nativní aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet-webapi ruční-token jwt – ověření](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobilní zařízení (UPW)            | .NET / C#  | Interaktivní | [DotNet nativní UPW wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [DotNet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (jeden tenant webového rozhraní API) </p> [DotNet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (s více tenanty webového rozhraní API)|
Mobilní zařízení (Android, iOS, UPW)   | .NET / C# (Xamarin) | Interaktivní | [DotNet – nativní multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobilní zařízení (Android)           | Android/Javu | Interaktivní |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobilní zařízení (iOS)           | iOS/Objective C | Interaktivní |   [nativeClient – iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Plocha (konzola)          | .NET / C# | Uživatelské jméno / heslo </p> Integrované ověřování Windows | | [DotNet – nativní bezobslužného](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Plocha (konzola)           | .NET core / C# | Profil zařízení | | [DotNet – deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Webové aplikace

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webové aplikace přihlašování uživatelů, volání Microsoft Graphu, nebo webové rozhraní API s využitím identity uživatele

 Platforma | Pouze přihlášení uživatele | Volání Microsoft Graphu nebo AAD Graph| Volá jiné technologie ASP.NET nebo ASP.NET Core 2.0 webového rozhraní API
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webové aplikace. openidconnect dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webové aplikace. WSFederation dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [DotNet webapp multitenant openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD Graph) |
ASP.NET Core 2.0 | [DotNet webapp openidconnect aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [WebApp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD Graph) | [DotNet – webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [DotNet – webapp-webapi-oauth2-identity uživatele](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [Python – webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [Java – webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [PHP – graphapi – web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webové aplikace demonstraci řízení přístupu na základě rolí (autorizace)

Následující ukázky ukazují, jak implementovat řízení přístupu na základě rolí, které se používá k omezení oprávnění určité funkce webové aplikace na určité uživatele. V závislosti na tom, jestli patří do Azure AD skupiny nebo role mají oprávnění uživatelů.

Platforma | Ukázka | Popis
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [DotNet – webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Webové aplikace .NET 4.5 MVC, který používá Azure AD **skupiny** pro autorizaci
ASP.NET 4.5 | [DotNet – webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Webové aplikace .NET 4.5 MVC, který používá Azure AD **role** pro autorizaci

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Démon procesu aplikace (přístup k webovým rozhraním API s identitou aplikace)

Následující ukázky ukazují desktopových nebo webových aplikací s přístupem k Microsoft Graphu nebo webové rozhraní API bez uživatele (s identitou aplikace).

Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu | Volání rozhraní API technologie ASP.NET nebo ASP.NET Core 2.0 Web
------------------ | -------- | ---------- | -------------------- | -------------------------
Démon procesu aplikace (konzola)          | .NET / C#  | Přihlašovací údaje pro klienta s tajný kód aplikace nebo certifikát | | [DotNet – démon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [DotNet démon certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Démon procesu aplikace (konzola)         | .NET core / C# | Přihlašovací údaje pro klienta pomocí certifikátu| | [dotnetcore démon certifikát pověření](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Plocha            | Java | Přihlašovací údaje klienta |   [bezobslužného Java – nativní](https://github.com/azure-samples/active-directory-java-native-headless) |
Webové aplikace v ASP.NET  | .NET / C# | Přihlašovací údaje klienta |    | [DotNet – webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webová rozhraní API

### <a name="web-api-protected-by-azure-active-directory"></a>Webové rozhraní API chráněné službou Azure Active Directory

Následující příklad ukazuje, jak chránit webové aplikace node.js API ve službě Azure AD.

Platforma | Ukázka | Popis
 -------- | ------------------- | ---------------------
Node.js | [uzel webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  Prostředí NodeJS webové rozhraní API, která je zabezpečena pomocí služby Azure AD a přístupových tokenů OAuth 2.0.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webové rozhraní API volání Microsoft Graphu nebo jiného webového rozhraní API

Následující ukázky ukazují webového rozhraní API, která volá jinou webové rozhraní API. Druhý příklad ukazuje, jak zpracovat podmíněného přístupu.

 Platforma |  Volání Microsoft Graphu | Volá jiné technologie ASP.NET nebo ASP.NET Core 2.0 webového rozhraní API
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [DotNet-webapi-prostřednictvím profilu onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[DotNet-webapi-prostřednictvím profilu onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Jednostránkové aplikace

Tato ukázka předvádí, jak psát jednostránkovou aplikaci zabezpečené službou Azure AD.

 Platforma |  Volání Microsoft Graphu | Volání vlastního rozhraní API | Volání jiné webové rozhraní API
 -------- |  --------------------- | ------------------ | ----------------
JavaScript a ASP.NET 4.x |  | [singlepageapp jazyka JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
JavaScript (AngularJS) / ASP.NET 4.x |  | [angularjs singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |
JavaScript (AngularJS) / ASP.NET 4.x |  |  | [angularjs. singlepageapp cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="other-microsoft-graph-samples"></a>Další ukázky Microsoft Graphu

Ukázky a návody, které ilustrují odlišnými vzory použití pro Microsoft Graph API, včetně ověřování pomocí Azure AD, najdete v části [ukázky komunity Microsoft Graph a kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Další informace najdete v tématech

[Příručka pro vývojáře Azure Active Directory](azure-ad-developers-guide.md)

[Azure AD Graph API konceptuální a referenční dokumentace](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API pomocné knihovny](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
