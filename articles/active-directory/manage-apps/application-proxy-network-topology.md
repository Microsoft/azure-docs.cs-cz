---
title: Aspekty topologie sítě, při použití Azure Active Directory Application Proxy | Dokumentace Microsoftu
description: Popisuje aspekty topologie sítě při použití Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 91f419f22dc4fdd13b136df5a790cd5ab699f683
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180088"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Aspekty topologie sítě při použití Azure Active Directory Application Proxy

Tento článek vysvětluje aspekty topologie sítě při použití Proxy aplikace Azure Active Directory (Azure AD) pro publikování a vzdálený přístup k vaší aplikace.

## <a name="traffic-flow"></a>Tok provozu

Při publikování aplikace prostřednictvím Proxy aplikací Azure AD se prochází provoz od uživatelů k aplikacím tři připojení:

1. Uživatel připojí k Azure AD Application Proxy veřejný koncový bod služby v Azure
2. Služba Proxy aplikace se připojí k konektor Proxy aplikací
3. Konektor Proxy aplikací se připojí k cílové aplikaci

![Diagram znázorňující tok přenosů z uživatele do cílové aplikace](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Umístění tenanta a Proxy aplikace služby

Při registraci tenanta Azure AD, oblast vašeho tenanta se určuje podle země, kterou zadáte. Když povolíte Proxy aplikací, jsou instance služby Proxy aplikace vašeho tenanta zvolili nebo vytvoří ve stejné oblasti jako váš tenant Azure AD, nebo k němu nejbližší oblasti.

Pokud zemi nebo oblast vašeho tenanta Azure AD je Spojené království, všechny konektory Proxy aplikací pomocí instance služby v EU datových centrech. Pokud vaši uživatelé přístup k publikovaným aplikací, jejich přenos prochází instance služby Proxy aplikace v tomto umístění.

## <a name="considerations-for-reducing-latency"></a>Důležité informace pro snížení latence

Všechna řešení proxy představovat čekací doba na připojení k síti. Bez ohledu na to, které proxy server nebo řešení sítě VPN zvolíte jako řešení pro vzdálený přístup vždy obsahuje sady servery umožňující připojení k uvnitř firemní sítě.

Organizace obvykle zahrnují koncové body serveru v jejich hraniční síti. S Azure AD Application Proxy ale provoz prochází přes službu proxy serveru v cloudu při konektory se nacházejí v podnikové síti. Žádné hraniční síti je povinný.

Následující části obsahují další návrhy, která vám pomůže snížit latenci ještě více. 

### <a name="connector-placement"></a>Umístění konektoru

Proxy aplikací vybere umístění instancí za vás na základě umístění vašeho tenanta. Ale získáte rozhodnout, kam se má nainstalovat konektor, což vám dává možnost definovat vlastnosti latence síťového provozu.

Při nastavování služby Proxy aplikace, odpovědět na tyto otázky:

* Kde se nachází aplikace?
* Kde se nachází většina uživatelů, kteří přístup k aplikaci?
* Kde se nachází instance Proxy aplikací?
* Už máte vyhrazené síťové připojení k datovým centrům Azure nastavení, jako je Azure ExpressRoute nebo podobné VPN?

Konektor má ke komunikaci s Azure a aplikací (kroků 2 a 3 v vývojový diagram provoz), takže umístění ovlivňuje konektor latence tyto dvě připojení. Při vyhodnocení umístění konektoru, mějte na paměti následující body:

* Pokud chcete použít omezené delegování protokolu Kerberos (KCD) pro jednotné přihlašování, konektoru vyžaduje dohled do datového centra. Kromě toho server konektoru musí být připojené k doméně.  
* V případě pochybností si nainstalujte konektor blíž k aplikaci.

### <a name="general-approach-to-minimize-latency"></a>Obecný přístup, aby se minimalizovala latence

Můžete minimalizovat latenci síťový provoz na začátku do konce optimalizací každé síťové připojení. Každé připojení můžete replikaci optimalizovat takto:

* Snižuje vzdálenost mezi dva elementy end třídy segment směrování.
* Výběr správné síti k procházení. Například procházení privátní síti, nikoli veřejného Internetu může být rychlejší, protože vyhrazená propojení.

Pokud máte vyhrazené sítě VPN nebo ExpressRoute propojení mezi Azure a vaší podnikové síti, můžete ho použít.

## <a name="focus-your-optimization-strategy"></a>Zaměřte svou strategii optimalizace

Je trochu, které vám pomohou řídit připojení mezi uživateli a službu Proxy aplikací. Uživatelé mohou přístup k vašim aplikacím z domácí sítě, kavárny nebo jiné zemi. Místo toho můžete optimalizovat připojení ze služby Proxy aplikace pro konektory Proxy aplikací k aplikacím. Zvažte využití následujících vzorů ve vašem prostředí.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Způsob 1: Umístění konektoru blízko aplikace

Umístění konektoru blízko cílové aplikace v síti zákazníka. Tato konfigurace minimalizuje kroku 3 v diagramu topografie, protože konektor a aplikace jsou zavřít. 

Pokud váš konektor dohled k řadiči domény, pak tento vzor je výhodné. Většina našich zákazníků tento model použijte, protože je vhodný pro většinu scénářů. Tento model také zkombinovat se vzorem 2 pro optimalizaci provozu mezi službou a konektor.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Způsob 2: Využijte výhod ExpressRoute s využitím partnerského vztahu Microsoftu

Pokud máte ExpressRoute s partnerský vztah Microsoftu, můžete použít rychlejší připojení ExpressRoute pro přenos dat mezi Proxy aplikací a konektor. Konektor je stále v síti, blízko ní.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Způsob 3: Využijte výhod s privátní partnerský vztah ExpressRoute

Pokud máte vyhrazené sítě VPN nebo ExpressRoute nastavit privátní partnerské vztahy mezi Azure a vaší podnikové síti, existuje další možnost. V této konfiguraci virtuální sítě v Azure se obvykle považuje za jako rozšíření podnikové sítě. Proto můžete nainstalovat konektor v datacentru Azure a stále splňují požadavky s nízkou latencí připojení konektoru aplikace.

Latence není dojde k ohrožení bezpečnosti, protože se přenášejí přenosů pomocí vyhrazeného připojení. Získáte také zlepšení latence služby konektoru Proxy aplikací, protože je konektor nainstalovaný v datacentru Azure blízko umístění vašeho tenanta Azure AD.

![Diagram znázorňující konektoru, nainstalovaná v rámci datového centra Azure](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Další postupy

Umístění konektoru sice hlavním cílem tohoto článku můžete také změnit umístění aplikace, abyste získali lepší vlastnosti latence.

Organizace se stále víc i přesouvají svých sítí do hostované prostředí. Díky tomu umístíte své aplikace v hostovaném prostředí, která je také součástí své podnikové síti, a přesto být v rámci domény. V takovém případě lze použít tyto vzory se dají popsané v předchozích částech do nového umístění aplikace. Pokud zvažujete tuto možnost, přečtěte si téma [Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md).

Kromě toho zvažte uspořádání své konektory pomocí [skupiny konektorů](application-proxy-connector-groups.md) do cílové aplikace, které jsou v různých umístěních a sítě. 

## <a name="common-use-cases"></a>Běžné případy použití

V této části provedeme několik běžných scénářů. Předpokládejme, že tenanta Azure AD (a tedy koncový bod služby proxy serveru) se nachází ve Spojených státech amerických (USA). Jaké jsou požadavky popsané v těchto případech platí také do jiných oblastí po celém světě využívají.

Pro tyto scénáře jsme volat "segmentu směrování" každé připojení a číslo pro snazší diskuse:

- **Hop 1**: Uživatele ve službě Proxy aplikací
- **Hop 2**: Služba Proxy aplikace pro konektor Proxy aplikací
- **Hop 3**: Konektor Proxy aplikace k cílové aplikaci 

### <a name="use-case-1"></a>Případ použití 1

**Scénář:** Aplikace je v síti vaší organizace v USA, s uživateli ve stejné oblasti. Žádné ExpressRoute nebo VPN neexistuje mezi datové centrum Azure a v podnikové síti.

**Doporučení:** Tvar 1, je popsáno v předchozí části. Pro zlepšení latence zvažte možnost použití služby ExpressRoute, v případě potřeby.

Toto je jednoduchý vzor. Optimalizace směrování 3 tak, že konektor v aplikaci. To je také přirozenou volbou, protože konektor je obvykle nainstalovaný s dohlednost služby do aplikace a do datového centra k provádění operací KCD.

![Diagram znázorňující, že uživatelé, proxy server, konektor a aplikace jsou všechny v USA](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Případ použití 2

**Scénář:** Aplikace je v síti vaší organizace v USA, s uživateli rozprostřete globálně. Žádné ExpressRoute nebo VPN neexistuje mezi datové centrum Azure a v podnikové síti.

**Doporučení:** Tvar 1, je popsáno v předchozí části. 

Běžný vzor je opět optimalizovat směrování 3, se vkládá konektoru v aplikaci. Směrování 3 není obvykle náročná, pokud je vše v rámci stejné oblasti. Směrování 1 však může být nákladnější v závislosti na tom, kde je uživatel, protože uživatelé po celém světě musí připojit k instanci Proxy aplikací v USA. Je vhodné poznamenat, že všechny proxy řešení má podobnou charakteristikou týkající se uživatelů se rozprostřete globálně.

![Diagram znázorňující, že uživatelé jsou rozloženy globálně, ale proxy, konektor a aplikace jsou v USA.](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Případ 3

**Scénář:** Aplikace je v síti vaší organizace v USA. ExpressRoute s využitím partnerského vztahu Microsoftu existuje mezi Azure a v podnikové síti.

**Doporučení:** Postupujte podle vzorů 1 a 2, je popsáno v předchozí části.

Nejprve umístíte konektoru co nejblíže k aplikaci. Pak systém automaticky používá ExpressRoute pro směrování 2. 

Pokud partnerský vztah Microsoftu používá spojení ExpressRoute, provoz mezi proxy a konektor probíhá přes tento odkaz. Směrování 2 má optimalizovanou latence.

![Diagram znázorňující ExpressRoute mezi proxy a konektoru](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Případ použití 4

**Scénář:** Aplikace je v síti vaší organizace v USA. Existuje ExpressRoute s soukromého partnerského vztahu mezi Azure a v podnikové síti.

**Doporučení:** Tvar 3, je popsáno v předchozí části.

Konektor umístěte do datacentra Azure, která je připojená k podnikové síti prostřednictvím soukromého partnerského vztahu ExpressRoute. 

Konektor můžete umístit do datacentra Azure. Vzhledem k tomu, že konektor má stále dohlednost služby do aplikace a datového centra prostřednictvím privátní sítě, směrování 3 zůstane optimalizované. Kromě toho je dál optimalizovat směrování 2.

![Diagram znázorňující konektor mezi konektor a aplikace v datacentru Azure a ExpressRoute](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Případ použití 5

**Scénář:** Aplikace je v síti vaší organizace v EU, s instancí Proxy aplikací a většina uživatelů v USA.

**Doporučení:** Umístění konektoru v aplikaci. Protože uživatelé v USA se přístup k instanci Proxy aplikací, který je ve stejné oblasti, směrování 1 není moc drahé. 3 směrování je optimalizované. Zvažte použití ExpressRoute pro optimalizaci směrování 2. 

![Diagram znázorňující uživatelů a proxy serveru v USA se konektor a aplikace v EU](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Můžete také zvážit použití jeden další typ variant v této situaci. Pokud se většina uživatelů v organizaci se v USA, pak je pravděpodobné, že síti rozšiřuje i ve Spojených státech. Umístěte konektoru v USA a použijte vyhrazený interní podnikové síti řádek do aplikace v EU. Tento způsob směrování 2 a 3 jsou optimalizované.

![Diagram znázorňující uživatelů, proxy a konektor v USA, aplikace v EU](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Další postup

- [Povolení Proxy aplikace](application-proxy-add-on-premises-application.md)
- [Povolení jednoduchého přihlášení](application-proxy-configure-single-sign-on-with-kcd.md)
- [Povolení podmíněného přístupu](application-proxy-integrate-with-sharepoint-server.md)
- [Řešení potíží s problémy, se kterými máte potíže s Proxy aplikací](application-proxy-troubleshoot.md)
