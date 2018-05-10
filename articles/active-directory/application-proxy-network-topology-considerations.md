---
title: Aspekty topologie sítě, při použití aplikace Proxy Azure Active Directory | Microsoft Docs
description: Popisuje aspekty topologie sítě, při použití Azure AD Application Proxy.
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
ms.date: 07/28/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5670414cd6c58743d30246681594526544cb128b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Aspekty topologie sítě při použití aplikace Proxy Azure Active Directory

Tento článek vysvětluje aspekty topologie sítě, při použití aplikace Proxy Azure Active Directory (Azure AD) pro publikování a vzdálený přístup k vaší aplikace.

## <a name="traffic-flow"></a>Tok přenosů dat

Při publikování aplikace prostřednictvím proxy aplikace služby Azure AD, prochází provoz z uživatelů k aplikacím tři připojení:

1. Připojení uživatele k Azure AD Application Proxy veřejný koncový bod služby v Azure
2. Služba Proxy aplikace připojí ke konektoru Proxy aplikace
3. Konektor Proxy aplikace se připojuje k cílové aplikace

![Diagram zobrazuje tok přenosů dat od uživatele do cílové aplikace](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Umístění klienta a Proxy aplikace služby

Při registraci pro klienta služby Azure AD oblasti klienta služby je určen podle země, které zadáte. Když povolíte Proxy aplikace, instance služby Proxy aplikace vašeho klienta jsou zvolené nebo vytvořit ve stejné oblasti jako váš klient Azure AD nebo nejbližší oblast na ni.

Pokud je klient Azure AD oblast Evropské unie (EU), všechny konektory Proxy aplikací pomocí instance služby v datových centrech Azure v EU. Při publikování aplikace přístup vašich uživatelů jejich provoz prochází instance služby Proxy aplikace v tomto umístění.

## <a name="considerations-for-reducing-latency"></a>Důležité informace týkající se sníží se latence

Všechna řešení pro proxy představovat latence na připojení k síti. Bez ohledu na to, které proxy server nebo řešení sítě VPN zvolíte jako řešení pro vzdálený přístup vždy obsahuje sadu servery umožňující připojení k uvnitř firemní sítě.

Organizace obvykle obsahovat koncové body serveru v jejich hraniční síti. S Azure AD Application Proxy ale přenosy dat v cloudu pomocí služby proxy server při konektory jsou umístěné ve vaší podnikové síti. Je potřeba žádné hraniční síť.

Další oddíly obsahují další návrhy vám pomůže zmenšit latence ještě víc. 

### <a name="connector-placement"></a>Umístění konektoru

Proxy aplikací zvolí instancí umístění pro vás, na základě vašeho umístění klienta. Ale zobrazí se rozhodnout, kam se má nainstalovat konektor, budete k definování charakteristik latence síťových přenosů.

Pokud instalujete službu Proxy aplikací, položte si následující otázky:

* Kde je umístěná aplikace?
* Kde se nachází většina uživatelů, kteří přistupují k aplikaci?
* Kde se nachází instance Proxy aplikace?
* Už máte vyhrazený síťové připojení k datových centrech Azure nastavení, jako je Azure ExpressRoute nebo podobné VPN?

Konektor musí komunikovat s Azure a aplikací (kroky 2 a 3 v diagram toku dat), takže umístění ovlivňuje konektor latence tyto dvě připojení. Při vyhodnocení umístění konektoru, mějte na paměti následující body:

* Pokud chcete použít omezené delegování protokolu Kerberos (použitím KCD) pro jednotné přihlašování, konektor musí směrem pohledu do datového centra. Kromě toho server konektor musí být připojené k doméně.  
* Pokud máte pochybnosti, nainstalujte konektor blíže k aplikaci.

### <a name="general-approach-to-minimize-latency"></a>Obecný přístup k zajištění minimální latence

Můžete-li minimalizovat čekací doba provozu začátku do konce, optimalizace každé síťové připojení. Každé připojení možné ji optimalizovat podle:

* Snižuje vzdálenost mezi dva elementy end směrování.
* Výběr správného síťového procházení. Například procházení privátní síti, nikoli veřejného Internetu může být rychlejší, z důvodu vyhrazené odkazy.

Pokud máte vyhrazené sítě VPN nebo ExpressRoute propojení mezi Azure a k podnikové síti, můžete použít.

## <a name="focus-your-optimization-strategy"></a>Zaměřit strategie optimalizace

Existuje mnoho, které můžete provést k řízení připojení mezi uživatele a služba Proxy aplikace. Uživatelé mohou přistupovat k vaší aplikace z domácí sítě, v kavárně nebo jiné země. Místo toho můžete optimalizovat připojení ze služby Proxy aplikace k Proxy aplikace konektory a aplikace. Zvažte využití následujících charakteristik ve vašem prostředí.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Vzor 1: Put konektor blízko aplikace

Místní konektor blízko cílová aplikace v síti zákazníka. Tato konfigurace minimalizuje krok 3 v diagramu topografie, protože jsou zavřít konektoru a aplikace. 

Pokud vaše konektor potřebuje směrem pohledu řadiče domény, je tento vzor výhodné. Většina našich zákazníků použít tento vzor, protože je vhodný pro většinu scénářů. Tento vzor může být spojen s vzor 2 k optimalizaci přenosů mezi službou a konektor.

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Vzor 2: Využít výhod ExpressRoute s veřejného partnerského vztahu

Pokud máte nastavit veřejného partnerského vztahu ExpressRoute, můžete pro přenosy mezi Proxy aplikací a konektor rychlejší připojení ExpressRoute. Konektor je stále v síti, blíží aplikace.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Vzor 3: Využít výhod ExpressRoute s soukromého partnerského vztahu

Pokud máte vyhrazené sítě VPN nebo ExpressRoute nastavit s soukromého partnerského vztahu mezi Azure a k podnikové síti, existuje další možnost. V této konfiguraci se obvykle považuje virtuální sítě v Azure jako rozšíření podnikové sítě. Proto můžete nainstalovat konektor v datovém centru Azure a stále splňují požadavky s nízkou latencí připojení konektoru aplikace.

Latence nejsou ohrožená, protože provoz je předávaných přes vyhrazené připojení. Zlepšení latence služby konektoru Proxy aplikace můžete také získat, protože je konektor nainstalovaný v datovém centru Azure blízko vaše umístění klienta Azure AD.

![Diagram zobrazující konektoru nainstalovány v datovém centru Azure](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Jiné postupy

I když fokus tohoto článku je konektor umístění, můžete určit také umístění aplikace a získat lepší charakteristiky latence.

Organizace stále, jsou přesunutí jejich sítě do hostovaného prostředí. Díky tomu budou moci umístíte své aplikace v hostovaném prostředí, která je také součástí své podnikové síti a přesto být v rámci domény. V takovém případě můžete použít vzory popsané v předchozí části do nového umístění aplikace. Pokud zvažujete tuto možnost, přečtěte si téma [Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md).

Kromě toho zvažte uspořádání vaší konektorů pomocí [konektor skupiny](active-directory-application-proxy-connectors-azure-portal.md) cílové aplikace, které jsou v různých umístěních a sítě. 

## <a name="common-use-cases"></a>Běžné případy použití

V této části jsme provede několik běžných scénářů. Předpokládáme, že klient Azure AD (a proto koncový bod služby proxy serveru) se nachází v USA (US). Jaké jsou požadavky popsané v těchto případech se rovněž vztahují na jiných oblastech po celém světě používat.

Pro tyto scénáře jsme volání každé připojení "směrování" a číslo je snazší diskusi:

- **Směrování 1**: uživatele ke službě Proxy aplikace
- **Směrování 2**: Proxy aplikace služby konektoru Proxy aplikace
- **Směrování 3**: konektor Proxy aplikace do cílové aplikace 

### <a name="use-case-1"></a>Případ použití 1

**Scénář:** aplikace je v síti organizace v USA, s uživateli ve stejné oblasti. Mezi datové centrum Azure a k podnikové síti neexistuje žádná ExpressRoute nebo VPN.

**Doporučení:** vzor postupujte podle kroků 1, popsané v předchozí části. Pro zlepšení latence vezměte v úvahu pomocí služby ExpressRoute, v případě potřeby.

Toto je jednoduchá vzor. Můžete optimalizovat směrování 3 tím, že konektor téměř aplikace. Toto je také přirozené volbou, protože konektor je obvykle nainstalován s směrem pohledu do aplikace a do datového centra k provádění operací použitím KCD.

![Diagram znázorňující, že uživatelé, proxy, konektor a aplikace jsou všechny v USA](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Případ použití 2

**Scénář:** aplikace je v síti organizace v USA, s uživateli šíření globálně. Mezi datové centrum Azure a k podnikové síti neexistuje žádná ExpressRoute nebo VPN.

**Doporučení:** vzor postupujte podle kroků 1, popsané v předchozí části. 

Znovu běžné vzor je za účelem optimalizace směrování 3, kam umístíte konektor téměř aplikace. Směrování 3 není obvykle levnější, pokud je všechny ve stejné oblasti. Směrování 1 však může být dražší, v závislosti na tom, kde je uživatel, protože uživatelé po celém světě musí připojit k instanci Proxy aplikace v USA. Je vhodné poznamenat, že řešení proxy serveru má podobnou charakteristikou týkající se uživatelů se šíření globálně.

![Diagram zobrazující, že uživatelé jsou rozloženy globálně, ale proxy, konektor a aplikace jsou v USA](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Případ použití 3

**Scénář:** aplikace je v síti organizace v USA. Existuje ExpressRoute s veřejný partnerský vztah mezi Azure a k podnikové síti.

**Doporučení:** postupujte podle vzorů 1 a 2, které jsou popsané v předchozí části.

Nejprve umístíte konektor jak nejblíže k aplikaci. Pak systém automaticky pomocí ExpressRoute pro směrování 2. 

Pokud odkaz ExpressRoute používá veřejný partnerský vztah, provoz mezi proxy serveru a konektor toky přes tento odkaz. Směrování 2 optimalizovala latence.

![Diagram zobrazující ExpressRoute mezi proxy a konektor](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Případ použití 4

**Scénář:** aplikace je v síti organizace v USA. Existuje ExpressRoute s soukromého partnerského vztahu mezi Azure a k podnikové síti.

**Doporučení:** postupujte podle vzoru 3, popsané v předchozí části.

Místní konektor v datovém centru Azure, která je připojena k podnikové síti prostřednictvím soukromého partnerského vztahu ExpressRoute. 

Konektor se může v datovém centru Azure. Vzhledem k tomu, že konektor má stále směrem pohledu aplikace a datového centra prostřednictvím privátní sítě, zůstane optimalizované směrování 3. Kromě toho směrování 2 je optimalizovaná Další.

![Diagram zobrazující konektor v datovém centru Azure a ExpressRoute mezi konektoru a aplikace](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Případ použití 5

**Scénář:** aplikace je v síti organizace v EU s instance Proxy aplikace a většina uživatelů v USA.

**Doporučení:** umístit konektor téměř aplikace. Protože USA uživatelé přistupují k instanci Proxy aplikace, která se stane být ve stejné oblasti, není příliš nákladné směrování 1. 3 směrování je optimalizované. Zvažte použití ExpressRoute za účelem optimalizace směrování 2. 

![Diagram zobrazující uživatelů a proxy serveru v USA, s konektoru a aplikace v EU](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

Můžete také použít jeden další variant v této situaci. Pokud většina uživatelů v organizaci se v USA, pak pravděpodobné, který rozšiřuje vaší sítě do US také. Umístěte konektor v USA a použijte řádek vyhrazené interní podnikové síti do aplikace v EU. Tento způsob směrování 2 a 3 jsou optimalizované.

![Diagram zobrazující proxy, uživatelů a konektor v USA, aplikace v EU](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Další postup

- [Povolení Proxy aplikace](active-directory-application-proxy-enable.md)
- [Povolení jednoduchého přihlášení](active-directory-application-proxy-sso-using-kcd.md)
- [Povolení podmíněného přístupu](application-proxy-enable-remote-access-sharepoint.md)
- [Řešení problémů, které máte s pomocí Proxy aplikace](active-directory-application-proxy-troubleshoot.md)
