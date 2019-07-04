---
title: Navrhování pro zotavení po havárii s využitím Azure ExpressRoute | Dokumentace Microsoftu
description: Tato stránka obsahuje architektury doporučení pro zotavení po havárii pomocí Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: cf2b4e385de901254fde3c3d3e807feda98d5b41
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466068"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Navrhování pro zotavení po havárii pomocí privátního partnerského vztahu ExpressRoute

ExpressRoute je navržené pro zajištění vysoké dostupnosti a tím zajistit dopravce připojení privátní sítě na podnikové úrovni k prostředkům společnosti Microsoft. Jinými slovy neexistuje žádný jediný bod selhání v cestě ExpressRoute v rámci sítě Microsoftu. Aspekty návrhu pro maximalizaci dostupnosti okruhu ExpressRoute, naleznete v tématu [navrhování pro vysokou dostupnost s využitím ExpressRoute][HA].

Ale trvá Murphy od oblíbených adage –*Pokud nic můžete zvrtne, bude*– v úvahu, v tomto článku nám umožní soustředit se na řešení, která přesahují chyby, které se dají řešit pomocí jednoho okruhu ExpressRoute. Jinými slovy v tomto článku Podívejme se na důležité informace o architektuře síťové pro vytvoření robustního back-end připojení k síti pro zotavení po havárii pomocí geograficky redundantního okruhy ExpressRoute.

## <a name="need-for-redundant-connectivity-solution"></a>Potřebu řešení pro redundantní připojení

Existují možnosti a instance, ve kterém získá snížený výkon celé oblasti služby (třeba které síti poskytovatele služeb, zákazníka nebo dalších poskytovatelů cloudových služeb společnosti Microsoft,). Původní příčinu těchto regionálních služeb široký dopad patří fyzická calamity. Pro provozní kontinuitu a zásadně důležité podnikové aplikace je tedy důležité mít plán pro zotavení po havárii.   

Bez ohledu na to, jestli používáte nepostradatelné aplikace v oblasti Azure nebo v místním nebo kdekoli jinde můžete použít jiné oblasti Azure jako lokalitu pro převzetí služeb při selhání. Následující články adresy zotavení po havárii z aplikací a perspektivy přístup front-endu:

- [Zotavení po havárii na podnikové úrovni][Enterprise DR]
- [Zotavení po havárii SMB pomocí Azure Site Recovery][SMB DR]

Pokud se spoléháte na připojení ExpressRoute mezi místní sítí a Microsoftem pro zásadně důležité operace, plán zotavení po havárii by měl obsahovat také geograficky redundantní síťové připojení. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Obtíže spojené s pomocí víc okruhů ExpressRoute

Připojit stejnou sadu sítím pomocí více než jedno připojení zavádíte paralelní cesty mezi sítěmi. Paralelní cesty, když není správně navržený, může vést k asymetrické směrování. Pokud máte v cestě stavové entity (například NAT, brána firewall), asymetrické směrování by mohla blokovat tok provozu.  Obvykle za cestou soukromého partnerského vztahu ExpressRoute nebude narazíte na stavové entit, jako je například NAT nebo branami firewall. Proto asymetrické směrování přes privátní partnerský vztah ExpressRoute nedochází k blokování nutně tok provozu.
 
Nicméně pokud můžete vyrovnávat zatížení provozu mezi geograficky redundantní cesty paralelní, bez ohledu na to, zda je třeba stavové entity nebo Ne, neprovádějí nekonzistentní síťový výkon. V tomto článku Pojďme se na tom, jak tyto problémy.

## <a name="small-to-medium-on-premises-network-considerations"></a>Malé a střední místní síťové požadavky

Pojďme se podívat sítě příklad znázorněný v následujícím diagramu. V tomto příkladu je geograficky redundantní připojení ExpressRoute navázat mezi místním umístěním společnosti Contoso a virtuální síť společnosti Contoso v oblasti Azure. V diagramu plná zelená čára označuje stane upřednostňovanou cestou (přes ExpressRoute 1) a tečkovaná ten představuje cestu pohotovostní (přes ExpressRoute 2).

[![1]][1]

Při navrhování připojení ExpressRoute pro zotavení po havárii, je potřeba zvážit:

- pomocí geograficky redundantního okruhy ExpressRoute
- používat různé služby poskytovatele sítí pro jiný okruh ExpressRoute
- Každý okruh ExpressRoute pro navrhování [vysoké dostupnosti][HA]
- Ukončuje se jiný okruh ExpressRoute do jiného umístění v síti zákazníka

