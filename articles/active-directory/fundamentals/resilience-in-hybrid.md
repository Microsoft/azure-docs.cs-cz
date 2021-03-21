---
title: Sestavujte pružnější hybridní ověřování v Azure Active Directory
description: Příručka pro architekty a správce IT při vytváření odolné hybridní infrastruktury
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de01788c01d4d6dedd9563faaaac07bff30bbd97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724755"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>Odolnost sestavení v hybridní architektuře

Hybridní ověřování umožňuje uživatelům přístup k prostředkům v cloudu s místními identitami, které jsou na místním serveru. Hybridní infrastruktura zahrnuje cloudové i místní součásti.

* Mezi cloudové komponenty patří Azure AD, prostředky a služby Azure, cloudové aplikace vaší organizace a SaaS aplikace.

* Mezi místní součásti patří místní aplikace, prostředky, jako jsou databáze SQL, a zprostředkovatel identity, jako je služba Windows Server Active Directory. 

> [!IMPORTANT]
> Při plánování odolnosti v hybridní infrastruktuře je klíč k minimalizaci závislostí a jediného bodu selhání. 

Microsoft nabízí tři mechanismy pro hybridní ověřování. Možnosti jsou uvedeny v pořadí podle odolnosti. Pokud je to možné, doporučujeme implementovat synchronizaci hodnot hash hesel.

* [Synchronizace hodnot hash hesel](../hybrid/whatis-phs.md) (kosmetice) používá Azure AD Connect k synchronizaci identity a hodnoty hash hesla k Azure AD a umožňuje uživatelům přihlašovat se ke cloudovým prostředkům pomocí místního hesla v místním prostředí. KOSMETICE má místní závislosti pouze pro synchronizaci, nikoli pro ověřování.

* [Předávací ověřování](../hybrid/how-to-connect-pta.md) (PTA) přesměruje uživatele do služby Azure AD, aby se přihlásili. Pak se uživatelské jméno a heslo ověřují proti službě Active Directory místně prostřednictvím agenta, který je nasazený v podnikové síti. PTA má místní nároky na své agenty Azure AD PTA, kteří se nacházejí na místních serverech.

* [Federační](../hybrid/whatis-fed.md) zákazníci nasadí federační službu, například AD FS, a pak Azure AD ověří kontrolní výraz SAML vyprodukovaný federační službou. Federace má nejvyšší závislost na místní infrastruktuře, a proto je více bodů selhání. 

   
Ve vaší organizaci možná používáte jednu nebo více těchto metod. Další informace najdete v tématu [Volba správné metody ověřování pro řešení hybridní identity Azure AD](../hybrid/choose-ad-authn.md). Tento článek obsahuje rozhodovací strom, který vám může pomáhat při rozhodování o metodologii.

## <a name="password-hash-synchronization"></a>Synchronizace hodnot hash hesel

Nejjednodušší a nejbezpečnější možnost hybridního ověřování pro Azure AD je [synchronizace hodnot hash hesel](../hybrid/whatis-phs.md) , která při zpracování požadavků na ověření nemá žádnou místní infrastrukturu identity. Jakmile budou identity s hodnotami hash hesel synchronizovány do služby Azure AD, uživatelé se můžou ověřit u cloudových prostředků bez závislosti na místních komponentách identity. 

