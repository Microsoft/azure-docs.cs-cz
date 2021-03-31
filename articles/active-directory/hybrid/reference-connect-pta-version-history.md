---
title: 'Předávací ověřování Azure AD: Historie vydání verze | Microsoft Docs'
description: V tomto článku jsou uvedené všechny verze agenta předávacího ověřování Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 854931273ffebd653615a44dbfa9ce1b846655c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333422"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Ověřovací agent předávacího ověřování Azure AD: Historie verzí 
 
Agenti místně nainstalovanou k povolení předávacího ověřování se pravidelně aktualizují, aby poskytovaly nové funkce. Tento článek obsahuje seznam verzí a funkcí, které se přidávají při zavedení nových funkcí. Agenti předávacího ověřování se automaticky aktualizují při vydání nové verze. 

Tady jsou Příbuzná témata: 

- [Přihlášení uživatele pomocí předávacího ověřování Azure AD](how-to-connect-pta.md) 
- [Instalace ověřovacího agenta předávacího ověřování Azure AD](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>Stav vydaných verzí: 
04/09/2020: vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

- Přidání podpory pro cílení cloudových prostředí při instalaci. Sadu prostředků můžete připnout do daného cloudového prostředí.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Stav verze 
1/22/2019: vydáno ke stažení  
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 
- Přidání podpory Service Bus spolehlivých kanálů pro přidání další vrstvy odolnosti připojení pro odchozí připojení 
- Vynutilit TLS 1,2 během registrace agenta 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Stav verze 
4/10/2018: vydáno ke stažení  
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 
- Podpora připojení webového soketu 
- Nastavte TLS 1,2 jako výchozí protokol pro agenta. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Stav verze 
1/31/2018: vydáno ke stažení  
### <a name="fixed-issues"></a>Opravené problémy 
- Opravili jsme chybu, která způsobila nevracení paměti v agentovi. 
- Aktualizace verze Azure Service Bus, která zahrnuje opravu chyby pro problémy s časovým limitem konektoru. 
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 
- Přidání podpory pro připojení založená na protokolu WebSocket mezi agentem a službami Azure AD za účelem zvýšení odolnosti připojení

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Stav verze 
11/25/2017: vydáno ke stažení  
### <a name="fixed-issues"></a>Opravené problémy 
- Opravené chyby související s mezipamětí DNS pro výchozí scénáře proxy 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Stav verze 
10/17/2017: vydáno ke stažení  
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 
- Přidání funkce mezipaměti DNS pro odchozí připojení, aby se přidala odolnost proti chybám DNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Stav verze 
08/31/2017: vydáno ke stažení  
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 
- GA verze agenta předávacího ověřování Azure AD 

## <a name="next-steps"></a>Další kroky

- [Přihlašování uživatelů s využitím předávacího ověřování služby Azure Active Directory](how-to-connect-pta.md)