Ve výchozím nastavení Pokud jste inzerování tras, stejně jako u všech cest ExpressRoute, Azure bude provoz vázaný na místě – nástroj pro vyrovnávání zatížení mezi všechny cesty ExpressRoute používající směrování na náklady na stejné více cest (ECMP).

Nicméně s geograficky redundantní okruhy ExpressRoute potřebujeme brát v úvahu výkonů jinou síť s jinou síť cesty (zejména latence sítě). Chcete-li získat více konzistentní výkon sítě při normálním provozu, můžete chtít preferovali okruh ExpressRoute, který nabízí minimální latenci.

Můžete ovlivnit Azure, aby preferovali jeden okruh ExpressRoute přes jiný pomocí jedné z následujících postupů (uvedené v pořadí efektivitu):

- inzerování konkrétnější trasu upřednostňovanou okruhu ExpressRoute ve srovnání s jiné okruhy ExpressRoute
- konfigurace vyšší váhu připojení na připojení, které odkazuje virtuální síť na preferované okruh ExpressRoute
- inzerování tras méně upřednostňované okruhu ExpressRoute s delší cestu AS (jak předřaďte cesta)

### <a name="more-specific-route"></a>Konkrétnější trasu

Následující diagram znázorňuje vliv cestu výběr ExpressRoute pomocí konkrétnější inzerování tras. V tomto příkladu ilustrované Contoso místní/24 rozsah IP adres, která je ohlášena jako rozsahy adres dvě /25 prostřednictvím stane upřednostňovanou cestou (ExpressRoute 1) a jako /24 prostřednictvím cesty pohotovostní (ExpressRoute 2).

[![2]][2]

Protože /25 je specifičtější, ve srovnání s /24, Azure bude posílat provoz směřující do 10.1.11.0/24 přes ExpressRoute 1 do normálního stavu. Pokud připojení ExpressRoute 1, přestanou fungovat, pak virtuální síť zobrazoval inzerování tras 10.1.11.0/24 jenom přes ExpressRoute 2; a proto pohotovostní okruh se používá v tomto stavu selhání.

### <a name="connection-weight"></a>Váhy připojení

Nakonfigurujete váhy připojení ExpressRoute pomocí webu Azure portal je znázorněný na následujícím snímku obrazovky.

[![3]][3]

Následující diagram znázorňuje vliv cestu výběr ExpressRoute pomocí váhy připojení. Výchozí váhy připojení je 0. V následujícím příkladu váhy připojení ExpressRoute 1 nastavená na 100. Pokud virtuální síť obdrží předponu trasy, inzerované prostřednictvím více než jeden okruh ExpressRoute, virtuální síť přednost připojení v nejvyšší váhou.

[![4]][4]

Pokud připojení ExpressRoute 1, přestanou fungovat, pak virtuální síť zobrazoval inzerování tras 10.1.11.0/24 jenom přes ExpressRoute 2; a proto pohotovostní okruh se používá v tomto stavu selhání.

### <a name="as-path-prepend"></a>JAKO cestu předřaďte

Následující diagram znázorňuje vliv cestu výběr ExpressRoute pomocí předřaďte cestu. V diagramu inzerování tras přes ExpressRoute 1 označuje výchozí chování eBGP. Na inzerování tras přes ExpressRoute 2 ASN v místní síti se přidá jako předpona kromě na trasy jako cestu. Při přijetí stejné směrování přes víc okruhů ExpressRoute, jeden proces výběru eBGP trasy, virtuální sítě raději směrování s nejkratší jako cestu. 

[![5]][5]

Pokud připojení ExpressRoute 1, přestanou fungovat, virtuální síť zobrazoval inzerování tras 10.1.11.0/24 jenom přes ExpressRoute 2. Consequentially tím déle, protože cesta by se mohla stát relevantní. Proto se použije v tomto stavu selhání pohotovostní okruhu.

Pomocí kteréhokoli z technik, pokud ovlivnit Azure preferovat jeden přes ExpressRoute přes ostatní, je také potřeba zajistit v místní síti také dáváte přednost stejnou cestu k ExpressRoute pro Azure vázat provozu, aby asymetrického toky. Hodnotu local preference obvykle se používá k ovlivnění místní sítě k preferovali jeden okruh ExpressRoute prostřednictvím jiné. Místní předvoleb je interní metriky protokolu BGP (iBGP). Trasa protokolu BGP s nejvyšší hodnotu local preference je upřednostňována.

> [!IMPORTANT]
> Použijete-li jako pohotovostní určité okruhy ExpressRoute, budete muset aktivně spravovat a pravidelně testovat převzetí služeb při selhání. 
> 

