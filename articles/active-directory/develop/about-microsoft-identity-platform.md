---
title: Vývoj platformy microsoftových identit – Azure
description: Přečtěte si o platformě microsoftových identit, což je vývoj služby Identity Azure Active Directory (Azure AD) a vývojářské platformy.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.openlocfilehash: 8714b7a96197cb4a59b29bada31b5559961bf8e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78300209"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Rozvoj platformy Microsoft Identity Platform

Platforma identit Microsoftu vznikla z platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašují uživatele, získat tokeny pro volání rozhraní API, jako je microsoft graph nebo rozhraní API, která vývojáři vytvořili. Skládá se ze služby ověřování, knihoven s otevřeným zdrojovým kódem, registrace a konfigurace aplikací (prostřednictvím vývojářského portálu a rozhraní API aplikace), úplné dokumentace pro vývojáře, ukázek rychlého startu, ukázek kódu, výukových programů, návodů a další obsah pro vývojáře. Microsoft Identity Platform podporuje standardní oborové protokoly, jako jsou OAuth 2.0 a OpenID Connect.

Až dosud většina vývojářů spolupracovala s platformou Azure AD v1.0 na ověřování pracovních a školních účtů (zřízených službou Azure AD) vyžádáním tokenů z koncového bodu Azure AD v1.0 pomocí Azure AD Authentication Library (ADAL), portálu Azure pro registrace a konfigurace aplikace a rozhraní Microsoft Graph API pro konfiguraci programových aplikací.

Pomocí sjednocené platformy identit microsoftu (v2.0) můžete napsat kód jednou a ověřit libovolnou identitu Microsoftu do vaší aplikace. Pro několik platforem je doporučeno plně podporované open source Knihovna ověřování Microsoft (MSAL) pro použití proti koncovým bodům platformy identity. MSAL je jednoduchý na používání, poskytuje skvělé jednotné přihlašování (SSO) prostředí pro vaše uživatele, pomáhá dosáhnout vysoké spolehlivosti a výkonu a je vyvinut pomocí Microsoft Secure Development Lifecycle (SDL). Při volání rozhraní API můžete nakonfigurovat aplikaci tak, aby využívala výhod přírůstkového souhlasu, což umožňuje odložit žádost o souhlas pro více invazivní obory, dokud to použití aplikace neodůvodní za běhu.  MSAL také podporuje Azure Active Directory B2C, takže vaši zákazníci používají své upřednostňované sociální, podnikové nebo místní identity účtů k získání jednotného přihlášení k vašim aplikacím a rozhraním API.

S platformou microsoftových identit rozšiřte svůj dosah na tyto typy uživatelů:

- Pracovní a školní účty (zřízené účty Azure AD)
- Osobní účty (například Outlook.com nebo Hotmail.com)
- Vaši zákazníci, kteří si prostřednictvím Služby MSAL a Azure AD B2C přinesou vlastní e-mail nebo sociální identitu (například LinkedIn, Facebook, Google)

Portál Azure můžete použít k registraci a konfiguraci aplikace a použití rozhraní Microsoft Graph API pro konfiguraci programových aplikací.

Aktualizujte svou aplikaci svým vlastním tempem. Aplikace vytvořené pomocí knihoven ADAL jsou i nadále podporovány. Podporována jsou také portfolia smíšených aplikací, která se skládají z aplikací vytvořených pomocí ADAL a aplikací vytvořených pomocí knihoven MSAL. To znamená, že aplikace využívající nejnovější ADAL a nejnovější MSAL bude poskytovat jednotné přihlašování v celém portfoliu, poskytované sdílené token mezipaměti mezi těmito knihovnami. Aplikace aktualizované z ADAL na MSAL bude udržovat stav přihlášení uživatele při upgradu.

## <a name="microsoft-identity-platform-experience"></a>Používání platformy Microsoft Identity Platform

Na následujícím schématu vidíte používání identit Microsoftu na vysoké úrovni včetně použití registrace aplikací, sad SDK, koncových bodů a podporovaných identit.

![Platforma Microsoft Identity Platform dnes](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Prostředí registrace aplikací

Prostředí **[registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908)** na portálu Azure je jedno prostředí portálu pro správu všech aplikací, které jste integrovali s platformou identit Microsoftu. Pokud používáte portál registrace aplikací, začněte místo toho používat prostředí registrace aplikace Portálu Azure.

Pro integraci s Azure AD B2C (při ověřování sociálních nebo místních identit), budete muset zaregistrovat aplikaci v tenantovi Azure AD B2C. Toto prostředí je také součástí portálu Azure.

Pomocí [rozhraní API aplikace](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0) můžete programově konfigurovat aplikace integrované s platformou identit microsoftu pro ověřování jakékoli identity společnosti Microsoft.

### <a name="msal-libraries"></a>Knihovny MSAL

Knihovnu MSAL můžete použít k vytváření aplikací, které ověřují všechny identity microsoftu. Knihovny MSAL v rozhraní .NET a JavaScript jsou obecně dostupné. Knihovny MSAL pro iOS a Android jsou ve verzi preview a vhodné pro použití v produkčním prostředí. Poskytujeme stejnou podporu na úrovni výroby pro knihovny MSAL ve verzi preview jako pro verze MSAL a ADAL, které jsou obecně dostupné.

Knihovny MSAL můžete také použít k integraci aplikace s Azure AD B2C.

Knihovny na straně serveru pro vytváření webových aplikací a webových rozhraní API jsou obecně dostupné: [ASP.NET](https://docs.microsoft.com/aspnet/overview) a [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Koncový bod platformy identit y Microsoft

Koncový bod platformy identit y Microsoft (v2.0) je nyní certifikován OIDC. Pracuje s knihovnami Microsoft Authentication Libraries (MSAL) nebo s jakoukoli jinou knihovnou kompatibilní se standardy. Implementuje lidské čitelné obory v souladu s průmyslovými standardy.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o verzích v1.0 a v2.0.

* [Microsoft identity platformy (v2.0) přehled](v2-overview.md)
* [Přehled služby Azure Active Directory pro vývojáře (v1.0)](../azuread-dev/v1-overview.md)
