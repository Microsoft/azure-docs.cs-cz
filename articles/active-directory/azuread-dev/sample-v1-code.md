---
title: Ukázky kódu pro Azure Active Directory v 1.0 | Microsoft Docs
description: Poskytuje index ukázek kódu Azure Active Directory (v 1.0 Endpoint), uspořádané podle scénáře.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: sample
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 484a44a7a9840409e3f5770c743e5cd62e0cef66
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910300"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Ukázka kódu Azure Active Directory (koncový bod verze 1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Pomocí služby Microsoft Azure Active Directory (Azure AD) můžete přidat ověřování a autorizaci k webovým aplikacím a webovým rozhraním API.

Tato část obsahuje odkazy na ukázky, které můžete použít pro další informace o koncovém bodu Azure AD v 1.0. Tyto ukázky ukazují, jak se provádí společně s fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázka kódu najdete podrobná témata pro čtení, která vám pomůžou s požadavky, instalací a nastavením. A kód je komentář, který vám pomůže pochopit důležité oddíly.

> [!NOTE]
> Pokud vás zajímá ukázky kódu pro Azure AD v2, přečtěte si téma [ukázky kódu v 2.0 podle scénáře](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Základní scénář pro jednotlivé typy ukázkových typů najdete v tématu [scénáře ověřování pro Azure AD](v1-authentication-scenarios.md).

Můžete také přispět k našim ukázkám na GitHubu. Další informace najdete v tématu [Microsoft Azure Active Directory Samples and Documentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Jednostránkové aplikace

V této ukázce se dozvíte, jak napsat jednostránkovou aplikaci zabezpečenou pomocí Azure AD.

 Platforma | Volá své vlastní rozhraní API. | Volá jiné webové rozhraní API.
 -------- |  --------------------- | ------------------ 
![Tento obrázek ukazuje logo JavaScriptu.](media/sample-v2-code/logo-js.png) | [JavaScript – singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Tento obrázek ukazuje logo úhlového JS.](media/sample-v2-code/logo-angular.png) | [AngularJS – singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [AngularJS – singlepageapp – CORS](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webové aplikace

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webové aplikace přihlašování uživatelů, volání Microsoft Graph nebo webové rozhraní API s identitou uživatele

Následující ukázky ilustrují webové aplikace, které podepisují uživatele. Některé z těchto aplikací také volají Microsoft Graph nebo vlastní webové rozhraní API, a to jménem přihlášeného uživatele.

 Platforma | Pouze přihlášení uživatelé | Volání Microsoft Graph | Volá jiné webové rozhraní API ASP.NET nebo ASP.NET Core 2,0.
 -------- | ------------------- | --------------------- | -------------------------
![Tento obrázek ukazuje logo ASP.NET Core.](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2,0 | [dotnet-WebApp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [WebApp-WebApi-víceklientské tenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(Graf AAD) | [dotnet-WebApp-WebApi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Tento obrázek ukazuje logo rozhraní ASP.NET Framework.](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 |  </p> [WebApp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-WebApp-WebApi-OAuth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-WebApp-víceklientské openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (Graf AAD) |
![Tento obrázek ukazuje logo Pythonu.](media/sample-v2-code/logo-python.png) | | [Python – WebApp – graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Tento obrázek ukazuje protokol Java.](media/sample-v2-code/logo-java.png)  | | [Java-WebApp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Tento obrázek ukazuje logo PHP.](media/sample-v2-code/logo-php.png) | | [php – graphapi – Web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webové aplikace, které demonstrují řízení přístupu na základě rolí (autorizace)

Následující ukázky ukazují, jak implementovat řízení přístupu na základě role (RBAC). RBAC slouží k omezení oprávnění určitých funkcí ve webové aplikaci na určité uživatele. Uživatelé mají oprávnění v závislosti na tom, jestli patří do **skupiny Azure AD** nebo mají danou **roli** aplikace.

Platforma | Ukázka |
 -------- | ------------------- |
![Tento obrázek ukazuje logo rozhraní ASP.NET Framework.](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet – WebApp – groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet – WebApp – roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Webová aplikace .NET 4,5 MVC, která používá **role** Azure AD k autorizaci

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Desktopové a mobilní veřejné klientské aplikace, které volají Microsoft Graph nebo webové rozhraní API

Následující ukázky ilustrují veřejné klientské aplikace (deskto/Pmobile aplikace), které přistupují k Microsoft Graph nebo webovému rozhraní API v názvu uživatele. V závislosti na zařízeních a platformách se můžou aplikace přihlašovat uživatelům různými způsoby (toky nebo granty):

- Interaktivně
- V tichém režimu (s integrovaným ověřováním systému Windows ve Windows nebo uživatelským jménem/heslem)
- Delegováním interaktivního přihlašování k jinému zařízení (tok kódu zařízení, který se používá na zařízeních, která neposkytují webové ovládací prvky)

Klientská aplikace | Platforma | Tok a udělení | Volání Microsoft Graph | Volá webové rozhraní API ASP.NET nebo ASP.NET Core 2. x.
------------------ | -------- | ---------- | -------------------- | -------------------------
Plocha (WPF)           | ![Tento obrázek ukazuje logo .NET/C#](media/sample-v2-code/logo-net.png)  | Interaktivní | Součást [dotnet – nativní – s více cíli](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet – nativní – plocha](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet – nativní – aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-WebApi-ruční-JWT – ověření](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobilní zařízení (UWP)            | .![Tento obrázek ukazuje .NET/C#/UWP](media/sample-v2-code/logo-windows.png)   | Interaktivní | [dotnet – nativní – UWP – WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Tato ukázka používá [WAM](/windows/uwp/security/web-account-manager), nikoli [ADAL.NET](https://aka.ms/adalnet) .|  [dotnet – Windows Store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (aplikace UWP používající ADAL.NET k volání webového rozhraní API pro jednoho tenanta) </p> [dotnet-WebApi-multi-tenant-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (aplikace UWP používající ADAL.NET pro volání webového rozhraní API pro více tenantů)|
Mobilní zařízení (Android, iOS, UWP)   | ![Tento obrázek ukazuje rozhraní .NET/C# (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interaktivní | [dotnet – nativní – více cílů](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobilní zařízení (Android)           | ![Tento obrázek ukazuje logo Androidu.](media/sample-v2-code/logo-android.png) | Interaktivní |   [svém](https://github.com/Azure-Samples/active-directory-android) |
Mobilní zařízení (iOS)           | ![Tento obrázek znázorňuje iOS/objektivní C nebo SWIFT.](media/sample-v2-code/logo-ios.png) | Interaktivní |   [nativeClient – iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Plocha (konzola)          | ![Tento obrázek ukazuje logo .NET/C#](media/sample-v2-code/logo-net.png) | Uživatelské jméno a heslo </p>  Integrované ověřování systému Windows | | [dotnet – nativní – bezobslužné](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Plocha (konzola)          | ![Tento obrázek ukazuje logo Java.](media/sample-v2-code/logo-java.png) | Uživatelské jméno a heslo | | [Java – nativní – bezobslužné](https://github.com/Azure-Samples/active-directory-java-native-headless)
Plocha (konzola)           | ![Tento obrázek ukazuje logo .NET Core/C#](media/sample-v2-code/logo-netcore.png) | Tok kódu zařízení | | [dotnet – deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Aplikace démona (přístup k webovým rozhraním API s identitou aplikace)

Následující ukázky ukazují desktopové nebo webové aplikace, které přistupují k Microsoft Graph nebo webovému rozhraní API bez uživatele (s identitou aplikace).

Klientská aplikace | Platforma | Tok a udělení | Volá webové rozhraní API ASP.NET nebo ASP.NET Core 2,0.
------------------ | -------- | ---------- | -------------------- 
Aplikace démona (konzola)          | ![Tento obrázek ukazuje logo .NET Framework.](media/sample-v2-code/logo-netframework.png) | Pověření klienta s tajným klíčem nebo certifikátem aplikace | [dotnet – démon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-démon-certifikát-pověření](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Aplikace démona (konzola)         | ![Tento obrázek ukazuje logo .NET Core](media/sample-v2-code/logo-netcore.png) | Pověření klienta s certifikátem| [dotnetcore-démon-certifikát-pověření](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Webová aplikace ASP.NET  | ![Tento obrázek ukazuje logo .NET Framework.](media/sample-v2-code/logo-netframework.png) | Přihlašovací údaje klienta | [dotnet-WebApp-WebApi-OAuth2-APPIDENTITY](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webová rozhraní API

### <a name="web-api-protected-by-azure-active-directory"></a>Webové rozhraní API chráněné Azure Active Directory

Následující příklad ukazuje, jak chránit node.js webového rozhraní API pomocí služby Azure AD.

V předchozích částech tohoto článku můžete také najít další ukázky, které ilustrují klientskou aplikaci, která **volá** rozhraní ASP.NET nebo ASP.NET Core **Web API** . Tyto ukázky se v této části neuvádějí znovu, ale najdete je v posledním sloupci tabulek výše nebo níže.

| Platforma | Ukázka |
|--------|-------------------|
| ![Tento obrázek ukazuje logo Node.js.](media/sample-v2-code/logo-nodejs.png)  | [uzel – WebApi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Volání webového rozhraní API Microsoft Graph nebo jiné webové rozhraní API

Následující ukázky ukazují webové rozhraní API, které volá jiné webové rozhraní API. Druhý příklad ukazuje, jak zpracovat podmíněný přístup.

| Platforma |  Volání Microsoft Graph | Volá jiné webové rozhraní API ASP.NET nebo ASP.NET Core 2,0. |
| -------- |  --------------------- | ------------------------- |
| ![Tento obrázek ukazuje logo rozhraní ASP.NET Framework.](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet – WebApi – OnBehalfOf](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet – WebApi – OnBehalfOf – CA](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet – WebApi – OnBehalfOf](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet – WebApi – OnBehalfOf – CA](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Další ukázky Microsoft Graph

Ukázky a kurzy, které ukazují různé způsoby použití rozhraní Microsoft Graph API, včetně ověřování pomocí služby Azure AD, najdete v tématu [Microsoft Graph komunitních ukázek & kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Viz také

- [Azure Active Directory příručka pro vývojáře](v1-overview.md)
- [Azure Active Directory knihovny ověřování](active-directory-authentication-libraries.md)
- [Koncepční a referenční informace k rozhraní Microsoft Graph API](/graph/use-the-api)