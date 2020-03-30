---
title: Microsoft identity platforma (v2.0) přehled - Azure
description: Přečtěte si o koncovém bodu a platformě platformy Microsoft identity platformy (v2.0).
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
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f6953fb5d016c802db260c55bc4970c3f029ab1a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240825"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Microsoft identity platformy (v2.0) přehled

Platforma identit Microsoftu vznikla z platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašují všechny identity Microsoftu a získat tokeny pro volání rozhraní API společnosti Microsoft, jako je microsoft graph nebo rozhraní API, která vývojáři vytvořili. Platforma identit společnosti Microsoft se skládá z:

- **OAuth 2.0 a OpenID Connect standard-kompatibilní ověřovací služba,** která umožňuje vývojářům ověřit všechny identity Microsoftu, včetně:
  - Pracovní nebo školní účty (zřízené prostřednictvím Azure AD)
  - Osobní účty Microsoft (například Skype, Xbox a Outlook.com)
  - Sociální nebo místní účty (přes Azure AD B2C)
- **Open-source knihovny:** Knihovny ověřování Microsoft (MSAL) a podpora dalších knihoven kompatibilních se standardy
- **Portál pro správu aplikací:** Prostředí pro registraci a konfiguraci vytvořené na webu Azure Portal spolu se všemi dalšími možnostmi správy Azure.
- **Konfigurační rozhraní API aplikace a prostředí PowerShell:** které umožňuje programovou konfiguraci vašich aplikací prostřednictvím rozhraní Microsoft Graph API a PowerShellu, takže můžete automatizovat úlohy DevOps.
- **Obsah pro vývojáře**: koncepční a referenční dokumentace, ukázky rychlého startu, ukázky kódu, kurzy a návody.

Pro vývojáře platforma identit Microsoftu nabízí bezproblémovou integraci do inovací v prostoru identity a zabezpečení, jako je ověřování bez hesla, step-up ověřování a podmíněný přístup.  Tyto funkce nemusíte implementovat sami: aplikace integrované s platformou identit microsoftu nativně využívají těchto inovací.

S platformou microsoftu identity můžete napsat kód jednou a oslovit libovolného uživatele. Můžete vytvořit aplikaci jednou a mít to fungovat na mnoha platformách, nebo vytvořit aplikaci, která funguje jako klient, stejně jako aplikace prostředků (API).

## <a name="getting-started"></a>Začínáme

Práce s identitou nemusí být těžká. 

Podívejte se na [video platformy identit Microsoftu,](identity-videos.md) ve kterém se dozvíte základní informace. 

Vyberte [si scénář,](authentication-flows-app-scenarios.md) který se vás týká – každá cesta scénáře má rychlý start a stránku s přehledem, která vám zprovozní během několika minut:

- [Vytvoření jednostránkové webové aplikace](scenario-spa-overview.md)
- [Vytvoření webové aplikace, která přihlašuje uživatele](scenario-web-app-sign-user-overview.md)
- [Vytvoření webové aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md)
- [Vytvoření chráněného webového rozhraní API](scenario-protected-web-api-overview.md)
- [Vytvoření webového rozhraní API, které volá webová rozhraní API](scenario-web-api-call-api-overview.md)
- [Vytvoření aplikace klasické pracovní plochy](scenario-desktop-overview.md)
- [Vytvoření aplikace pro daemon](scenario-daemon-overview.md)
- [Vytvoření mobilní aplikace](scenario-mobile-overview.md)

Následující graf popisuje běžné scénáře ověřovacíaplikace – použijte ji jako referenci při integraci platformy identit y Microsoft u vaší aplikace.

[![Scénáře aplikací v platformě microsoftidentity](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět více o základních konceptech ověřování, doporučujeme začít s těmito tématy:

- [Toky ověřování a scénáře aplikací](authentication-flows-app-scenarios.md)
- [Základy ověřování](authentication-scenarios.md)
- [Instanční objekty a instanční objekty](app-objects-and-service-principals.md)
- [Publikum](v2-supported-account-types.md)
- [Oprávnění a souhlas](v2-permissions-and-consent.md)
- [ID tokeny](id-tokens.md) a [přístupové tokeny](access-tokens.md)

Vytvořte aplikaci bohatou na data, která volá [Microsoft Graph](https://docs.microsoft.com/graph/overview).

Až budete připraveni spustit aplikaci do **produkčního prostředí**, přečtěte si tyto doporučené postupy:

- [Povolte protokolování](msal-logging.md) v aplikaci.
- Povolte telemetrii ve vaší aplikaci.
- Povolte [proxy servery a přizpůsobte klienty HTTP](msal-net-provide-httpclient.md).
- Otestujte integraci podle [kontrolního seznamu integrace platformy identit společnosti Microsoft](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Další informace

Pokud plánujete vytvořit aplikaci orientosnou na zákazníka, která se přihlásí do sociálních a místních identit, přečtěte [si přehled Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
