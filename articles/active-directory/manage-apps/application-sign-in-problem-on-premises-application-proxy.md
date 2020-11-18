---
title: Potíže s přihlášením k místní aplikaci pomocí proxy aplikace Azure AD | Microsoft Docs
description: Řešení běžných problémů s tím, že se nemůžete přihlásit k místní aplikaci integrované s Azure AD pomocí Proxy aplikací služby AD Azure
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7abf61d6b10e22a434c42097bf42183c7b39e352
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654945"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Problémy s přihlašováním k místní aplikaci pomocí proxy aplikací služby Azure AD

Pokud máte problémy s přihlášením do místní aplikace, můžete zkusit problém vyřešit podle následujících pokynů.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>Můžu aplikaci načíst, ale něco na stránce zřejmě není v pořádku

Následující dokumenty vám mohou pomoci při řešení některých nejběžnějších problémů v této kategorii.

  * [Můžu se k aplikaci dostat, ale stránka aplikace se nezobrazuje správně](application-proxy-page-appearance-broken-problem.md)
  * [Můžu se k aplikaci dostat, ale načítá se příliš dlouho](application-proxy-page-load-speed-problem.md)
  * [Můžu se k aplikaci dostat, ale odkazy na stránce aplikace nefungují](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>Mám problém s připojením k aplikaci
  Následující dokumenty vám mohou pomoci při řešení některých nejběžnějších problémů v této kategorii.
  * [Nevím, které porty pro aplikaci otevřít](application-proxy-add-on-premises-application.md)
  * [Vyskytl se problém, protože ve skupině konektorů pro aplikaci nebyl žádný funkční konektor](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Mám problém s konfigurací Proxy aplikací služby AD na portálu pro správu
  Následující dokumenty vám mohou pomoci při řešení některých nejběžnějších problémů v této kategorii.
  * [Mám potíže s konfigurací aplikace aplikačního proxy serveru](application-proxy-config-how-to.md)
  * [Nevím, jak nakonfigurovat jednotné přihlašování k aplikaci aplikačního proxy serveru](application-proxy-config-sso-how-to.md)
  * [Vyskytl se problém při vytváření aplikace na portálu pro správu](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Mám problém s nastavením back-endového ověřování pro aplikaci
  Následující dokumenty vám mohou pomoci při řešení některých nejběžnějších problémů v této kategorii.
  * [Nevím, jak nakonfigurovat omezené delegování Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [Nevím, jak nakonfigurovat aplikaci pro používání PingAccessu](/azure/active-directory/manage-apps/application-proxy-ping-access-publishing-guide)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Mám problém s přihlášením k aplikaci
  Následující dokumenty vám mohou pomoci při řešení některých nejběžnějších problémů v této kategorii.
  * [Zobrazuje se chyba „Není přístup k podnikové aplikaci“](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Mám problém s konektorem agenta Proxy aplikací
  Následující dokumenty vám mohou pomoci při řešení některých nejběžnějších problémů v této kategorii.
  * [Mám problémy s instalací konektoru agenta proxy aplikací](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>Další kroky
[Jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md)