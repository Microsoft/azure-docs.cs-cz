---
title: Důležité informace o topologii sítě pro proxy aplikace Azure AD
description: Zahrnuje důležité informace o topologii sítě při použití proxy aplikací Azure AD.
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
ms.date: 07/22/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaceaf1f5e9b6e34ced5db39b61e607fffcb5953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295141"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Důležité informace o topologii sítě při použití proxy aplikace služby Azure Active Directory

Tento článek vysvětluje aspekty topologie sítě při použití proxy aplikací Azure Active Directory (Azure AD) pro vzdálené publikování a přístup k aplikacím.

## <a name="traffic-flow"></a>Dopravní tok

Když je aplikace publikována prostřednictvím proxy aplikace Azure AD, provoz od uživatelů k aplikacím toky prostřednictvím tří připojení:

1. Uživatel se připojí ke službě Azure AD Application Proxy veřejné koncového bodu v Azure
1. Služba Proxy aplikace se připojuje ke konektoru Proxy aplikace
1. Konektor proxy aplikace se připojuje k cílové aplikaci

![Diagram znázorňující tok provozu z uživatele na cílovou aplikaci](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Umístění klienta a služba proxy aplikace

Když se zaregistrujete pro klienta Azure AD, oblast vašeho tenanta je určena země nebo oblasti, kterou zadáte. Když povolíte proxy aplikace, instance služby Proxy aplikace pro vašeho tenanta jsou vybrány nebo vytvořeny ve stejné oblasti jako váš tenant Azure AD nebo nejbližší oblast k němu.

Například pokud je země nebo oblast vašeho klienta Azure AD ve Velké Británii, všechny konektory proxy aplikací používají instance služeb v evropských datových centrech. Když uživatelé přistupují k publikovaným aplikacím, jejich provoz prochází instancemi služby Proxy aplikace v tomto umístění.

## <a name="considerations-for-reducing-latency"></a>Důležité informace pro snížení latence

Všechna proxy řešení zavádějí latenci do síťového připojení. Bez ohledu na to, které proxy nebo VPN řešení zvolíte jako řešení vzdáleného přístupu, vždy obsahuje sadu serverů, které umožňují připojení do podnikové sítě.

Organizace obvykle zahrnují koncové body serveru v jejich hraniční síti. S Azure AD proxy aplikace však provoz toky prostřednictvím služby proxy v cloudu, zatímco konektory jsou umístěny ve vaší podnikové síti. Není vyžadována žádná obvodová síť.

Další části obsahují další návrhy, které vám pomohou snížit latenci ještě více. 

### <a name="connector-placement"></a>Umístění konektoru

Proxy aplikace zvolí umístění instancí pro vás, na základě umístění vašeho klienta. Můžete se však rozhodnout, kam nainstalovat konektor, což vám dává moc definovat charakteristiky latence síťového provozu.

Při nastavování služby Proxy aplikace položte následující otázky:

- Kde se aplikace nachází?
- Kde se nachází většina uživatelů, kteří mají přístup k aplikaci?
- Kde se nachází instance Proxy aplikace?
- Už máte nastavené vyhrazené síťové připojení k datovým centrům Azure, jako je Azure ExpressRoute nebo podobná VPN?

Konektor musí komunikovat s Azure a vaše aplikace (kroky 2 a 3 v diagramu toku provozu), takže umístění konektoru ovlivňuje latenci těchto dvou připojení. Při hodnocení umístění konektoru mějte na paměti následující body:

- Pokud chcete použít kerberos omezené delegování (KCD) pro jednotné přihlašování, pak konektor potřebuje zorné pole do datového centra. Kromě toho musí být připojen k serveru konektoru.  
- V případě pochybností nainstalujte konektor blíže k aplikaci.

### <a name="general-approach-to-minimize-latency"></a>Obecný přístup k minimalizaci latence

Optimalizací jednotlivých síťových připojení můžete minimalizovat latenci koncového provozu. Každé připojení lze optimalizovat pomocí:

- Zmenšení vzdálenosti mezi dvěma konci směrování.
- Výběr správné sítě pro přechod. Například procházení privátní sítě spíše než veřejný Internet může být rychlejší, protože vyhrazené odkazy.

Pokud máte vyhrazené propojení VPN nebo ExpressRoute mezi Azure a vaší podnikovou sítí, můžete to použít.

## <a name="focus-your-optimization-strategy"></a>Zaměřte svou optimalizační strategii

Je málo, co můžete udělat pro řízení připojení mezi uživateli a službou Proxy aplikace. Uživatelé mohou přistupovat k vašim aplikacím z domácí sítě, kavárny nebo z jiné země nebo oblasti. Místo toho můžete optimalizovat připojení ze služby Proxy aplikace na konektory proxy aplikací k aplikacím. Zvažte začlenění následující vzory ve vašem prostředí.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Vzor 1: Položte konektor blízko aplikace

Umístěte konektor v blízkosti cílové aplikace v síti zákazníka. Tato konfigurace minimalizuje krok 3 v diagramu topografii, protože spojnice a aplikace jsou blízko.

Pokud váš konektor potřebuje zorné pole k řadiči domény, pak tento vzor je výhodné. Většina našich zákazníků používá tento vzor, protože funguje dobře pro většinu scénářů. Tento vzor lze také kombinovat se vzorem 2 pro optimalizaci provozu mezi službou a konektorem.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Vzor 2: Využití expressroute s partnerským vztahem Microsoftu

Pokud máte ExpressRoute nastavenou s partnerským vztahem Microsoftu, můžete použít rychlejší připojení ExpressRoute pro přenos mezi proxy aplikací a konektorem. Konektor je stále ve vaší síti, v blízkosti aplikace.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Vzor 3: Využití expressroute se soukromým partnerským vztahem

Pokud máte vyhrazenou síť VPN nebo ExpressRoute nastavenou s privátním partnerským vztahem mezi Azure a vaší podnikovou sítí, máte další možnost. V této konfiguraci virtuální síť v Azure se obvykle považuje za rozšíření podnikové sítě. Takže můžete nainstalovat konektor v datovém centru Azure a stále splňovat požadavky na nízkou latenci připojení konektoru k aplikaci.

Latence není ohrožena, protože provoz probíhá přes vyhrazené připojení. Můžete také získat lepší latence služby proxy aplikace ke konektoru, protože konektor je nainstalovaný v datovém centru Azure v blízkosti vašeho umístění klienta Azure AD.

![Diagram znázorňující konektor nainstalovaný v datovém centru Azure](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Další přístupy

Přestože zaměření tohoto článku je umístění konektoru, můžete také změnit umístění aplikace získat lepší charakteristiky latence.

Organizace stále více přesouvají své sítě do hostovaných prostředí. To jim umožňuje umístit své aplikace do hostovaného prostředí, které je také součástí jejich podnikové sítě a stále v rámci domény. V tomto případě vzorky popsané v předchozích částech lze použít pro nové umístění aplikace. Pokud zvažujete tuto možnost, přečtěte si téma [Služby domény Azure AD](../../active-directory-domain-services/overview.md).

Kromě toho zvažte uspořádání konektorů pomocí [skupin konektorů](application-proxy-connector-groups.md) k cílení aplikací, které jsou v různých umístěních a sítích.

## <a name="common-use-cases"></a>Běžné případy použití

V této části projdeme několik běžných scénářů. Předpokládejme, že tenanta Azure AD (a proto koncový bod služby proxy) je umístěn ve Spojených státech (USA). Důležité informace popsané v těchto případech použití platí také pro jiné oblasti po celém světě.

V těchto scénářích nazýváme každé připojení "hop" a číslo je pro snadnější diskusi:

- **Hop 1**: Uživatel služby Proxy aplikace
- **Hop 2**: Služba Proxy aplikace na konektor Proxy aplikace
- **Hop 3**: Konektor proxy aplikace k cílové aplikaci 

### <a name="use-case-1"></a>Případ použití 1

**Scénář:** Aplikace je v síti organizace v USA, s uživateli ve stejné oblasti. Mezi datovým centrem Azure a podnikovou sítí neexistuje žádná expressroute nebo VPN.

**Doporučení:** Postupujte podle vzoru 1, vysvětleno v předchozí části. Pro zlepšení latence, zvažte použití ExpressRoute, v případě potřeby.

Jedná se o jednoduchý vzor. Můžete optimalizovat hop 3 umístěním konektoru v blízkosti aplikace. To je také přirozenou volbou, protože konektor je obvykle nainstalován s přímkou pohledu do aplikace a do datového centra k provádění operací KCD.

![Diagram, který znázorňuje uživatele, proxy server, konektor a aplikace jsou v USA](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Případ použití 2

**Scénář:** Aplikace je v síti organizace v USA, s uživateli rozprostřenými po celém světě. Mezi datovým centrem Azure a podnikovou sítí neexistuje žádná expressroute nebo VPN.

**Doporučení:** Postupujte podle vzoru 1, vysvětleno v předchozí části.

Opět platí, že společný vzor je optimalizovat směrování 3, kde umístíte konektor v blízkosti aplikace. Směrování 3 není obvykle drahé, pokud je vše ve stejné oblasti. Směrování 1 však může být dražší v závislosti na tom, kde je uživatel, protože uživatelé po celém světě musí přistupovat k instanci proxy aplikace v USA. Stojí za zmínku, že jakékoli proxy řešení má podobné vlastnosti, pokud jde o uživatele, které jsou rozloženy po celém světě.

![Uživatelé jsou rozšířeny po celém světě, ale všechno ostatní je v USA](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Případ použití 3

**Scénář:** Aplikace je v síti organizace v USA. ExpressRoute s partnerským vztahem Microsoftu existuje mezi Azure a podnikovou sítí.

**Doporučení:** Postupujte podle vzorů 1 a 2, které jsou vysvětleny v předchozí části.

Nejprve umístěte konektor co nejblíže k aplikaci. Potom systém automaticky používá ExpressRoute pro směrování 2.

Pokud odkaz ExpressRoute používá partnerský vztah Microsoftu, přeteče přes toto propojení přenosmezi proxy serverem a konektorem. Směrování 2 má optimalizovanou latenci.

![Diagram znázorňující ExpressRoute mezi proxy serverem a konektorem](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Případ použití 4

**Scénář:** Aplikace je v síti organizace v USA. ExpressRoute s privátním partnerským vztahem existuje mezi Azure a podnikovou sítí.

**Doporučení:** Postupujte podle vzoru 3, vysvětleno v předchozí části.

Umístěte konektor v datovém centru Azure, které je připojené k podnikové síti prostřednictvím soukromého partnerského vztahu ExpressRoute.

Konektor může být umístěn v datovém centru Azure. Vzhledem k tomu, že konektor má stále zorné pole do aplikace a datového centra prostřednictvím privátní sítě, směrování 3 zůstává optimalizované. Kromě toho je hop 2 dále optimalizován.

![Konektor v datovém centru Azure, ExpressRoute mezi konektorem a aplikací](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Případ použití 5

**Scénář:** Aplikace je v síti organizace v Evropě, s instancí Proxy aplikace a většina uživatelů v USA.

**Doporučení:** Umístěte konektor v blízkosti aplikace. Vzhledem k tomu, že uživatelé v USA přistupují k instanci proxy aplikace, která se nachází ve stejné oblasti, není směrování 1 příliš nákladné. Hop 3 je optimalizován. Zvažte použití ExpressRoute pro optimalizaci směrování 2.

![Diagram znázorňuje uživatele a proxy v USA, konektor a aplikace v Evropě](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Můžete také zvážit použití jedné další varianty v této situaci. Pokud většina uživatelů v organizaci jsou v USA, pak je pravděpodobné, že vaše síť rozšiřuje do USA stejně. Umístěte konektor v USA a použijte vyhrazenou interní linku podnikové sítě k aplikaci v Evropě. Tímto způsobem jsou optimalizovány chmel2 a 3.

![Diagram znázorňuje uživatele, proxy a konektor v USA, aplikace v Evropě](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Další kroky

- [Povolit proxy aplikace](application-proxy-add-on-premises-application.md)
- [Povolení jednoduchého přihlášení](application-proxy-configure-single-sign-on-with-kcd.md)
- [Povolení podmíněného přístupu](application-proxy-integrate-with-sharepoint-server.md)
- [Řešení potíží s proxy aplikace](application-proxy-troubleshoot.md)
