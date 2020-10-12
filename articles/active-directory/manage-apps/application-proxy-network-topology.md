---
title: Požadavky na topologii sítě pro Azure Proxy aplikací služby AD
description: Popisuje důvody síťové topologie při použití Azure Proxy aplikací služby AD.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d814a3ce62d7e9b3ce04953e05293d17ecc08d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764464"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Důvody síťové topologie při použití Proxy aplikací služby Azure Active Directory

Tento článek vysvětluje důvody síťové topologie při použití proxy aplikace Azure Active Directory (Azure AD) pro vzdálené publikování a přístup k aplikacím.

## <a name="traffic-flow"></a>Tok přenosů

Když se aplikace publikuje prostřednictvím služby Azure Proxy aplikací služby AD, provoz od uživatelů do aplikací se bude natékat prostřednictvím tří připojení:

1. Uživatel se připojí ke veřejnému koncovému bodu služby Azure Proxy aplikací služby AD v Azure.
1. Služba proxy aplikací se připojuje ke konektoru proxy aplikací.
1. Konektor proxy aplikace se připojí k cílové aplikaci

![Diagram znázorňující tok přenosů od uživatele k cílové aplikaci](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Umístění tenanta a služba proxy aplikací

Když se přihlásíte ke klientovi služby Azure AD, určí se oblast vašeho tenanta podle země nebo oblasti, kterou zadáte. Když povolíte proxy aplikací, instance služby proxy aplikací pro vašeho tenanta se volí nebo vytvoří ve stejné oblasti jako váš tenant Azure AD nebo v nejbližší oblasti.

Pokud je například zemí nebo oblastí tenanta Azure AD Spojené království, všechny konektory proxy aplikací používají instance služby v evropských datových centrech. Když uživatelé přistupují k publikovaným aplikacím, jejich provoz prochází prostřednictvím instancí služby proxy aplikací v tomto umístění.

## <a name="considerations-for-reducing-latency"></a>Předpoklady pro snížení latence

Všechna proxy řešení zavádějí latenci do síťového připojení. Bez ohledu na to, jaké řešení proxy nebo VPN jste si zvolili jako řešení vzdáleného přístupu, vždy zahrnuje sadu serverů, které umožňují připojení ve vaší podnikové síti.

Mezi organizace obvykle patří koncové body serveru v hraniční síti. V případě Azure Proxy aplikací služby AD ale přenosy dat prostřednictvím služby proxy v cloudu, zatímco se konektory nacházejí v podnikové síti. Není nutná žádná hraniční síť.

Další části obsahují další návrhy, které vám pomůžou snížit latenci ještě dál. 

### <a name="connector-placement"></a>Umístění konektoru

Proxy aplikací zvolí umístění instancí na základě vašeho umístění tenanta. Ale budete se muset rozhodnout, kam konektor nainstalovat, a budete moct definovat vlastnosti latence síťového provozu.

Při nastavování služby proxy aplikací si položte následující otázky:

- Kde se aplikace nachází?
- Kde se nachází většina uživatelů, kteří k této aplikaci přistupuje?
- Kde se nachází instance proxy aplikace?
- Už máte vyhrazené síťové připojení k nastaveným datovým centrům Azure, jako je Azure ExpressRoute nebo podobná síť VPN?

Konektor musí komunikovat s Azure i s vašimi aplikacemi (kroky 2 a 3 v diagramu toku provozu), takže umístění konektoru ovlivňuje latenci těchto dvou připojení. Při vyhodnocování umístění konektoru mějte na paměti následující body:

- Pokud chcete pro jednotné přihlašování použít omezené delegování protokolu Kerberos (KCD), pak konektor potřebuje pro datové centrum řadu pohledů. Kromě toho musí být server konektoru připojený k doméně.  
- Pokud máte pochybnosti, nainstalujte konektor blíže k aplikaci.

### <a name="general-approach-to-minimize-latency"></a>Obecný přístup k minimalizaci latence

Díky optimalizaci všech síťových připojení můžete minimalizovat latenci koncového provozu. Každé připojení může být optimalizováno:

- Snížení vzdálenosti mezi dvěma konci segmentu směrování.
- Výběr správné sítě pro procházení. Například procházení privátní sítě, nikoli veřejného Internetu, může být rychlejší z důvodu vyhrazených odkazů.

Pokud máte vyhrazenou síť VPN nebo ExpressRoute propojení mezi Azure a podnikovou sítí, můžete ji chtít použít.

## <a name="focus-your-optimization-strategy"></a>Zaměřte se na strategii optimalizace

K řízení připojení mezi uživateli a službou proxy aplikací je málo možné. Uživatelé můžou k aplikacím přistupovat z domácí sítě, kavárny nebo jiné země nebo oblasti. Místo toho můžete optimalizovat připojení ze služby proxy aplikací k konektorům proxy aplikací k aplikacím. Zvažte zahrnutí následujících vzorů ve vašem prostředí.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Vzor 1: vložení konektoru blízko do aplikace

Umístěte konektor blízko k cílové aplikaci v síti zákazníka. Tato konfigurace minimalizuje krok 3 v diagramu topografie, protože konektor a aplikace jsou zavřené.

Pokud váš konektor potřebuje k řadiči domény nějaký pohled, je tento vzor výhodný. Většina našich zákazníků tento model využívá, protože ve většině scénářů funguje dobře. Tento model je také možné kombinovat se vzorem 2 pro optimalizaci provozu mezi službou a konektorem.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Vzor 2: Využijte výhod ExpressRoute s partnerským vztahem Microsoftu

Pokud jste ExpressRoute nastavili s partnerským vztahem Microsoftu, můžete použít rychlejší připojení ExpressRoute pro provoz mezi proxy aplikací a konektorem. Konektor je stále ve vaší síti, blízko do aplikace.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Vzor 3: Využijte výhod ExpressRoute se soukromým partnerským vztahem

Pokud máte vyhrazenou síť VPN nebo ExpressRoute nastavili s privátním partnerským vztahem mezi Azure a podnikovou sítí, máte další možnost. V této konfiguraci se virtuální síť v Azure obvykle považuje za rozšíření podnikové sítě. Proto můžete konektor nainstalovat do datacentra Azure a pořád vyhovět požadavkům na nízkou latenci připojení typu konektor k aplikaci.

Latence není ohrožená, protože provoz probíhá přes vyhrazené připojení. Získáte také vylepšenou latenci služby proxy serveru aplikace, protože konektor je nainstalován v datovém centru Azure blízko do umístění tenanta Azure AD.

![Diagram znázorňující konektor nainstalovaný v datacentru Azure](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Další přístupy

I když je zaměření tohoto článku umístění konektoru, můžete také změnit umístění aplikace, abyste získali lepší vlastnosti latence.

Stále více organizací přesouvá své sítě do hostovaných prostředí. To jim umožní umístit své aplikace do hostovaného prostředí, které je také součástí své podnikové sítě, a nadále být v doméně. V tomto případě lze vzory popsané v předchozích částech použít pro nové umístění aplikace. Pokud zvažujete tuto možnost, přečtěte si téma [Azure AD Domain Services](../../active-directory-domain-services/overview.md).

Kromě toho zvažte uspořádání konektorů pomocí [skupin konektorů](application-proxy-connector-groups.md) na cílové aplikace, které jsou v různých umístěních a sítích.

## <a name="common-use-cases"></a>Běžné případy použití

V této části si projdeme několik běžných scénářů. Předpokládejme, že je tenant Azure AD (a proto koncový bod služby proxy) umístěný v USA (US). Pokyny popsané v těchto případech použití platí i pro ostatní oblasti po celém světě.

V těchto scénářích říkáme každé připojení "směrování" a jejich počet je pro snazší diskuzi:

- **Směrování 1**: uživatel na službu proxy aplikací
- **Směrování 2**: služba proxy aplikací pro konektor proxy aplikací
- **Směrování 3**: konektor proxy aplikací k cílové aplikaci 

### <a name="use-case-1"></a>Případ použití 1

**Scénář:** Aplikace se nachází v síti organizace v USA s uživateli ve stejné oblasti. Mezi datacentrem Azure a podnikovou sítí neexistují žádné ExpressRoute ani VPN.

**Doporučení:** Sledujte vzor 1, který je vysvětlen v předchozí části. Pro lepší latenci zvažte v případě potřeby používání ExpressRoute.

Toto je jednoduchý vzor. Segment směrování 3 optimalizujete umístěním konektoru poblíž aplikace. To je také přirozený výběr, protože konektor je obvykle nainstalován s možností pohledu do aplikace a datacentra pro provádění operací KCD.

![Diagram, který zobrazuje uživatele, proxy, konektor a aplikace, jsou všechny v USA.](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Případ použití 2

**Scénář:** Aplikace se nachází v síti organizace v USA, přičemž uživatelé se globálně rozšíří. Mezi datacentrem Azure a podnikovou sítí neexistují žádné ExpressRoute ani VPN.

**Doporučení:** Sledujte vzor 1, který je vysvětlen v předchozí části.

Běžným vzorem je znovu optimalizace směrování 3, kde umístíte konektor poblíž aplikace. Segment směrování 3 není obvykle nákladný, pokud je v rámci stejné oblasti. Směrování 1 ale může být dražší v závislosti na tom, kde je uživatel, protože uživatelé na světě musí přistupovat k instanci proxy aplikace v USA. Je potřeba poznamenat, že jakékoli řešení proxy má podobné vlastnosti týkající se globálně rozprostřených uživatelů.

![Uživatelé se rozprostře globálně, ale všechno ostatní je v USA.](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Případ použití 3

**Scénář:** Aplikace se nachází v síti organizace v USA. Mezi Azure a podnikovou sítí existuje ExpressRoute s partnerským vztahem Microsoftu.

**Doporučení:** Postupujte podle vzorů 1 a 2, který je vysvětlen v předchozí části.

Nejdřív umístěte konektor co nejblíže do aplikace. Systém pak automaticky používá ExpressRoute pro segment směrování 2.

Pokud odkaz ExpressRoute používá partnerský vztah Microsoftu, přenos dat mezi proxy serverem a konektorem pokračuje přes tento odkaz. Směrování 2 má optimalizovanou latenci.

![Diagram znázorňující ExpressRoute mezi proxy a konektorem](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Případ použití 4

**Scénář:** Aplikace se nachází v síti organizace v USA. Mezi Azure a podnikovou sítí existuje ExpressRoute se soukromým partnerským vztahem.

**Doporučení:** Sledujte vzorek 3, který je vysvětlen v předchozí části.

Umístěte konektor do datacentra Azure, které je připojené k podnikové síti prostřednictvím privátního partnerského vztahu ExpressRoute.

Konektor můžete umístit do datacentra Azure. Vzhledem k tomu, že konektor stále obsahuje pohled na aplikaci a datacentrum přes soukromou síť, bude směrování 3 i nadále optimalizováno. Kromě toho je směrování 2 optimalizováno dále.

![Konektor v datacentru Azure, ExpressRoute mezi konektorem a aplikací](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Případ použití 5

**Scénář:** Aplikace je v podnikové síti v Evropě s instancí proxy aplikací a nejvíc uživateli v USA.

**Doporučení:** Umístěte konektor poblíž aplikace. Vzhledem k tomu, že uživatelé USA přistupují k instanci proxy aplikace, která se nachází ve stejné oblasti, není segment směrování 1 příliš nákladný. Segment směrování 3 je optimalizován. Zvažte použití ExpressRoute k optimalizaci směrování 2.

![Diagram znázorňuje uživatele a proxy server v USA, konektoru a aplikaci v Evropě.](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

V této situaci můžete také zvážit použití jedné jiné varianty. Pokud je většina uživatelů v organizaci v USA, je pravděpodobné, že vaše síť rozšiřuje i na nás. Umístěte konektor do USA a použijte vyhrazené interní síťové linky pro aplikaci v Evropě. Tímto způsobem jsou optimalizovány směrování 2 a 3.

![Diagram znázorňuje uživatele, proxy server a konektor v USA, aplikace v Evropě.](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Další kroky

- [Povolit proxy aplikací](application-proxy-add-on-premises-application.md)
- [Povolení jednoduchého přihlášení](application-proxy-configure-single-sign-on-with-kcd.md)
- [Povolit podmíněný přístup](application-proxy-integrate-with-sharepoint-server.md)
- [Řešení potíží s proxy aplikace](application-proxy-troubleshoot.md)
