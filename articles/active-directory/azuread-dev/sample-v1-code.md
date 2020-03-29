---
title: Ukázky kódu pro službu Azure Active Directory v1.0 | Dokumenty společnosti Microsoft
description: Poskytuje index vzorků kódu služby Azure Active Directory (v1.0 endpoint), uspořádaných podle scénáře.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ae283529abb3b71ee50fc710dd1ebe0d17a12be0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154827"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Ukázky kódu služby Azure Active Directory (koncový bod v1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Pomocí služby Microsoft Azure Active Directory (Azure AD) můžete přidat ověřování a autorizaci do webových aplikací a webových rozhraní API.

Tato část obsahuje odkazy na ukázky, které můžete použít k další informace o koncovém bodu Azure AD v1.0. Tyto ukázky ukazují, jak se to dělá spolu s fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázkového kódu najdete podrobná témata pro čtení, která vám pomohou s požadavky, instalací a nastavením. A kód je komentován, aby vám pomohl pochopit kritické části.

> [!NOTE]
> Pokud vás zajímají ukázky kódu Azure AD V2, přečtěte si informace [o ukázkách kódu v2.0 podle scénáře](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Chcete-li pochopit základní scénář pro každý typ ukázky, najdete [v tématu scénáře ověřování pro Azure AD](v1-authentication-scenarios.md).

Můžete také přispět k našim ukázekm na GitHubu. Informace o tom najdete v [tématu Ukázky a dokumentace služby Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Jednostránkové aplikace

Tato ukázka ukazuje, jak napsat jednostránkovou aplikaci zabezpečenou službou Azure AD.

 Platforma | Volá vlastní rozhraní API | Volání jiného webového rozhraní API
 -------- |  --------------------- | ------------------ 
![Tento obrázek znázorňuje logo JavaScriptu](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Tento obrázek znázorňuje angular js logo](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webové aplikace

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webové aplikace přihlašování uživatelů, volání Microsoft Graphu nebo webové rozhraní API s identitou uživatele

Následující ukázky ilustrují podepisování uživatelů webových aplikací. Některé z těchto aplikací také volat Microsoft Graph nebo vlastní webové rozhraní API, jménem přihlášeného uživatele.

 Platforma | Pouze znaky u uživatelů | Volání Microsoft Graph | Volá jiné ASP.NET nebo ASP.NET core 2.0 web API
 -------- | ------------------- | --------------------- | -------------------------
![Tento obrázek znázorňuje logo ASP.NET](media/sample-v2-code/logo-netcore.png)</p>ASP.NET jádro 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD Graf) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Tento obrázek znázorňuje logo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 |  </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD Graf) |
![Tento obrázek znázorňuje logo Pythonu](media/sample-v2-code/logo-python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Tento obrázek znázorňuje java log](media/sample-v2-code/logo-java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Tento obrázek znázorňuje logo PHP](media/sample-v2-code/logo-php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webové aplikace demonstrující řízení přístupu na základě rolí (autorizace)

Následující ukázky ukazují, jak implementovat řízení přístupu na základě rolí (RBAC). RBAC se používá k omezení oprávnění některých funkcí ve webové aplikaci pro určité uživatele. Uživatelé jsou autorizováni v závislosti na tom, zda patří do **skupiny Azure AD** nebo mají roli dané **aplikace**.

Platforma | Ukázka |
 -------- | ------------------- |
![Tento obrázek znázorňuje logo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Webová aplikace .NET 4.5 MVC, která používá **role** Azure AD pro autorizaci

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Desktopové a mobilní veřejné klientské aplikace volající microsoft graph nebo webové rozhraní API

Následující ukázky ilustrují veřejné klientské aplikace (deskto/pmobile aplikace), které přistupují k microsoft graphu nebo webovému rozhraní API jménem uživatele. V závislosti na zařízeních a platformách mohou aplikace přihlašovat uživatele různými způsoby (toky/granty):

- Interaktivně
- Tiše (s integrovaným ověřováním systému Windows v systému Windows nebo uživatelským jménem/heslem)
- Delegováním interaktivního přihlášení do jiného zařízení (tok kódu zařízení používaný na zařízeních, která neposkytují webové ovládací prvky)

Klientská aplikace | Platforma | Tok/Grant | Volání Microsoft Graph | Volání webového rozhraní API ASP.NET nebo ASP.NET Core 2.x
------------------ | -------- | ---------- | -------------------- | -------------------------
Plocha (WPF)           | ![Tento obrázek znázorňuje logo .NET/C#](media/sample-v2-code/logo-net.png)  | Interaktivní | Část [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-nativní plocha](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-nativní-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validace](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobilní zařízení (UPW)            | .![Tento obrázek znázorňuje .NET/C#/UWP](media/sample-v2-code/logo-windows.png)   | Interaktivní | [dotnet-nativní-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Tato ukázka používá [WAM](/windows/uwp/security/web-account-manager), ne [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (aplikace UPW používající ADAL.NET k volání webového rozhraní API jednoho klienta) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (aplikace UPW používající ADAL.NET k volání víceklientského webového rozhraní API)|
Mobilní (Android, iOS, UPW)   | ![Tento obrázek znázorňuje .NET/C# (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interaktivní | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobilní (Android)           | ![Tento obrázek znázorňuje logo Androidu](media/sample-v2-code/logo-android.png) | Interaktivní |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobilní (iOS)           | ![Tento obrázek znázorňuje iOS / Cíl C nebo Swift](media/sample-v2-code/logo-ios.png) | Interaktivní |   [nativníKlient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Plocha (konzola)          | ![Tento obrázek znázorňuje logo .NET/C#](media/sample-v2-code/logo-net.png) | Uživatelské jméno / heslo </p>  Integrované ověřování systému Windows | | [dotnet-nativní-bezhlavý](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Plocha (konzola)          | ![Tento obrázek znázorňuje logo Javy](media/sample-v2-code/logo-java.png) | Uživatelské jméno / heslo | | [java-nativní-bezhlavý](https://github.com/Azure-Samples/active-directory-java-native-headless)
Plocha (konzola)           | ![Tento obrázek znázorňuje logo .NET Core/C#](media/sample-v2-code/logo-netcore.png) | Tok kódu zařízení | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon aplikace (přístup k webovým API s identitou aplikace)

Následující ukázky zobrazují desktopové nebo webové aplikace, které přistupují k microsoft graphu nebo webovému rozhraní API bez uživatele (s identitou aplikace).

Klientská aplikace | Platforma | Tok/Grant | Volá webové rozhraní API ASP.NET nebo ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- 
Aplikace Daemon (konzola)          | ![Tento obrázek znázorňuje logo .NET](media/sample-v2-code/logo-netframework.png) | Pověření klienta s tajným tajemstvím aplikace nebo certifikátem | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Aplikace Daemon (konzola)         | ![Tento obrázek znázorňuje logo .NET](media/sample-v2-code/logo-netcore.png) | Pověření klienta s certifikátem| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET webová aplikace  | ![Tento obrázek znázorňuje logo .NET](media/sample-v2-code/logo-netframework.png) | Přihlašovací údaje klienta | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webová rozhraní API

### <a name="web-api-protected-by-azure-active-directory"></a>Webové rozhraní API chráněné službou Azure Active Directory

Následující ukázka ukazuje, jak chránit rozhraní node.js webové rozhraní API s Azure AD.

V předchozích částech tohoto článku můžete také najít další ukázky ilustrující klientskou aplikaci **volající** ASP.NET nebo ASP.NET základní **webové rozhraní API**. Tyto vzorky nejsou v této části znovu zmíněny, ale najdete je v posledním sloupci tabulek nad nebo pod

| Platforma | Ukázka |
|--------|-------------------|
| ![Tento obrázek znázorňuje logo Node.js](media/sample-v2-code/logo-nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webové rozhraní API volající microsoft graph nebo jiné webové rozhraní API

Následující ukázky ukazují webové rozhraní API, které volá jiné webové rozhraní API. Druhá ukázka ukazuje, jak zpracovat podmíněný přístup.

| Platforma |  Volání Microsoft Graph | Volá jiné ASP.NET nebo ASP.NET core 2.0 web API |
| -------- |  --------------------- | ------------------------- |
| ![Tento obrázek znázorňuje logo ASP.NET](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Další ukázky aplikace Microsoft Graph

Ukázky a kurzy, které ukazují různé vzorce využití rozhraní Microsoft Graph API, včetně ověřování pomocí služby Azure AD, najdete [v tématu Ukázky komunity Microsoft Graph & u kurzy](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Viz také

- [Průvodce vývojářem služby Azure Active Directory](v1-overview.md)
- [Knihovny ověřování služby Azure Active Directory](active-directory-authentication-libraries.md)
- [Koncepční a referenční informace rozhraní API rozhraní Microsoft Graph](https://docs.microsoft.com/graph/use-the-api)
