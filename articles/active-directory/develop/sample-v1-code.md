---
title: Ukázky kódu pro Azure Active Directory | Dokumentace Microsoftu
description: Index služby Azure Active Directory (koncový bod verze 1.0) obsahuje ukázky kódu, uspořádané podle scénáře.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a05b1885871c83da79839448fce2814016f0c082
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194559"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Ukázky kódu Azure Active Directory (koncový bod verze 1.0)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Microsoft Azure Active Directory (Azure AD) můžete použít k přidání ověřování a autorizace do webových aplikací a webových rozhraní API.

Tato část obsahuje odkazy na ukázky, která vám pomůže získat další informace o koncový bod Azure AD verze 1.0. Tyto ukázky předvádějí, jak se to dělá spolu s fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázky kódu najdete podrobné čtení-mě témata, která pomůžou s požadavky, instalací a nastavování. A kód je zakomentovaný, které vám pomůžou porozumět kritických oddílů.

> [!NOTE]
> Pokud vás zajímají ukázky kódu Azure AD V2, přečtěte si téma [ukázky kódu v2.0 podle scénáře](sample-v2-code.md).

Základní scénář pro každý typ vzorku najdete v tématu [scénáře ověřování pro službu Azure AD](authentication-scenarios.md).

Také můžete přispívat do našich ukázek na Githubu. Další informace o postupu [Microsoft Azure Active Directory ukázky a dokumentaci](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Jednostránkové aplikace

Tato ukázka předvádí, jak psát jednostránkovou aplikaci zabezpečené službou Azure AD.

 Platforma | Volání vlastního rozhraní API | Volání jiné webové rozhraní API
 -------- |  --------------------- | ------------------ | ----------------
![JavaScript](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Angular JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webové aplikace

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webové aplikace přihlašování uživatelů, volání Microsoft Graphu, nebo webové rozhraní API s využitím identity uživatele

Následující ukázky ukazují webové aplikace přihlašování uživatelů. Některé z těchto aplikací také volání Microsoft Graphu nebo vlastního webového rozhraní API, jménem přihlášeného uživatele.

 Platforma | Pouze přihlášení uživatele | Volání Microsoft Graphu nebo AAD Graph| Volá jiné technologie ASP.NET nebo ASP.NET Core 2.0 webového rozhraní API
 -------- | ------------------- | --------------------- | -------------------------
![ASP.NET](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) <p/>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) <p/> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) <p/> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)<p/> (AAD Graph) |
![Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![PHP](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webové aplikace demonstraci řízení přístupu na základě rolí (autorizace)

Následující ukázky ukazují, jak implementovat řízení přístupu na základě role (RBAC). RBAC se používá k omezení oprávnění určité funkce ve webové aplikaci na určité uživatele. Uživatelé mají oprávnění v závislosti na tom, jestli patří do **skupiny Azure AD** nebo mají určité aplikace **role**.

Platforma | Ukázka
 -------- | -------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) <p/>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Webové aplikace .NET 4.5 MVC, který používá Azure AD **role** pro autorizaci

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Desktop a mobile veřejné klientské aplikace volání Microsoft Graphu nebo rozhraní Web API

Následující ukázky ukazují aplikace veřejným klientem (desktop a mobilní aplikace), s přístupem k Microsoft Graphu nebo webové rozhraní API jménem uživatele. V závislosti na zařízení a platformy můžete různými způsoby (toky/uděluje) aplikace přihlásit uživatele: 

- interaktivně,
- tiše (s Windows ověřování integrované ve Windows nebo uživatelské jméno a heslo), 
- nebo dokonce delegováním interaktivní přihlášení na jiné zařízení (zařízení kód tok používaný na zařízení, které neposkytují webové ovládací prvky).

Klientská aplikace | Platforma | Tok/udělení | Volání Microsoft Graphu | Volá ASP.NET nebo ASP.NET Core 2.x webového rozhraní API
------------------ | -------- | ---------- | -------------------- | -------------------------
Plochy (WPF)           | ![.NET/C#](media/sample-v2-code/logo_NET.png)  | Interaktivní | Součást [dotnet. nativní multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobilní zařízení (UPW)            | .![.NET/C#/UWP](media/sample-v2-code/logo_Windows.png)   | Interaktivní | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Tato ukázka používá [WAM](https://docs.microsoft.com/windows/uwp/security/web-account-manager), nikoli [ADAL.NET](https://aka.ms/adalnet)|  [DotNet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (aplikaci UPW pomocí ADAL.NET volání jednoho tenanta webového rozhraní API) </p> [DotNet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (aplikaci UPW pomocí ADAL.NET volat webové rozhraní API pro více tenantů)|
Mobilní zařízení (Android, iOS, UPW)   | ![.NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interaktivní | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobilní zařízení (Android)           | ![Android / Javu](media/sample-v2-code/logo_Android.png) | Interaktivní |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobilní zařízení (iOS)           | ![iOS / Objective C nebo swiftu.](media/sample-v2-code/logo_iOS.png) | Interaktivní |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Plocha (konzola)          | ![.NET/C#](media/sample-v2-code/logo_NET.png) | Uživatelské jméno / heslo </p>  Integrované ověřování systému Windows | | [DotNet – nativní bezobslužného](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Plocha (konzola)          | ![Konzola jazyka Java](media/sample-v2-code/logo_Java.png) | Uživatelské jméno / heslo | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Plocha (konzola)           | ![.NET Core/C#](media/sample-v2-code/logo_NETcore.png) | Tok kódu při zařízení | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Démon procesu aplikace (přístup k webovým rozhraním API s identitou aplikace)

Následující ukázky ukazují desktopových nebo webových aplikací s přístupem k Microsoft Graphu nebo webové rozhraní API bez uživatele (s identitou aplikace).

Klientská aplikace | Platforma | Tok/udělení | Volání rozhraní API technologie ASP.NET nebo ASP.NET Core 2.0 Web
------------------ | -------- | ---------- | -------------------- | -------------------------
Démon procesu aplikace (konzola)          | ![.NET](media/sample-v2-code/logo_NETframework.png) | Přihlašovací údaje pro klienta s tajný kód aplikace nebo certifikát | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Démon procesu aplikace (konzola)         | ![.NET](media/sample-v2-code/logo_NETcore.png) | Přihlašovací údaje pro klienta pomocí certifikátu| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Webové aplikace v ASP.NET  | ![.NET](media/sample-v2-code/logo_NETframework.png) | Přihlašovací údaje klienta | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webová rozhraní API

### <a name="web-api-protected-by-azure-active-directory"></a>Webové rozhraní API chráněné službou Azure Active Directory

Následující příklad ukazuje, jak chránit webové aplikace node.js API ve službě Azure AD.

V předchozích částech tohoto článku najdete také další ukázky ilustrující klientská aplikace **volání** ASP.NET nebo ASP.NET Core **webového rozhraní API**. Tyto ukázky nejsou znovu uvedené v této části, ale najdete je v posledním sloupečku tabulky nad nebo pod

Platforma | Ukázka
 -------- | -------------------
![PHP](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi)

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webové rozhraní API volání Microsoft Graphu nebo jiného webového rozhraní API

Následující ukázky ukazují webového rozhraní API, která volá jinou webové rozhraní API. Druhý příklad ukazuje, jak zpracovat podmíněného přístupu.

 Platforma |  Volání Microsoft Graphu | Volá jiné technologie ASP.NET nebo ASP.NET Core 2.0 webového rozhraní API
 -------- |  --------------------- | -------------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="other-microsoft-graph-samples"></a>Další ukázky Microsoft Graphu

Ukázky a návody, které ilustrují odlišnými vzory použití pro Microsoft Graph API, včetně ověřování pomocí Azure AD, najdete v části [ukázky komunity Microsoft Graph a kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Další informace najdete v tématech

[Příručka pro vývojáře Azure Active Directory](v1-overview.md)

[Knihovny Azure Active Directory Authentication](active-directory-authentication-libraries.md)

[Azure AD Graph API konceptuální a referenční dokumentace](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API pomocné knihovny](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
