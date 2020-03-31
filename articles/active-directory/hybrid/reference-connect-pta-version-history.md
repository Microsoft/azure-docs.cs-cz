---
title: 'Předávací ověřování Azure AD: Historie verzí verze | Dokumenty společnosti Microsoft'
description: Tento článek obsahuje seznam všech verzí agenta předávacího ověřování Azure AD
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c5d0efe8e662544dc69356c6b17dd7eca6f3a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786448"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Agent předávacího ověřování Azure AD: Historie verzí vydání 
 
Agenti nainstalované v místním prostředí, které umožňují předávací ověřování, jsou pravidelně aktualizováni, aby poskytovali nové funkce. Tento článek uvádí verze a funkce, které jsou přidány při zavedení nové funkce. Předávací ověřování agenti jsou aktualizovány automaticky při vydání nové verze. 

Zde jsou související témata: 

- [Přihlášení uživatele pomocí předávacího ověřování Azure AD](how-to-connect-pta.md) 
- [Instalace agenta předávacího ověřování Azure AD](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Stav verze 
1/22/2019: Vydáno ke stažení  
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 
- Přidána podpora spolehlivých kanálů Service Bus pro přidání další vrstvy odolnosti připojení pro odchozí připojení 
- Vynutit TLS 1.2 během registrace agenta 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Stav verze 
10.4.2018: Vydáno ke stažení  
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 
- Podpora připojení webového soketu 
- Nastavení protokolu TLS 1.2 jako výchozího protokolu agenta 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Stav verze 
1/31/2018: Vydáno ke stažení  
### <a name="fixed-issues"></a>Oprava potíží 

- Opravena chyba, která způsobovala některé nevracení paměti v agentovi. 
- Byla aktualizována verze služby Azure Service Bus, která zahrnuje opravu chyb pro problémy s časovým časem konektoru. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Stav verze 
11/26/2017: Vydáno ke stažení  
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 
- Přidána podpora pro připojení založená na websocketu mezi agentem a službami Azure AD pro zlepšení odolnosti proti připojení 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Stav verze 
11/25/2017: Vydáno ke stažení  
### <a name="fixed-issues"></a>Oprava potíží 
- Opraveny chyby související s mezipamětí DNS pro výchozí scénáře proxy serveru 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Stav verze 
10/17/2017: Vydáno ke stažení  
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 
- Přidána funkce mezipaměti DNS pro odchozí připojení pro přidání odolnosti proti chybám DNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Stav verze 
08/31/2017: Vydáno ke stažení  
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 
- Verze GA předávacího agenta Azure AD 

## <a name="next-steps"></a>Další kroky

- [Přihlašování uživatelů s využitím předávacího ověřování služby Azure Active Directory](how-to-connect-pta.md)