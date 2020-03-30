---
title: Viditelnost a řízení aplikací pomocí Microsoft Cloud App Security
description: Naučte se, jak identifikovat úrovně rizika aplikací, zastavit porušování a nevracení v reálném čase a používat konektory aplikací k využití rozhraní API poskytovatele pro viditelnost a zásady správného řízení.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 02/03/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e1a06acddae0973d5dbc7e64212026149f217
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77069739"
---
# <a name="cloud-app-visibility-and-control"></a>Řízení a viditelnost cloudových aplikací

Chcete-li získat všechny výhody cloudových aplikací a služeb, musí IT tým najít správnou rovnováhu mezi podporou přístupu při zachování kontroly a ochranou důležitých dat. Microsoft Cloud App Security poskytuje bohatou viditelnost, kontrolu nad cestováním dat a sofistikované analýzy pro identifikaci a boj proti kybernetickým hrozbám ve všech cloudových službách Microsoftu a třetích stran.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Zjišťování a správa stínového IT ve vaší síti

Když se správci IT získaní, kolik cloudových aplikací podle nich jejich zaměstnanci používají, v průměru říkají 30 nebo 40, když ve skutečnosti je průměr více než 1 000 samostatných aplikací, které zaměstnanci ve vaší organizaci používají. Shadow IT vám pomůže zjistit, které aplikace jsou používány a jaká je vaše úroveň rizika. Osmdesát procent zaměstnanců používá neschválené aplikace, které nikdo nezkontroloval a nemusí být v souladu s vašimi zásadami zabezpečení a dodržování předpisů. A protože vaši zaměstnanci mají přístup k vašim prostředkům a aplikacím mimo vaši podnikovou síť, nestačí mít pravidla a zásady ve vašich firewallech.

Pomocí zjišťování aplikací Microsoft Cloud (funkce Azure Active Directory Premium P1) můžete zjistit, které aplikace se používají, prozkoumat riziko těchto aplikací, nakonfigurovat zásady pro identifikaci nových rizikových aplikací a zrušit jejich schválení, abyste je nativně zablokovali pomocí proxy nebo firewall.

- Zjišťování a identifikace stínového IT
- Vyhodnocení a analýza
- Správa aplikací
- Pokročilé reportování zjišťování IT stínů
- Kontrola schválených aplikací
 
### <a name="learn-more"></a>Další informace

- [Zjišťování a správa stínového IT ve vaší síti](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Zjištěné aplikace s cloudovým zabezpečením aplikací](https://docs.microsoft.com/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Viditelnost a řízení relace uživatele 

Na dnešním pracovišti často nestačí vědět, co se děje ve vašem cloudovém prostředí. Chcete zastavit porušování a úniky v reálném čase dříve, než zaměstnanci úmyslně nebo neúmyslně ohrozí vaše data a vaši organizaci. Microsoft Cloud App Security společně s Azure Active Directory (Azure AD) poskytuje tyto funkce v komplexním a integrovaném prostředí s řízením aplikací podmíněného přístupu. 

Řízení relací používá architekturu reverzního proxy serveru a je jedinečně integrované s podmíněným přístupem Azure AD. Podmíněný přístup Azure AD umožňuje vynutit ovládací prvky přístupu v aplikacích vaší organizace na základě určitých podmínek. Podmínky definují, kdo (uživatel nebo skupina uživatelů) a co (které cloudové aplikace) a kde (která umístění a sítě) se zásady podmíněného přístupu použijí. Po určení podmínek můžete uživatele směrovat do cloudového zabezpečení aplikací, kde můžete chránit data v reálném čase.  

S tímto ovládacím prvkem můžete:  
- Řízení stahování souborů
- Monitorování scénářů B2B  
- Řízení přístupu k souborům  
- Ochrana dokumentů při stahování  
 
### <a name="learn-more"></a>Další informace

- [Ochrana aplikací pomocí session control v cloudovém zabezpečení aplikací](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Pokročilá viditelnost a ovládací prvky aplikací 

Konektory aplikací používají rozhraní API poskytovatelů aplikací k umožnění lepší viditelnosti a kontroly ze strany Microsoft Cloud App Security nad aplikacemi, ke kterým se připojujete. Cloud App Security využívá api poskytované poskytovatelem cloudu. Každá služba má vlastní rozhraní a omezení rozhraní API, jako je omezení, omezení rozhraní API, dynamická okna rozhraní API s časovým posunem a další. Produktový tým Cloud App Security pracoval s těmito službami na optimalizaci využití api a zajištění nejlepšího výkonu. S přihlédnutím k různým omezením, která služby ukládají svým platformám API, využívají moduly Cloud App Security maximální povolenou kapacitu. Některé operace, jako je například prohledávání všech souborů v tenantovi, vyžadují mnoho volání rozhraní API, takže jsou rozloženy na delší dobu. Očekávejte, že některé zásady budou spuštěny několik hodin nebo dní. 
 
### <a name="learn-more"></a>Další informace  

- [Připojení aplikací v cloudovém zabezpečení aplikací](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Další kroky

- [Zjišťování a správa stínového IT ve vaší síti](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Zjištěné aplikace s cloudovým zabezpečením aplikací](https://docs.microsoft.com/cloud-app-security/discovered-apps)
- [Ochrana aplikací pomocí session control v cloudovém zabezpečení aplikací](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Připojení aplikací v cloudovém zabezpečení aplikací](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
