---
title: Microsoft Identity Platform (v 2.0) – přehled – Azure
description: Seznamte se s koncovým bodem a platformou Microsoft Identity Platform (v 2.0).
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
ms.openlocfilehash: 6280f3784247b671521a4994be1f233d4913d90c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77194496"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Microsoft Identity Platform (v 2.0) – přehled

Platforma identit Microsoftu vznikla z platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlásí všechny identity Microsoftu, a získat tokeny pro volání rozhraní API Microsoftu, jako jsou Microsoft Graph nebo rozhraní API, které vývojáři vytvořili. Platforma Microsoft identity se skládá z těchto součástí:

- **Protokol OAuth 2,0 a služba ověřování kompatibilní se standardem OpenID Connect** , které umožňují vývojářům ověřovat jakékoli identity Microsoftu, včetně těchto:
  - Pracovní nebo školní účty (zřízené prostřednictvím Azure AD)
  - Osobní účty Microsoft (například Skype, Xbox a Outlook.com)
  - Sociální nebo místní účty (prostřednictvím Azure AD B2C)
- **Open source knihovny**: knihovny Microsoft Authentication Library (MSAL) a podpora dalších knihoven odpovídajících standardům
- **Portál pro správu aplikací**: prostředí pro registraci a konfiguraci vytvořené v Azure Portal společně se všemi ostatními možnostmi správy Azure.
- **Rozhraní API pro konfiguraci aplikací a PowerShell**: umožňuje programovou konfiguraci vašich aplikací prostřednictvím REST API (Microsoft Graph a Azure Active Directory Graph 1,6) a PowerShellu, takže můžete automatizovat úlohy DevOps.
- **Vývojářský obsah**: koncepční a referenční dokumentace, ukázky pro rychlý Start, ukázky kódu, kurzy a návody.

Pro vývojáře nabízí Microsoft Identity Platform bezproblémovou integraci do inovací v rámci identity a prostoru zabezpečení, jako je ověřování bez hesla, krokové ověřování a podmíněný přístup.  Nemusíte tyto funkce implementovat sami: aplikace integrované s Microsoft Identity platformou nativně využívají takové inovace.

S platformou Microsoft Identity můžete napsat kód jenom jednou a oslovit každého uživatele. Můžete vytvořit aplikaci jednou a nechat ji pracovat na celé řadě platforem nebo vytvořit aplikaci, která funguje jako klient a také jako aplikace prostředků (API).

## <a name="getting-started"></a>Začínáme

Práce s identitou nemusí být těžká. 

Podívejte se na [video o platformě Microsoft Identity](identity-videos.md) , kde se seznámíte se základy. 

Vyberte [scénář](authentication-flows-app-scenarios.md) , který vám bude platit – každá cesta scénáře obsahuje rychlý Start a stránku s přehledem, která vám umožní začít pracovat během několika minut:

- [Sestavení jednostránkové aplikace](scenario-spa-overview.md)
- [Vytvoření webové aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md)
- [Sestavení webové aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md)
- [Sestavení chráněného webového rozhraní API](scenario-protected-web-api-overview.md)
- [Sestavení webového rozhraní API, které volá webová rozhraní API](scenario-web-api-call-api-overview.md)
- [Sestavení desktopové aplikace](scenario-desktop-overview.md)
- [Sestavení aplikace typu démon](scenario-daemon-overview.md)
- [Vytvoření mobilní aplikace](scenario-mobile-overview.md)

Následující tabulka popisuje běžné scénáře aplikací ověřování – použijte je jako referenční informace při integraci platformy Microsoft identity s vaší aplikací.

[![scénářů aplikací v platformě Microsoft Identity Platform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o konceptech základního ověřování, doporučujeme začít s těmito tématy:

- [Toky ověřování a scénáře aplikací](authentication-flows-app-scenarios.md)
- [Základy ověřování](authentication-scenarios.md)
- [Aplikační a instanční objekty](app-objects-and-service-principals.md)
- [Publikum](v2-supported-account-types.md)
- [Oprávnění a souhlas](v2-permissions-and-consent.md)
- [Tokeny ID](id-tokens.md) a [přístupové tokeny](access-tokens.md)

Sestavte datově bohatou aplikaci, která volá [Microsoft Graph](https://docs.microsoft.com/graph/overview).

Až budete připraveni ke spuštění aplikace v **produkčním prostředí**, přečtěte si tyto osvědčené postupy:

- [Povolte protokolování](msal-logging.md) v aplikaci.
- Povolte telemetrii ve vaší aplikaci.
- Povolte [proxy a přizpůsobte si klienty http](msal-net-provide-httpclient.md).
- Otestujte integraci podle [kontrolního seznamu integrace Microsoft Identity Platform](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Další informace

Pokud plánujete vytvořit zákaznickou aplikaci, která podepisuje sociální a místní identity, přečtěte si téma [přehled Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
