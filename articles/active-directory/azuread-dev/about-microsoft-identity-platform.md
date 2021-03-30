---
title: Vývoj platformy Microsoft Identity Platform – Azure
description: Přečtěte si o platformě Microsoft Identity Platform a vývoji služby identity Azure Active Directory (Azure AD) a vývojářské platformy.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv, marsma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ab0057f55ddb5bf76f6fa74b0211b5ea5af24288
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96608210"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Rozvoj platformy Microsoft Identity Platform

[Platforma Microsoft Identity](../develop/index.yml) je vývojem vývojářské platformy Azure Active Directory (Azure AD). Umožňuje vývojářům sestavovat aplikace, které přihlásí uživatele, získat tokeny pro volání rozhraní API, například Microsoft Graph nebo rozhraní API, které vývojáři sestavili. Skládá se ze služby ověřování, open source knihoven, registrace aplikací a konfigurace (prostřednictvím portálu pro vývojáře a rozhraní API aplikace), úplné dokumentace pro vývojáře, ukázek pro rychlý Start, ukázek kódu, kurzů, návody a dalšího obsahu pro vývojáře. Microsoft Identity Platform podporuje standardní oborové protokoly, jako jsou OAuth 2.0 a OpenID Connect.

Spousta vývojářů už dřív pracovala s platformou Azure AD v 1.0 k ověřování pracovních a školních účtů (zřízené službou Azure AD) tím, že požaduje tokeny z koncového bodu Azure AD v 1.0 pomocí služby Azure AD Authentication Library (ADAL), Azure Portal pro registraci a konfiguraci aplikací a rozhraní Microsoft Graph API pro konfiguraci programových aplikací.

Díky jednotné platformě Microsoft identity (v 2.0) můžete napsat kód jednou a ověřit jakoukoli identitu Microsoftu do své aplikace. Pro několik platforem se pro použití s koncovými body platformy identity doporučuje plně podporovaná knihovna MSAL (Open Source Microsoft Authentication Library). MSAL se snadno používá, nabízí uživatelům Skvělé prostředí jednotného přihlašování (SSO) pro vaše uživatele, pomáhá dosahovat vysoké spolehlivosti a výkonu a vyvíjí se pomocí SDL (Microsoft Secure Development Lifecycle). Při volání rozhraní API můžete svou aplikaci nakonfigurovat tak, aby využívala výhod přírůstkového souhlasu, která umožňuje zpozdit žádost o souhlas s více invazivními rozsahy, dokud použití aplikace neopravňuje k tomu za běhu.  MSAL také podporuje Azure Active Directory B2C, takže vaši zákazníci používají své preferované sociální, podnikové nebo místní identity účtu k získání přístupu k aplikacím a rozhraním API pomocí jednotného přihlašování.

S platformou Microsoft Identity můžete rozšířit svůj přístup k těmto typům uživatelů:

- Pracovní a školní účty (účty zřízené Azure AD)
- Osobní účty (například Outlook.com nebo Hotmail.com)
- Vaši zákazníci, kteří přinášejí vlastní e-mail nebo sociální identitu (například LinkedIn, Facebook, Google) prostřednictvím MSAL a Azure AD B2C

Azure Portal můžete použít k registraci a konfiguraci aplikace a k použití rozhraní Microsoft Graph API pro konfiguraci programových aplikací.

Aktualizujte svou aplikaci vlastním tempem. Aplikace vytvořené pomocí knihoven ADAL budou nadále podporovány. Podporují se i smíšené portfolia aplikací, které se skládají z aplikací vytvořených pomocí ADAL a aplikací vytvořených pomocí knihoven MSAL. To znamená, že aplikace používající nejnovější ADAL a nejnovější MSAL budou v rámci portfolia poskytovat jednotné přihlašování, které poskytuje mezipaměť sdíleného tokenu mezi těmito knihovnami. Aplikace aktualizované z ADAL na MSAL budou při upgradu udržovat stav přihlášení uživatele.

## <a name="microsoft-identity-platform-experience"></a>Používání platformy Microsoft Identity Platform

Na následujícím schématu vidíte používání identit Microsoftu na vysoké úrovni včetně použití registrace aplikací, sad SDK, koncových bodů a podporovaných identit.

![Platforma Microsoft Identity Platform dnes](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Prostředí pro registraci aplikací

Prostředí Azure Portal **[Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908)** je prostředí s jedním portálem pro správu všech aplikací, které jste integrací s platformou Microsoft identity. Pokud jste používali portál pro registraci aplikací, začněte místo toho používat prostředí pro registraci aplikací Azure Portal.

Pro integraci s Azure AD B2C (při ověřování sociálních nebo místních identit) budete muset aplikaci zaregistrovat v tenantovi Azure AD B2C. Toto prostředí je také součástí Azure Portal.

Pomocí [rozhraní API pro aplikace](/graph/api/resources/application) můžete programově nakonfigurovat aplikace integrované s platformou Microsoft identity pro ověřování jakékoli identity Microsoftu.

### <a name="msal-libraries"></a>Knihovny MSAL

Pomocí knihovny MSAL můžete sestavovat aplikace, které ověřují všechny identity Microsoftu. Knihovny MSAL v rozhraní .NET a JavaScriptu jsou všeobecně dostupné. Knihovny MSAL pro iOS a Android jsou ve verzi Preview a jsou vhodné pro použití v produkčním prostředí. Poskytujeme stejnou podporu na úrovni výroby pro knihovny MSAL ve verzi Preview, a to pro verze MSAL a ADAL, které jsou všeobecně dostupné.

Pomocí knihoven MSAL můžete také integrovat aplikaci s Azure AD B2C.

### <a name="microsoft-identity-platform-endpoint"></a>Koncový bod platformy Microsoft identity

Koncový bod Microsoft Identity Platform (v 2.0) je OIDC certifikovaný. Funguje s knihovnami Microsoft Authentication Library (MSAL) nebo jinými knihovnami kompatibilními s normami. V souladu s oborovým standardem implementuje lidské čitelné rozsahy.

## <a name="next-steps"></a>Další kroky

Další informace najdete v [dokumentaci k platformě Microsoft Identity Platform](../develop/index.yml).
