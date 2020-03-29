---
title: Upgrade na proxy aplikace Azure AD | Dokumenty společnosti Microsoft
description: Zvolte, které proxy řešení je nejlepší, pokud upgradujete z Microsoft Forefront nebo Unified Access Gateway.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4790dc7ebeeee3407e89bcf38d7e3f25699ed328
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108414"
---
# <a name="compare-remote-access-solutions"></a>Porovnání řešení vzdáleného přístupu

Azure Active Directory Application Proxy je jedním ze dvou řešení vzdáleného přístupu, které Microsoft nabízí. Druhý je proxy webové aplikace, místní verze. Tato dvě řešení nahrazují dřívější produkty, které společnost Microsoft nabízela: Microsoft Forefront Threat Management Gateway (TMG) a Unified Access Gateway (UAG). Pomocí tohoto článku pochopit, jak tyto čtyři řešení ve vzájemném porovnání. Pro ty z vás, kteří stále používají zastaralé řešení TMG nebo UAG, použijte tento článek, který vám pomůže naplánovat migraci na jeden z proxy aplikací. 


## <a name="feature-comparison"></a>Porovnání funkcí

V této tabulce můžete pochopit, jak se brána pro správu hrozeb (TMG), sjednocená přístupová brána (UAG), proxy webové aplikace (WAP) a proxy aplikace Azure AD (AP) vzájemně porovnávají.

| Funkce | Tmg | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Ověření certifikátu | Ano | Ano | - | - |
| Selektivně publikovat aplikace prohlížeče | Ano | Ano | Ano | Ano |
| Předběžné ověřování a jednotné přihlašování | Ano | Ano | Ano | Ano | 
| Brána firewall vrstvy 2/3 | Ano | Ano | - | - |
| Možnosti předávání proxy serveru | Ano | - | - | - |
| Možnosti sítě VPN | Ano | Ano | - | - |
| Podpora bohatého protokolu | - | Ano | Ano, pokud běží přes HTTP | Ano, pokud běžíte přes protokol HTTP nebo přes bránu vzdálené plochy |
| Slouží jako proxy server ADFS | - | Ano | Ano | - |
| Jeden portál pro přístup k aplikacím | - | Ano | - | Ano |
| Překlad odkazu na tělo odpovědi | Ano | Ano | - | Ano | 
| Ověřování pomocí záhlaví | - | Ano | - | Ano, s pingaccessem | 
| Zabezpečení v cloudovém měřítku | - | - | - | Ano | 
| Podmíněný přístup | - | Ano | - | Ano |
| Žádné součásti v demilitarizované zóně (DMZ) | - | - | - | Ano |
| Žádná příchozí připojení | - | - | - | Ano |

Pro většinu scénářů doporučujeme jako moderní řešení proxy aplikace Azure AD. Proxy webové aplikace je upřednostňovaný jenom ve scénářích, které vyžadují proxy server pro službu AD FS, a vlastní domény ve službě Azure Active Directory nelze používat. 

Azure AD Application Proxy nabízí jedinečné výhody ve srovnání s podobnými produkty, včetně:

- Rozšíření Azure AD na místní prostředky
   - Zabezpečení a ochrana v cloudovém měřítku
   - Funkce, jako je podmíněný přístup a vícefaktorové ověřování, lze snadno povolit
- Žádné součásti v demilitarizované zóně
- Není vyžadováno žádné příchozí připojení.
- Jeden přístupový panel, na který můžou uživatelé přejít pro všechny své aplikace, včetně O365, integrovaných aplikací SaaS azure a místních webových aplikací. 


## <a name="next-steps"></a>Další kroky

- [Použití aplikace Azure AD k zajištění zabezpečeného vzdáleného přístupu k místním aplikacím](application-proxy.md)
