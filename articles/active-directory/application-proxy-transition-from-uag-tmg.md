---
title: Upgrade na proxy aplikace služby Azure AD | Microsoft Docs
description: Zvolte, které proxy řešení je nejvhodnější, pokud upgradujete z Microsoft Forefront nebo Unified Gateway přístup.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: e6764a0eeb27b8bff24140ff2c12e9e66b06b48b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="compare-remote-access-solutions"></a>Porovnání řešení vzdáleného přístupu

Azure Proxy aplikace Active Directory je jedním ze dvou řešení vzdáleného přístupu, které nabízí Microsoft. Druhá je Proxy webových aplikací, na místní verzi. Tyto dvě řešení nahradit starší produkty, které nabízí Microsoft: Microsoft Forefront Threat Management Gateway (TMG) a Unified brány přístup (UAG). Tento článek vám pomůže porozumět jak porovnat tyto čtyři řešení k sobě navzájem. Pro těch, které používáte stále nepoužívané řešení TMG nebo UAG používejte pro plánování migrace do jednoho z Proxy aplikace v tomto článku. 


## <a name="feature-comparison"></a>Porovnání funkcí

Pomocí této tabulky pochopit, jak porovnat Threat Management Gateway (TMG), Unified brány přístup (UAG), Proxy webové aplikace (WAP) a Azure AD Application Proxy (AP) k sobě navzájem.

| Funkce | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Ověřování pomocí certifikátu | Ano | Ano | - | - |
| Selektivně publikovat prohlížečových aplikací | Ano | Ano | Ano | Ano |
| Předběžné ověření a jeden přihlášení | Ano | Ano | Ano | Ano | 
| Vrstvy 2 nebo 3 brány firewall | Ano | Ano | - | - |
| Předávání funkce proxy serveru | Ano | - | - | - |
| Funkce sítě VPN | Ano | Ano | - | - |
| Podpora bohaté protokolu | - | Ano | Ano, pokud systém přes protokol HTTP | Ano, pokud je spuštěn prostřednictvím protokolu HTTP nebo prostřednictvím služby Brána vzdálené plochy |
| Slouží jako server proxy služby AD FS | - | Ano | Ano | - |
| Jeden portál pro přístup k aplikaci | - | Ano | - | Ano |
| Překlad odkaz text odpovědi | Ano | Ano | - | Ano | 
| Ověřování se záhlavími | - | Ano | - | Ano, s PingAccess | 
| Cloudového škálovatelného zabezpečení | - | - | - | Ano | 
| Podmíněný přístup | - | Ano | - | Ano |
| Žádné součásti v demilitarizovaná zóna (DMZ) | - | - | - | Ano |
| Žádná příchozí připojení | - | - | - | Ano |

Pro většinu scénářů doporučujeme, abyste aplikaci Azure AD jako moderní řešení. Proxy webových aplikací je pouze upřednostňované v scénáře, které vyžadují připojení proxy serveru pro službu AD FS a nemůžete použít vlastní domény ve službě Azure Active Directory. 

Azure AD Application Proxy nabízí jedinečné výhody ve srovnání s podobné produkty, včetně:

- Rozšíření Azure AD k místním prostředkům
   - Cloudového škálovatelného zabezpečení a ochrana
   - Funkce, například podmíněného přístupu a vícefaktorového ověřování se dají snadno povolit
- Žádné componenet v demilitarizovaná zóna
- Žádná příchozí připojení, vyžaduje
- Jeden přístupový panel vaši uživatelé můžete přejít k pro všechny své aplikace, včetně O365, Azure AD integrovaných aplikací SaaS a místní webové aplikace. 


## <a name="next-steps"></a>Další postup

- [Poskytnout zabezpečený vzdálený přístup k místním aplikacím pomocí aplikace Azure AD.](manage-apps/application-proxy.md)
- [Přechod z Forefront TMG a UAG na Proxy aplikace](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).
