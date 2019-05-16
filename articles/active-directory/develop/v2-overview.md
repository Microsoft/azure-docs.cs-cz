---
title: Microsoft identity platform (v2.0) přehled – Azure
description: Další informace o Microsoft identity platform (v2.0) koncového bodu a platformu.
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
ms.openlocfilehash: dd3a1525d7b1f9c0a0b6516014ea83fb8abd0376
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544827"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Přehled Microsoft identity platform (v2.0)

Platforma Microsoft Identity Platform vznikla z platformy pro vývojáře a službu identit Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které všechny Microsoft identity Přihlaste se a získat tokeny pro volání APIs Microsoftu, jako je například Microsoft Graphu nebo rozhraní API, která jste vytvořili vývojáři. Platforma Microsoft identity se skládá ze:

- **Kompatibilní se standardní ověřovací službu OAuth 2.0 a OpenID Connect** , který umožňuje vývojářům k ověření libovolné identitě společnosti Microsoft, včetně:
  - Pracovní nebo školní účty (poskytované prostřednictvím Azure AD)
  - Osobní účty Microsoft (jako je Skype, Xbox a Outlook.com)
  - Účty sociálních sítí nebo místní (přes Azure AD B2C)
- **Knihovny Open-source**: Knihovny Microsoft Authentication (MSAL) a podporu pro další standardům knihovny
- **Portál pro správu aplikace**: Registrace a konfigurace prostředí založená na webu Azure Portal, spolu s všechny další možnosti správy Azure.
- **Konfigurace aplikace API a PowerShell**: Programová konfigurace aplikací pomocí rozhraní REST API (Microsoft Graph a Azure Active Directory Graph 1.6) a prostředí PowerShell, který umožňuje tak můžete automatizovat úlohy DevOps.
- **Obsah pro vývojáře**: konceptuální a referenční dokumentaci, ukázky rychlý start, ukázky, kurzy a návody.

Vývojářům nabízí platforma identit Microsoft bezproblémovou integraci do inovací v prostoru identity a zabezpečení, jako je například passwordless ověřování, stupňované ověřování a podmíněný přístup.  Není nutné implementovat tyto funkce sami: aplikace integrovaná s platformou identity Microsoft nativně využít výhod těchto inovací.

S platformou identity Microsoft můžete napsat kód jednou a oslovení uživatelů. Můžete vytvořit aplikaci jednou a jeho práci na mnoha platformách nebo sestavit aplikaci, která funguje jako klient, stejně jako aplikace prostředků (API).

## <a name="getting-started"></a>Začínáme

Práce s identitou nemusí být těžká. Zvolte scénář, který se vás týká – každá cesta scénář má rychlý start a na stránce Přehled, které vám pomůžou rychle zprovoznit během několika minut:

- [Sestavení jednostránkové aplikace](scenario-spa-overview.md)
- [Vytvoření webové aplikace, který se přihlásí uživatelé](scenario-web-app-sign-user-overview.md)
- [Vytvoření webové aplikace, která volá webové rozhraní API](scenario-web-app-call-api-overview.md)
- [Vytvoření chráněného webového rozhraní API](scenario-protected-web-api-overview.md)
- [Vytvoření webového rozhraní API, která volá webové rozhraní API](scenario-web-api-call-api-overview.md)
- [Vytváření desktopových aplikací](scenario-desktop-overview.md)
- [Vytvoření aplikace démona](scenario-daemon-overview.md)
- [Vytvořte mobilní aplikaci](scenario-mobile-overview.md)

Následující diagram popisuje běžné scénáře ověřování aplikace – použijte jako referenci při integraci platforma identit Microsoft s vaší aplikací.

[![Scénáře aplikací v Microsoft identity platform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Další postup

Pokud chcete získat další informace o základních konceptů ověřování, doporučujeme že začít s těmito tématy:

- [Základy ověřování](authentication-scenarios.md)
- [Aplikace a instanční objekty](app-objects-and-service-principals.md)
- [cílové skupiny](v2-supported-account-types.md)
- [Oprávnění a souhlas](v2-permissions-and-consent.md)
- [Tokeny typu ID](id-tokens.md) a [přístupové tokeny](access-tokens.md)

Sestavit aplikaci bohatého na data, která volá [Microsoft Graphu](https://docs.microsoft.com/graph/overview).

Jakmile budete připraveni ke spuštění vaší aplikace do **produkčního prostředí**, tyto osvědčené postupy:

- [Povolení protokolování](msal-logging.md) ve vaší aplikaci.
- Povolte telemetrii v aplikaci.
- Povolit [proxy servery a vlastní nastavení klientů protokolu HTTP](msal-net-provide-httpclient.md).
- Otestovat integraci vašich podle [kontrolní seznam k Microsoft identity platform integrace](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Další informace

Pokud jste měli v úmyslu vytvořit aplikaci určenou pro zákazníky, který se přihlásí sociálních sítí a místní identity, podívejte se na [přehled Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).