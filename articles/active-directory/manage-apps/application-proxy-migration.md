---
title: Upgradovat na Azure Proxy aplikací služby AD | Microsoft Docs
description: Pokud upgradujete z Microsoft Forefront nebo Unified Access Gateway, vyberte, které řešení proxy je nejvhodnější.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1ffb5ec0b39c5331afbc55d99268d9a3997e62
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764447"
---
# <a name="compare-remote-access-solutions"></a>Porovnání řešení vzdáleného přístupu

Proxy aplikací služby Azure Active Directory je jedno ze dvou řešení vzdáleného přístupu, které Microsoft nabízí. Druhý je proxy webových aplikací, místní verze. Tato dvě řešení nahrazují předchozí produkty, které nabízí Microsoft: Microsoft Forefront Threat Management Gateway (TMG) a Unified Access Gateway (UAG). V tomto článku se seznámíte s tím, jak se tato čtyři řešení vzájemně porovnávají. Pro ty, kteří stále používají zastaralá řešení TMG nebo UAG, použijte tento článek, který vám pomohou naplánovat migraci na jeden z proxy aplikací. 


## <a name="feature-comparison"></a>Porovnání funkcí

Tato tabulka vám pomůže pochopit, jak brány pro správu hrozeb (TMG), Unified Access Gateway (UAG), proxy webových aplikací (WAP) a Azure Proxy aplikací služby AD (AP) jsou vzájemně porovnatelné.

| Funkce | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Ověření certifikátu | Ano | Ano | - | - |
| Selektivní publikování aplikací v prohlížeči | Ano | Ano | Ano | Ano |
| Předběžné ověření a jednotné přihlašování | Ano | Ano | Ano | Ano | 
| Brána firewall vrstvy 2/3 | Ano | Ano | - | - |
| Možnosti předávaného proxy serveru | Yes | - | - | - |
| Možnosti sítě VPN | Ano | Ano | - | - |
| Bohatá podpora protokolů | - | Yes | Ano, pokud se používá přes protokol HTTP | Ano, pokud je spuštěn přes protokol HTTP nebo prostřednictvím Brána vzdálené plochy |
| Slouží jako proxy server ADFS | - | Ano | Ano | - |
| Jeden portál pro přístup k aplikaci | - | Ano | - | Ano |
| Překlad odkazu na text odpovědi | Ano | Ano | - | Ano | 
| Ověřování pomocí hlaviček | - | Yes | - | Ano, s PingAccess | 
| Cloud – škálování zabezpečení | - | - | - | Yes | 
| Podmíněný přístup | - | Ano | - | Ano |
| Žádné součásti v zóně demilitarizovaná (DMZ) | - | - | - | Yes |
| Žádná příchozí připojení | - | - | - | Yes |

Pro většinu scénářů doporučujeme Azure Proxy aplikací služby AD jako moderní řešení. Proxy webových aplikací je preferován pouze ve scénářích, které vyžadují proxy server pro AD FS a nemůžete použít vlastní domény v Azure Active Directory. 

V porovnání s podobnými produkty nabízí Azure Proxy aplikací služby AD jedinečné výhody, včetně:

- Rozšíření Azure AD na místní prostředky
   - Zabezpečení a ochrana v cloudovém měřítku
   - Funkce, jako je podmíněný přístup a Multi-Factor Authentication, se snadno povolují.
- V zóně demilitarizovaná nejsou žádné součásti.
- Nejsou požadována žádná příchozí připojení.
- Jeden přístupový panel, na který můžou vaši uživatelé přejít pro všechny své aplikace, včetně O365, integrovaných aplikací SaaS Azure AD a vašich místních webových aplikací. 


## <a name="next-steps"></a>Další kroky

- [Použití aplikace Azure AD k poskytování zabezpečeného vzdáleného přístupu k místním aplikacím](application-proxy.md)
