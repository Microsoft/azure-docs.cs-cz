---
title: Přehled platformy Microsoft Identity – Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o komponentách platformy Microsoft identity a o tom, jak vám můžou pomoci při sestavování podpory IAM (Identity and Access Management) pro vaše aplikace.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40
ms.openlocfilehash: e63d2cac97a16674c4e2e880f94373b0af1510de
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056408"
---
# <a name="what-is-the-microsoft-identity-platform"></a>Co je Microsoft Identity Platform?

Platforma Microsoft Identity vám pomůže sestavovat aplikace, které můžou uživatelé a zákazníci přihlašovat k používání svých identit Microsoftu nebo účtů sociálních sítí, a poskytovat autorizovaný přístup k vašim vlastním rozhraním API nebo rozhraním API Microsoftu, jako je Microsoft Graph.

Platforma Microsoft Identity Platform tvoří několik součástí:

- **Ověřovací služba kompatibilní se standardem OAuth 2,0 a OpenID Connect** , která umožňuje vývojářům ověřovat několik typů identity, včetně těchto:
  - Pracovní nebo školní účty zřízené prostřednictvím služby Azure AD
  - Osobní účet Microsoft, jako Skype, Xbox a Outlook.com
  - Sociální nebo místní účty pomocí Azure AD B2C
- **Open source knihovny**: knihovny Microsoft Authentication Library (MSAL) a podpora dalších knihoven odpovídajících standardům
- **Portál pro správu aplikací**: prostředí pro registraci a konfiguraci v Azure Portal společně s dalšími možnostmi správy Azure.
- **Rozhraní API pro konfiguraci aplikací a PowerShell**: Programová konfigurace vašich aplikací prostřednictvím rozhraní Microsoft Graph API a PowerShellu, abyste mohli automatizovat úlohy DevOps.
- **Vývojářský obsah**: Technická dokumentace, včetně rychlých startů, kurzů, průvodců návody a ukázek kódu.

Pro vývojáře nabízí platforma Microsoft Identity Platform integraci moderních inovací v rámci identity a prostoru zabezpečení, jako je ověřování bez hesla, podrobné ověřování a podmíněný přístup. Nemusíte tyto funkce implementovat sami: aplikace integrované s Microsoft Identity platformou nativně využívají takové inovace.

S platformou Microsoft Identity můžete napsat kód jenom jednou a oslovit každého uživatele. Můžete vytvořit aplikaci jednou a nechat ji pracovat na celé řadě platforem nebo vytvořit aplikaci, která funguje jako klient a také jako aplikace prostředků (API).

## <a name="getting-started"></a>Začínáme

Výběr [scénáře aplikace](authentication-flows-app-scenarios.md) , kterou chcete sestavit. Každá z těchto cest scénáře začíná přehledem a odkazy na rychlý Start, které vám pomůžou začít pracovat:

- [Jednostránková aplikace (SPA)](scenario-spa-overview.md)
- [Webová aplikace, která přihlašuje uživatele](scenario-web-app-sign-user-overview.md)
- [Webová aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md)
- [Chráněné webové rozhraní API](scenario-protected-web-api-overview.md)
- [Webové rozhraní API, které volá webová rozhraní API](scenario-web-api-call-api-overview.md)
- [Desktopová aplikace](scenario-desktop-overview.md)
- [Aplikace démona](scenario-daemon-overview.md)
- [Mobilní aplikace](scenario-mobile-overview.md)

Při práci s platformou Microsoft identity k integraci ověřování a autorizace ve vašich aplikacích můžete použít odkaz na tento obrázek, který popisuje nejběžnější scénáře aplikací a jejich součásti identity. Vyberte obrázek pro zobrazení v plné velikosti.

[![Mapa Metro ukazující několik scénářů aplikací v platformě Microsoft Identity Platform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>Informace o konceptech ověřování

Přečtěte si, jak se základní ověřování a koncepce Azure AD vztahují na platformu Microsoft identity v této Doporučené sadě článků:

- [Základy ověřování](./authentication-vs-authorization.md)
- [Aplikační a instanční objekty](app-objects-and-service-principals.md)
- [Publikum](v2-supported-account-types.md)
- [Oprávnění a souhlas](v2-permissions-and-consent.md)
- [Tokeny ID](id-tokens.md)
- [Přístupové tokeny](access-tokens.md)
- [Toky ověřování a scénáře aplikací](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>Další možnosti správy identit a přístupu

[Azure AD B2C](../../active-directory-b2c/overview.md) – sestavování zákaznických aplikací, které se uživatelé můžou přihlašovat pomocí svých sociálních účtů, jako je Facebook nebo Google, nebo pomocí e-mailové adresy a hesla.

[Azure AD B2B](../external-identities/what-is-b2b.md) – pozvání externích uživatelů do tenanta služby Azure AD jako uživatele typu Host a přiřazení oprávnění k autorizaci, když používají jejich existující pověření k ověřování.

[Azure Active Directory pro vývojáře (v 1.0)](../azuread-dev/v1-overview.md) – zobrazené tady pro vývojáře s existujícími aplikacemi, které používají koncový bod starší verze 1.0. **Nepoužívejte v** 1.0 pro nové projekty.

## <a name="next-steps"></a>Další kroky

Pokud máte účet Azure, ke kterému už máte Azure Active Directory přístup, ale většina vývojářů platforem Microsoft Identity Platform potřebuje svůj vlastní tenant Azure AD pro použití při vývoji aplikací, "vývojářského tenanta".

Zjistěte, jak vytvořit vlastního tenanta pro použití při sestavování aplikací:

[Rychlý Start: nastavení tenanta Azure AD](quickstart-create-new-tenant.md)
