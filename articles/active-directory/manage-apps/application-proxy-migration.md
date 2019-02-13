---
title: Upgrade na Azure AD Application Proxy | Dokumentace Microsoftu
description: Vyberte řešení, které proxy je vhodná, pokud provádíte upgrade z Microsoft Forefront nebo sjednocené brány přístup.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2017
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15e831bbcb956401149d8c33fce4d00a3be5a11d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170869"
---
# <a name="compare-remote-access-solutions"></a>Porovnání řešení vzdáleného přístupu

Azure Active Directory Application Proxy je jednou ze dvou řešení vzdáleného přístupu, které nabízí Microsoft. Druhá je Proxy webových aplikací, na místní verzi. Tyto dvě řešení nahradit starší produkty, které Microsoft nabízí: Microsoft Forefront Threat Management Gateway (TMG) a jednotný přístup brány (UAG). Pomocí tohoto článku můžete pochopit, jak porovnat tyto čtyři řešení k sobě navzájem. Pro ty z vás pořád používá zastaralý TMG nebo UAG řešení použijte tento článek abyste mohli naplánovat vaši migraci do jednoho proxy aplikací. 


## <a name="feature-comparison"></a>Porovnání funkcí

Tato tabulka slouží k pochopení, jak porovnat Threat Management Gateway (TMG), sjednocené brány přístup (UAG), Proxy webových aplikací (WAP) a Azure AD Application proxy server (AP) k sobě navzájem.

| Funkce | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Ověřování certifikátem | Ano | Ano | - | - |
| Selektivně publikovat prohlížečových aplikací | Ano | Ano | Ano | Ano |
| Předběžné ověřování a jednotné přihlašování | Ano | Ano | Ano | Ano | 
| Vrstvy 2 nebo 3 brány firewall | Ano | Ano | - | - |
| Předat možnosti proxy | Ano | - | - | - |
| Funkce sítě VPN | Ano | Ano | - | - |
| Podpora bohaté možnosti protokolu | - | Ano | Ano, pokud spuštění přes protokol HTTP | Ano, pokud používáte přes protokol HTTP nebo prostřednictvím brány vzdálené plochy |
| Slouží jako server proxy služby ADFS | - | Ano | Ano | - |
| Jeden portál pro přístup k aplikaci | - | Ano | - | Ano |
| Překlad odkazu tělo odpovědi | Ano | Ano | - | Ano | 
| Ověřování pomocí hlavičky | - | Ano | - | Ano, pingaccessu | 
| Zabezpečení v cloudovém měřítku | - | - | - | Ano | 
| Podmíněný přístup | - | Ano | - | Ano |
| Žádné součásti v demilitarizovaná zóna (DMZ) | - | - | - | Ano |
| Žádná příchozí připojení | - | - | - | Ano |

Pro většinu scénářů doporučujeme aplikace Azure AD jako moderních řešení. Proxy webových aplikací je pouze upřednostňované ve scénářích, které vyžadují proxy server pro službu AD FS a nemůžete použít vlastní domény ve službě Azure Active Directory. 

Proxy aplikací Azure AD nabízí jedinečné výhody ve srovnání s podobné produkty Microsoftu, včetně:

- Rozšíření Azure AD a místní prostředky
   - Zabezpečení cloudových a ochrany
   - Funkce, jako je podmíněný přístup a vícefaktorového ověřování se dají snadno povolit
- Žádné součásti v demilitarizovaná zóna
- Žádná příchozí připojení vyžaduje
- Jeden přístupovém panelu, že vaši uživatelé lze použít pro všechny své aplikace, včetně O365, aplikacím SaaS integrované služby Azure AD a místní webové aplikace. 


## <a name="next-steps"></a>Další postup

- [Pomocí aplikace Azure AD poskytovat zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md)
- [Přechod z Forefrontu TMG a mu umožní Proxy aplikací](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).