![Diagram architektury KOSMETICE](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

Pokud zvolíte tuto možnost ověřování, nebudete mít k dispozici přerušení, když budou místní součásti identity nedostupné. K místnímu přerušení může dojít z mnoha důvodů, včetně selhání hardwaru, výpadků výpadků, přírodních havárií a útoků malwaru. 

### <a name="how-do-i-implement-phs"></a>Návody implementovat KOSMETICE?

Chcete-li implementovat KOSMETICE, přečtěte si následující zdroje informací:

* [Implementace synchronizace hodnot hash hesel pomocí Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md)

* [Povolení synchronizace hodnoty hash hesel](../hybrid/how-to-connect-password-hash-synchronization.md)

Pokud vaše požadavky jsou takové, že nemůžete používat KOSMETICE, použijte předávací ověřování.

## <a name="pass-through-authentication"></a>Předávací ověřování

Předávací ověřování má závislost na ověřovacích agentech, které jsou umístěné místně na serverech. Mezi Azure AD a místními PTA agenty se nachází trvalé připojení nebo Service Bus. Brána firewall, servery hostující agenty ověřování a místní služba Windows Server Active Directory (nebo jiný zprostředkovatel identity) jsou všechny možné body selhání. 

![Diagram architektury PTA](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>Návody implementovat PTA?

Chcete-li implementovat předávací ověřování, přečtěte si následující zdroje informací.

* [Způsob, jakým předávací ověřování funguje](../hybrid/how-to-connect-pta-how-it-works.md)

* [Podrobné informace o zabezpečení předávacího ověřování](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Instalace předávacího ověřování Azure AD](../hybrid/how-to-connect-pta-quick-start.md)

* Pokud používáte PTA, definujte [topologii s vysokou dostupností](../hybrid/how-to-connect-pta-quick-start.md).

 ## <a name="federation"></a>metadata

Federace zahrnuje vytvoření vztahu důvěryhodnosti mezi Azure AD a federační službou, což zahrnuje výměnu koncových bodů, podpisových certifikátů tokenů a dalších metadat. Když přijde požadavek do služby Azure AD, načte konfiguraci a přesměruje uživatele do nakonfigurovaných koncových bodů. V tomto okamžiku uživatel komunikuje se službou FS (Federation Service), která vydává kontrolní výraz SAML ověřený službou Azure AD. 

Následující diagram znázorňuje topologii podnikové Active Directory Federation Services (AD FS) (AD FS), nasazení, které zahrnuje redundantní federační a proxy servery webových aplikací v různých místních datových centrech. Tato konfigurace spoléhá na komponenty infrastruktury podnikové sítě, jako je DNS, Vyrovnávání zatížení sítě s možnostmi geografického spřažení, branami firewall atd. Všechny místní komponenty a připojení jsou náchylné k selhání. Další informace najdete v [dokumentaci k plánování kapacity AD FS](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) .

> [!NOTE]
>  Federace má nejvyšší počet místních závislostí, a proto nejvíce možných bodů selhání. I když tento diagram zobrazuje AD FS, další místní zprostředkovatelé identit podléhají podobným hlediskům návrhu, aby dosáhli vysoké dostupnosti, škálovatelnosti a převzetí služeb při selhání.

![Diagram architektury federace](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>Návody implementovat federaci?

Pokud implementujete strategii federovaného ověřování nebo chcete, aby byla pružnější, podívejte se na následující zdroje informací.

* [Co je federované ověřování](../hybrid/whatis-fed.md)

* [Jak funguje federace](../hybrid/how-to-connect-fed-whatis.md)

* [Seznam kompatibilit pro federaci Azure AD](../hybrid/how-to-connect-fed-compatibility.md)

* Dodržujte [AD FS dokumentaci pro plánování kapacity](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) .

* [Nasazení AD FS v Azure IaaS](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* [Povolení kosmetice](../hybrid/tutorial-phs-backup.md) spolu s vaší federací

## <a name="next-steps"></a>Další kroky
Prostředky odolnosti pro správce a architekty
 
* [Odolnost sestavení se správou přihlašovacích údajů](resilience-in-credentials.md)

* [Odolnost sestavení se stavy zařízení](resilience-with-device-states.md)

* [Odolnost sestavení pomocí vyhodnocení průběžného přístupu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Odolnost sestavení při ověřování externích uživatelů](resilience-b2b-authentication.md)

* [Odolnost sestavení v přístupu aplikace s proxy aplikací](resilience-on-premises-access.md)

Prostředky odolnosti pro vývojáře

* [Sestavování odolnosti IAM ve vašich aplikacích](resilience-app-development-overview.md)

* [Odolnost sestavení v systémech CIAM](resilience-b2c.md)