## <a name="large-distributed-enterprise-network"></a>Velké distribuované rozlehlé sítě

Pokud máte velký podnik distribuované sítě, budete pravděpodobně máte víc okruhů ExpressRoute. V této části se podíváme se na postup návrhu zotavení po havárii pomocí typu aktivní aktivní okruhy ExpressRoute, bez nutnosti dalších neslo okruhy. 

Pojďme se na příklad znázorněný v následujícím diagramu. V tomto příkladu Contoso má dva místními umístěními připojené k dvě nasazení IaaS společnosti Contoso ve dvou různých oblastech Azure prostřednictvím okruhů ExpressRoute ve dvou různých umístění partnerského vztahu. 

[![6]][6]

Jak stojí zotavení po havárii má vliv na různé oblasti pro různé umístění (region1/region2 k location2/location1) směrování provozu. Pojďme se na dva různé po havárii architektury, které směruje provoz mezi místo oblasti odlišně.

### <a name="scenario-1"></a>Scénář 1

Do prvního scénáře můžeme navrhnout zotavení po havárii tak, aby veškerý provoz mezi Azure oblasti a místní sítí procházet skrz místnímu okruhu ExpressRoute do stabilního stavu. Pokud místní okruh ExpressRoute selže, pak ke vzdálenému okruhu ExpressRoute se používá pro všechny toky přenosů mezi Azure a místní síť.

Scénář 1 je znázorněné v následujícím diagramu. V diagramu označení zelené čáry cesty pro tok přenosů mezi ze sítě VNet1 a místní sítí. Modré čáry označují cesty pro tok přenosů mezi sítě VNet2 a místní sítí. Plné čáry označují požadovanou cestu do stabilního stavu a přerušované čáry označují provoz cesta selhání odpovídající okruh ExpressRoute, které má tok provozu stabilního stavu. 

[![7]][7]

Můžete navrhovat scénář s využitím váhy připojení k ovlivnění virtuálních sítí s dáváte přednost, že provoz vázaný připojení k místní umístění partnerského vztahu ExpressRoute pro místní síť. K dokončení řešení, je potřeba zajistit tok symetrické zpětný provoz. Můžete použít místní preference iBGP relace mezi vašimi směrovači protokolu BGP (na kterých okruhy ExpressRoute jsou ukončeny na straně místní) k preferovali okruh ExpressRoute. Toto řešení je znázorněn v následujícím diagramu. 

[![8]][8]

### <a name="scenario-2"></a>Scénář 2

Scénář 2 je znázorněn v následujícím diagramu. V diagramu označení zelené čáry cesty pro tok přenosů mezi ze sítě VNet1 a místní sítí. Modré čáry označují cesty pro tok přenosů mezi sítě VNet2 a místní sítí. V stálé (plné čáry v diagramu) všechny přenosy mezi virtuálními sítěmi a místními umístěními flow přes páteřní infrastrukturu Microsoftu ve většině případů a prochází přes propojení mezi místními umístěními pouze ve stavu selhání (tečkované čáry v diagram) z ExpressRoute.

[![9]][9]

Toto řešení je znázorněn v následujícím diagramu. Jak je znázorněno, můžete navrhovat scénář buď pomocí konkrétnější trasu (možnost 1) nebo AS path předřaďte (možnost 2) a ovlivnit výběr cesty virtuální sítě. K ovlivnění výběr směrování místní sítě vázané provozu Azure, potřebujete nakonfigurovat propojení mezi místním umístěním jako méně vhodnější. Konfigurace propojení odkazu jako vhodnější Howe závisí na směrovací protokol použitý v místní síti. Použít local preference s iBGP nebo metriky s IGP (protokolu OSPF nebo je je).

[![10]][10]


## <a name="next-steps"></a>Další postup

V tomto článku jsme probírali postup návrhu pro zotavení po havárii okruhu soukromého partnerského vztahu připojení ExpressRoute. Následující články adresy zotavení po havárii z aplikací a perspektivy přístup front-endu:

- [Zotavení po havárii na podnikové úrovni][Enterprise DR]
- [Zotavení po havárii SMB pomocí Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "malé a střední velikosti místní síťové požadavky"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "vliv cestu výběr pomocí konkrétnější trasy"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "konfigurace váhy připojení prostřednictvím webu Azure portal"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "vliv cestu výběr pomocí váhy připojení"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "předřaďte vliv cestu výběr pomocí jako cesta"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "velkých distribuovaných místní síťové požadavky"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scénář 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "řešení 1 okruhy ExpressRoute aktivní aktivní"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "okruhy ExpressRoute aktivní aktivní řešení 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





