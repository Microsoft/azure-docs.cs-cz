---
title: Vývoj platforma identit Microsoft - Azure
description: Další informace o Microsoft identity platform vývojem představ o platformu služby a pro vývojáře Azure Active Directory (Azure AD) identity.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 807045089f70e117d46754412d974be7fba5a77a
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832499"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Rozvoj platformy Microsoft Identity Platform

Platforma identit Microsoftu vznikla z platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašování uživatelů, získat tokeny pro volání rozhraní API, jako je například Microsoft Graphu nebo rozhraní API, která jste vytvořili vývojáři. Skládá se z ověřovací služba, open source knihoven, registrace aplikace a konfigurace (prostřednictvím portálu pro vývojáře a aplikace API), dokumentaci pro vývojáře, ukázky rychlý start, ukázky, kurzy, Průvodce postupy, a Další obsah pro vývojáře. Microsoft Identity Platform podporuje standardní oborové protokoly, jako jsou OAuth 2.0 a OpenID Connect.

Až dosud Většina vývojářů pracovali s platformou Azure AD verze 1.0 a ověření pracovních a školních účtů (zřídit ve službě Azure AD) vyžádáním tokenů z koncového bodu Azure AD v1.0, pomocí Azure AD Authentication Library (ADAL), webu Azure portal registrace aplikace a konfigurace a Azure AD Graph API pro konfiguraci aplikace prostřednictvím kódu programu.

S platformou identity Microsoft (verze 2.0) Rozšiřte svůj dosah na těchto druhů uživatelů:

- Pracovním a školním účtům (účty, zřízené služby Azure AD)
- Osobní účty (například Outlook.com nebo Hotmail.com)
- Vaši zákazníci, kteří přináší svá vlastní e-mailu nebo identity v sociálních sítích (jako je LinkedIn, Facebook, Google) prostřednictvím nabídky Azure AD B2C

Jednotná platforma identit Microsoft můžete napsat kód jednou a ověření identity libovolné Microsoftu do vaší aplikace. Pro několik platforem je plně podporován open source knihovnu s názvem Microsoft Authentication Library (MSAL). Knihovna MSAL se snadno používá, poskytuje skvělé jednotné přihlašování (SSO) prostředí pro vaše uživatele, pomáhá dosahovat vysokou spolehlivost a výkon a vyvinutý podle Microsoft Secure Development Lifecycle (SDL). Při volání rozhraní API, můžete nakonfigurovat aplikaci využívat přírůstkové souhlasu, která umožňuje zpoždění žádost o souhlas pro další invazivní obory, dokud používání vaší aplikace to stojí za běhu.

Můžete pomocí webu Azure portal k registraci a konfiguraci vaší aplikace a používat rozhraní Microsoft Graph API pro konfiguraci aplikací prostřednictvím kódu programu.

Aktualizace aplikace svým vlastním tempem. Aplikace vytvořené pomocí knihovny ADAL i dál podporovaná. Portfolií hybridní aplikace, které se skládají z aplikace vytvořené pomocí knihovny ADAL a aplikace vytvořené pomocí knihovny MSAL, jsou také podporovány. To znamená, že aplikací s použitím nejnovější knihovnu ADAL a nejnovější MSAL bude poskytovat jednotné přihlašování napříč vaším portfoliem, poskytuje Sdílená mezipaměť tokenu mezi tyto knihovny. Aplikace aktualizována z ADAL na MSAL bude udržovat stav přihlášení uživatele po upgradu.

## <a name="microsoft-identity-platform-experience"></a>Používání platformy Microsoft Identity Platform

Na následujícím schématu vidíte používání identit Microsoftu na vysoké úrovni včetně použití registrace aplikací, sad SDK, koncových bodů a podporovaných identit.

![Platforma Microsoft Identity Platform dnes](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Prostředí registrace aplikací

Na webu Azure portal **[registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908)** prostředí je jeden prostředí portálu pro správu všech aplikací, které jste integrovali se službou Microsoft identity platform. Pokud používáte portál pro registraci aplikací, spouští se místo toho použít prostředí registrace aplikace Azure portal.

Integrace s Azure AD B2C (při ověřování identity v sociálních sítích nebo místní) budete muset zaregistrovat aplikaci v tenantovi B2C. Toto prostředí je také součástí na webu Azure portal.

**Aplikace API v Microsoft Graphu** je aktuálně ve verzi preview. Pomocí tohoto rozhraní API pro programovou konfiguraci vaší aplikace integrované s platformou identity Microsoft za účelem ověřování totožnosti jakékoli Microsoft identity. Ale dokud toto rozhraní API ve fázi obecné dostupnosti, by měl použít rozhraní API pro Azure AD Graph 1.6 a manifest aplikace.

### <a name="msal-libraries"></a>Knihovna MSAL knihovny

Knihovna MSAL můžete použít k vytváření aplikací, které se ověřují všechny identity společnosti Microsoft. Knihovna MSAL knihoven v .NET jsou obecně dostupné. Knihovna MSAL knihovny pro JavaScript, iOS a Android jsou ve verzi preview a je vhodný pro použití v produkčním prostředí. Poskytujeme produkční úrovni podporu pro knihovny knihovna MSAL ve verzi preview jako My pro verze MSAL a ADAL, které jsou obecně dostupné.

Knihovna MSAL knihovny můžete použít také k integraci vaší aplikace s Azure AD B2C.

Na straně serveru knihovny pro vývoj webových aplikací a webových rozhraní API jsou obecně dostupné: [ASP.NET](https://docs.microsoft.com/aspnet/overview) a [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Koncový bod platforma identit Microsoft

Koncový bod Microsoft identity platform (v2.0) je teď OIDC certified. Funguje to knihovny Microsoft Authentication (MSAL) nebo jiné kompatibilní se standardy knihovny. Implementuje lidské čitelné obory, v souladu se standardy.

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o verzích v1.0 a v2.0.

* [Přehled Microsoft identity platform (v2.0)](v2-overview.md)
* [Azure Active Directory pro vývojáře (verze 1.0) – přehled](v1-overview.md)
