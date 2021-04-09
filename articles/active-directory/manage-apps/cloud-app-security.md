---
title: Viditelnost a řízení aplikací pomocí Microsoft Cloud App Security
description: Naučte se, jak identifikovat úrovně rizik aplikací, zastavit porušení a nevracení v reálném čase a používat konektory aplikací k využití rozhraní API poskytovatele pro viditelnost a řízení.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 682549b5b99169060437c8c91d465121002edc6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259600"
---
# <a name="cloud-app-visibility-and-control"></a>Řízení a viditelnost cloudových aplikací

Aby bylo možné využívat všechny výhody cloudových aplikací a služeb, musí IT tým najít správnou rovnováhu mezi podporou přístupu a současně zachovat kontrolu nad ochranou důležitých dat. Microsoft Cloud App Security poskytuje bohatou viditelnost, kontrolu nad datovou cestou a propracované analýzy k identifikaci a boji s internetovými hrozbami napříč všemi vašimi cloudových službami Microsoftu a třetích stran.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Zjišťování a správa stínového IT ve vaší síti

Když správci IT požadují, kolik cloudových aplikací si jejich zaměstnanci považují za, průměrně vydávají 30 nebo 40, ve skutečnosti se jedná o více než 1 000 samostatných aplikací používaných zaměstnanci ve vaší organizaci. Stínové IT oddělení vám pomůže zjistit, které aplikace se používají a jaké jsou úrovně rizika. 80 procent zaměstnanců používá neschválené aplikace, které se nikdo nezkontroloval a nemusí být v souladu se zásadami zabezpečení a dodržování předpisů. A vzhledem k tomu, že vaši zaměstnanci mají přístup k vašim prostředkům a aplikacím mimo vaši podnikovou síť, už není nutné mít v bráně firewall pravidla a zásady.

Pomocí Microsoft Cloud App Discovery (funkce Azure Active Directory Premium P1) zjistíte, které aplikace se používají, prozkoumejte rizika těchto aplikací, nakonfigurujete zásady pro identifikaci nových rizikových aplikací a tyto aplikace odhlásíte, aby byly nativně zablokované pomocí proxy serveru nebo zařízení brány firewall.

- Zjišťování a identifikace stínového IT
- Vyhodnocení a analýza
- Správa aplikací
- Rozšířené vytváření sestav pro zjišťování stínových IT
- Řízení schválených aplikací
 
### <a name="learn-more"></a>Další informace

- [Zjišťování a Správa stínového IT v síti ](/cloud-app-security/tutorial-shadow-it)
- [Zjištěné aplikace s Cloud App Security ](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Viditelnost a řízení uživatelské relace 

Na dnešním pracovišti není často dost jasné, co se děje ve vašem cloudovém prostředí, a to i po tom. Chcete ukončit porušení a nevracení se změnami v reálném čase předtím, než zaměstnanci úmyslně nebo neúmyslně umístí vaše data a vaši organizaci na riziko. Společně s Azure Active Directory (Azure AD) Microsoft Cloud App Security poskytují tyto možnosti v holistický a integrovaném prostředí s Řízení podmíněného přístupu k aplikacím. 

Řízení relace používá architekturu reverzního proxy serveru a je jedinečnou integrací s podmíněným přístupem Azure AD. Podmíněný přístup Azure AD umožňuje vynutilit řízení přístupu v aplikacích vaší organizace na základě určitých podmínek. Podmínky definují, kdo (uživatel nebo skupina uživatelů) a co (které cloudové aplikace) a kde kde (která umístění a sítě) jsou aplikovány na zásady podmíněného přístupu. Po určení podmínek můžete uživatele směrovat na Cloud App Security, kde můžete chránit data v reálném čase.  

Pomocí tohoto ovládacího prvku můžete:  
- Řízení stahování souborů
- Monitorování scénářů B2B  
- Řízení přístupu k souborům  
- Chránit dokumenty při stažení  
 
### <a name="learn-more"></a>Další informace

- [Ochrana aplikací pomocí řízení relace v Cloud App Security ](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Pokročilá viditelnost a ovládací prvky aplikace 

Konektory aplikací používají rozhraní API poskytovatelů aplikací k zajištění větší viditelnosti a kontroly pomocí Microsoft Cloud App Security přes aplikace, ke kterým se připojujete. Cloud App Security využívá rozhraní API poskytovaná poskytovatelem cloudu. Každá služba má své vlastní rozhraní a omezení rozhraní API, jako je omezování, omezení rozhraní API, dynamická okna rozhraní API pro časová posunutí a další. Tým Cloud App Security produktů pracoval s těmito službami k optimalizaci používání rozhraní API a zajištění nejlepšího výkonu. V případě, že se přihlížíte k různým omezením, které na své rozhraní API ukládají služby, Cloud App Security motory využívají maximální povolenou kapacitu. Některé operace, jako je například kontrola všech souborů v tenantovi, vyžadují mnoho volání rozhraní API, aby byly rozloženy během delšího období. Očekává, že některé zásady budou spuštěny několik hodin nebo dnů. 
 
### <a name="learn-more"></a>Další informace  

- [Připojení aplikací v Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Další kroky

- [Zjišťování a Správa stínového IT v síti ](/cloud-app-security/tutorial-shadow-it)
- [Zjištěné aplikace s Cloud App Security ](/cloud-app-security/discovered-apps)
- [Ochrana aplikací pomocí řízení relace v Cloud App Security ](/cloud-app-security/proxy-intro-aad)
- [Připojení aplikací v Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)