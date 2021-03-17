---
title: Microsoft Identity web Authentication Library – přehled
titleSuffix: Microsoft identity platform
description: Přečtěte si o webu Microsoft Identity web, o knihovně ověřování a autorizaci pro ASP.NET Core aplikací, které se integrují s Azure Active Directory, Azure AD B2C a Microsoft Graph a dalšími webovými rozhraními API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8bc9773fa7e1b3db2e249e803f4d2a3cf39edb48
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102542"
---
# <a name="microsoft-identity-web-authentication-library"></a>Microsoft Identity web Authentication Library

Microsoft Identity web je sada ASP.NET Corech knihoven, které zjednodušují přidávání ověřování a podpory autorizace do webových aplikací a webových rozhraní API integrací s platformou Microsoft identity. Poskytuje vrstvu rozhraní API s jedním povrchem, která spojuje ASP.NET Core, jeho middleware ověřování a [Microsoft Authentication Library (MSAL) pro .NET](https://github.com/azuread/microsoft-authentication-library-for-dotnet).

## <a name="supported-application-scenarios"></a>Podporované scénáře aplikací

Pokud vytváříte ASP.NET Core Web Apps nebo webová rozhraní API a chcete používat Azure Active Directory (Azure AD) nebo Azure AD B2C pro správu identit a přístupu (IAM), doporučujeme používat web Microsoft Identity web pro všechny tyto scénáře:

- [Webová aplikace, která přihlašuje uživatele](scenario-web-app-sign-user-overview.md)
- [Webová aplikace, která podepisuje uživatele a volá webové rozhraní API jménem](scenario-web-app-call-api-overview.md)
- [Chráněné webové rozhraní API, ke kterým mají přístup jenom ověření uživatelé](scenario-protected-web-api-overview.md)
- [Chráněné webové rozhraní API, které jménem přihlášeného uživatele volá jiné webové rozhraní API (pro příjem dat)](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>Získat knihovnu

Web Microsoft Identity Web můžete získat z [NuGet](#nuget), [šablon projektů .NET Core](#project-templates)a [GitHubu](#github).

#### <a name="nuget"></a>NuGet

Web Microsoft Identity web je k dispozici na NuGet jako sada balíčků, které poskytují modulární funkčnost na základě potřeb vaší aplikace. Pro `dotnet add` instalaci balíčků odpovídajících vašemu projektu použijte příkaz rozhraní .NET CLI nebo **Správce balíčků NuGet** sady Visual Studio:

- [Microsoft. identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) – hlavní balíček. Vyžadováno všemi aplikacemi, které používají web Microsoft Identity Web.
- [Microsoft. identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) – volitelné. Přidá uživatelské rozhraní pro přihlášení a odhlášení uživatele a přidružený kontroler pro webové aplikace.
- [Microsoft. identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) – volitelné. Poskytuje zjednodušenou interakci s rozhraním Microsoft Graph API.
- [Microsoft. identity. Web. MicrosoftGraphBeta](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraphBeta) – volitelné. Poskytuje zjednodušenou interakci s [koncovým bodem](/graph/api/overview?view=graph-rest-beta&preserve-view=true)Microsoft Graph API beta.

#### <a name="project-templates"></a>Šablony projektů

Šablony webového projektu Microsoft identity jsou součástí .NET 5,0 a jsou k dispozici ke stažení pro projekty ASP.NET Core 3,1.

Pokud používáte ASP.NET Core 3,1, nainstalujte šablony pomocí rozhraní .NET CLI:

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

Následující diagram znázorňuje vysoké zobrazení podporovaných typů aplikací a jejich relevantních argumentů:

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Diagram dostupných šablon projektů rozhraní .NET CLI pro Microsoft Identity Web":::
<br /><sup><b>*</b></sup>`MultiOrg`není podporován v systému `webapi2` , ale lze jej povolit v *appsettings.jsv* nástroji nastavením tenant na `common` nebo`organizations`
<br /><sup><b>**</b></sup>`--calls-graph`není podporován pro Azure AD B2C

Tento ukázkový příkaz rozhraní .NET CLI, který je pořízený z našeho [kurzu Blazor serveru](tutorial-blazor-server.md), vygeneruje nový projekt Blazor serveru, který obsahuje pravé balíčky a počáteční kód (zobrazené hodnoty zástupných znaků):

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id "00000000-0000-0000-0000-000000000000" --tenant-id "11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHubu

Microsoft Identity web je open source projekt hostovaný na GitHubu: <a href="https://github.com/AzureAD/microsoft-identity-web" target="_blank">AzureAD/Microsoft-Identity-web</a>

[Wikiweb úložiště](https://github.com/AzureAD/microsoft-identity-web/wiki) obsahuje další dokumentaci a pokud potřebujete pomáhat nebo objevovat chybu, můžete založit [problém](https://github.com/AzureAD/microsoft-identity-web/issues).

## <a name="features"></a>Funkce

Microsoft Identity web obsahuje několik funkcí, které nejsou k dispozici, pokud použijete výchozí šablony projektu ASP.NET 3,1.

| Funkce                                                                                  | ASP.NET Core 3,1                                                     | Microsoft Identity Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [Přihlášení uživatelů](scenario-web-app-sign-user-app-configuration.md) ve službě Web Apps             | <li>Pracovní nebo školní účty<li>Sociální identity (s Azure AD B2C) | <li>Pracovní nebo školní účty<li>Osobní účty Microsoft<li>Sociální identity (s Azure AD B2C)     |
| [Ochrana webových rozhraní API](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>Pracovní nebo školní účty<li>Sociální identity (s Azure AD B2C) | <li>Pracovní nebo školní účty<li>Osobní účty Microsoft<li>Sociální identity (s Azure AD B2C)     |
| Ověření vystavitele ve více tenantů aplikacích                                                   | Ne                                                                   | Ano, pro [všechny cloudy](authentication-national-cloud.md) a [Azure AD B2C](../../active-directory-b2c/index.yml) |
| Webová aplikace/rozhraní API [volání Microsoft Graph] [scénář-API-Call-Graph]                             | Ne                                                                   | Ano                                                                                                     |
| Webová aplikace/rozhraní API [volání webového rozhraní API] [scénář-API-Call-API]                                       | Ne                                                                   | Ano                                                                                                     |
| Podporuje přihlašovací údaje certifikátu                                                         | Ne                                                                   | Ano, včetně Azure Key Vault                                                                          |
| Přírůstkové vyjádření souhlasu a podpora podmíněného přístupu ve webových aplikacích                           | Ne                                                                   | Ano, na stránkách MVC, Razor a Blazor                                                                    |
| Certifikáty pro šifrování tokenů ve webových rozhraních API                                                | Ne                                                                   | Ano                                                                                                     |
| [Obory/ověřování role aplikace] [scénář – ověřování rozhraní API] ve webových rozhraních API                        | Ne                                                                   | Ano                                                                                                     |
| `WWW-Authenticate` generování hlaviček ve webových rozhraních API                                         | Ne                                                                   | Ano                                                                                                     |

## <a name="next-steps"></a>Další kroky

Pokud se chcete podívat na web Microsoft identity v akci, vyzkoušejte náš kurz Blazor serveru:

[Kurz: Vytvoření aplikace serveru Blazor, která pro ověřování používá platformu Microsoft Identity Platform](tutorial-blazor-server.md)

Web wiki webu Microsoft identity na GitHubu obsahuje rozsáhlou referenční dokumentaci pro různé aspekty knihovny. Například použití certifikátu, přírůstkový souhlas a odkaz na podmíněný přístup najdete tady:

- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates" target="_blank">Používání certifikátů s Microsoft. identity. Web</a> (GitHub)
- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access" target="_blank">Přírůstkový souhlas a podmíněný přístup</a> (GitHub)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[scénář-API-Call-API]: scénář-Web-API-Call-API-Call-API. MD # Option-1-Call-Microsoft-Graph-with-the-SDK  
[scénář-API-Call-Graph]: scénář-Web-API-Call-API-Call-API. MD # Option-1-Call-Microsoft-Graph-with-the-SDK  
[scénář – ověřování rozhraní API]: scenario-protected-web-api-verification-scope-app-roles.md  
