---
title: 'Azure ExpressRoute: Návrh pro zotavení po havárii'
description: Tato stránka obsahuje doporučení architektury pro zotavení po havárii při používání Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076692"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Návrh pro zotavení po havárii s privátním partnerským partnerem ExpressRoute

ExpressRoute je navržen pro vysokou dostupnost poskytovat operátor třídy privátní síť připojení k prostředkům společnosti Microsoft. Jinými slovy neexistuje žádný jediný bod selhání v cestě ExpressRoute v rámci sítě Společnosti Microsoft. Důležité informace o návrhu pro maximalizaci dostupnosti okruhu ExpressRoute naleznete v [tématu Návrh vysoké dostupnosti s ExpressRoute][HA].

Nicméně, vezmeme-li Murphyho populární pořekadlo -*pokud se něco může pokazit, bude to*-- v tomto článku se zaměřit na řešení, která jdou nad rámec selhání, které lze řešit pomocí jediného okruhu ExpressRoute. Jinými slovy, v tomto článku se podívejme na aspekty síťové architektury pro vytváření robustního připojení k back-endové síti pro zotavení po havárii pomocí geograficky redundantních obvodů ExpressRoute.

## <a name="need-for-redundant-connectivity-solution"></a>Potřeba redundantního řešení připojení

Existují možnosti a instance, kdy dojde ke zhoršení celé místní služby (ať už společnosti Microsoft, poskytovatelů síťových služeb, zákazníků nebo jiných poskytovatelů cloudových služeb). Hlavní příčinou takového regionálního dopadu na služby patří přírodní kalamita. Proto pro kontinuitu podnikání a kritické aplikace je důležité plánovat zotavení po havárii.   

Bez ohledu na to, zda spouštěte klíčové aplikace v oblasti Azure nebo v místním prostředí nebo kdekoli jinde, můžete jako web s podporou převzetí služeb při selhání použít jinou oblast Azure. Následující články se zabývají zotavení po havárii z aplikací a frontend přístup perspektivy:

- [Zotavení po havárii na podnikové úrovni][Enterprise DR]
- [Zotavení po havárii SMB pomocí Azure Site Recovery][SMB DR]

Pokud spoléháte na připojení ExpressRoute mezi místní sítí a Microsoftem pro klíčové operace, váš plán zotavení po havárii by měl také zahrnovat geograficky redundantní síťové připojení. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Výzvy spojené s používáním více okruhů ExpressRoute

Pokud propojíte stejnou sadu sítí pomocí více než jednoho připojení, zavedete paralelní cesty mezi sítěmi. Paralelní cesty, pokud nejsou správně navrženy, by mohly vést k asymetrickému směrování. Pokud máte stavové entity (například NAT, brána firewall) v cestě, asymetrické směrování může blokovat tok přenosu.  Obvykle se přes cestu soukromého partnerského vztahu ExpressRoute nedostanete napříč stavovými entitami, jako je NAT nebo firewally. Proto asymetrické směrování přes ExpressRoute privátní partnerský vztah nemusí nutně blokovat tok provozu.
 
Pokud však vyvažujete zatížení napříč geograficky redundantními paralelními cestami, bez ohledu na to, zda máte stavové entity nebo ne, došlo by k nekonzistentnímu výkonu sítě. V tomto článku pojďme diskutovat o tom, jak řešit tyto problémy.

## <a name="small-to-medium-on-premises-network-considerations"></a>Malé a střední místní síťové aspekty

Podívejme se na příklad sítě znázorněné v následujícím diagramu. V příkladu geograficky redundantní ExpressRoute připojení je vytvořenmezi contoso je místní umístění a Contoso virtuální síť v oblasti Azure. V diagramu plná zelená čára označuje preferovanou cestu (přes ExpressRoute 1) a tečkovaná představuje pohotovostní cestu (přes ExpressRoute 2).

[![1]][1]

Při navrhování připojení ExpressRoute pro zotavení po havárii je třeba zvážit:

- použití georedundantních obvodů ExpressRoute
- používání různých sítí poskytovatelů služeb pro různé okruhy ExpressRoute
- návrh každého okruhu ExpressRoute pro [vysokou dostupnost][HA]
- ukončení různého okruhu ExpressRoute v jiném umístění v síti zákazníka

Ve výchozím nastavení, pokud inzerujete trasy identicky přes všechny cesty ExpressRoute, Azure bude zatížení vyvažování místně vázaný provoz přes všechny cesty ExpressRoute pomocí stejné náklady vícecestné (ECMP) směrování.

Nicméně, s geo-redundantní ExpressRoute obvody musíme vzít v úvahu různé výkony sítě s různými síťovými cestami (zejména pro latenci sítě). Chcete-li získat konzistentnější výkon sítě během normálního provozu, můžete upřednostňovat okruh ExpressRoute, který nabízí minimální latenci.

Můžete ovlivnit Azure preferovat jeden okruh ExpressRoute před jiným pomocí jedné z následujících technik (jsou uvedeny v pořadí účinnosti):

- inzerování konkrétnější trasy přes preferovaný okruh ExpressRoute ve srovnání s jinými okruhy ExpressRoute
- konfigurace vyšší hmotnosti připojení pro připojení, které propojuje virtuální síť s preferovaným okruhem ExpressRoute
- inzerování tras přes méně preferovaný okruh ExpressRoute s delší cestou AS (předvyřiťování cesty AS)

### <a name="more-specific-route"></a>Konkrétnější trasa

Následující diagram znázorňuje ovlivnění výběru cesty ExpressRoute pomocí konkrétnější inzerování trasy. V ilustrovaném příkladu contoso místní /24 ROZSAH IP je inzerován jako dva /25 rozsahy adres prostřednictvím upřednostňované cesty (ExpressRoute 1) a jako /24 prostřednictvím pohotovostní cesty (ExpressRoute 2).

[![2]][2]

Vzhledem k tomu, že /25 je konkrétnější, ve srovnání s /24, Azure by odeslat provoz určený ch10.1.11.0/24 přes ExpressRoute 1 v normálním stavu. Pokud obě připojení ExpressRoute 1 přejít dolů, pak virtuální síť uvidí 10.1.11.0/24 trasa reklamy pouze přes ExpressRoute 2; a proto se v tomto stavu selhání používá pohotovostní obvod.

### <a name="connection-weight"></a>Hmotnost připojení

Následující snímek obrazovky znázorňuje konfiguraci hmotnosti připojení ExpressRoute přes portál Azure.

[![3]][3]

Následující diagram znázorňuje ovlivnění výběru cesty ExpressRoute pomocí tloušťky připojení. Výchozí hmotnost připojení je 0. V níže uvedeném příkladu je hmotnost připojení expressroute 1 nakonfigurována jako 100. Když virtuální síť obdrží předponu trasy inzerované prostřednictvím více než jeden okruh ExpressRoute, virtuální síť bude upřednostňovat připojení s nejvyšší hmotností.

[![4]][4]

Pokud obě připojení ExpressRoute 1 přejít dolů, pak virtuální síť uvidí 10.1.11.0/24 trasa reklamy pouze přes ExpressRoute 2; a proto se v tomto stavu selhání používá pohotovostní obvod.

### <a name="as-path-prepend"></a>Předchlaž cesty AS

Následující diagram znázorňuje ovlivnění výběru cesty ExpressRoute pomocí předchlazené cesty AS. V diagramu inzerování trasy přes ExpressRoute 1 označuje výchozí chování eBGP. Při inzerování trasy přes ExpressRoute 2 je asn místní sítě navíc předřazena na cestě AS trasy. Pokud je stejná trasa přijata prostřednictvím více okruhů ExpressRoute, podle procesu výběru trasy eBGP, virtuální síť upřednostňuje trasu s nejkratší cestou AS. 

[![5]][5]

Pokud obě připojení ExpressRoute 1 přejít dolů, pak virtuální síť uvidí 10.1.11.0/24 trasa reklamy pouze přes ExpressRoute 2. V důsledku toho by se delší cesta as stala irelevantní. Proto by se v tomto stavu selhání používal pohotovostní obvod.

Pomocí některé z technik, pokud ovlivníte Azure upřednostnit jeden z vašich ExpressRoute před ostatními, musíte také zajistit, že místní síť také přednost stejné expressroute cestu pro azure vázaný provoz, aby se zabránilo asymetrické toky. Obvykle hodnota místní předvolby se používá k ovlivnění místní sítě přednost jeden okruh ExpressRoute před ostatními. Místní předvolba je interní metrika Protokolu BGP (iBGP). Upřednostňuje se trasa BGP s nejvyšší místní hodnotou předvoleb.

> [!IMPORTANT]
> Pokud používáte určité okruhy ExpressRoute jako pohotovostní režim, je třeba je aktivně spravovat a pravidelně testovat operaci převzetí služeb při selhání. 
> 

## <a name="large-distributed-enterprise-network"></a>Velká distribuovaná podniková síť

Pokud máte velkou distribuovanou podnikovou síť, budete pravděpodobně mít více okruhů ExpressRoute. V této části se podívejme, jak navrhnout zotavení po havárii pomocí aktivní aktivní ExpressRoute obvody, bez nutnosti další pohotovostní obvody. 

Podívejme se na příklad znázorněný v následujícím diagramu. V příkladu má Contoso dvě místní umístění připojená ke dvěma nasazením Contoso IaaS ve dvou různých oblastech Azure prostřednictvím okruhů ExpressRoute ve dvou různých partnerských umístěních. 

[![6]][6]

Způsob, jakým jsme architekt zotavení po havárii má vliv na to, jak mezi regionální přes umístění (region1/region2 na location2/location1) provoz je směrován. Podívejme se na dvě různé architektury katastrof, které směruje přes oblast umístění provozu odlišně.

### <a name="scenario-1"></a>Scénář 1

V prvním scénáři navrhneme zotavení po havárii tak, aby veškerý provoz mezi oblastí Azure a místní sítí tok přes místní okruh ExpressRoute v ustáleném stavu. Pokud se místní okruh ExpressRoute nezdaří, použije se vzdálený okruh ExpressRoute pro všechny toky přenosů mezi Azure a místní sítí.

Scénář 1 je znázorněno v následujícím diagramu. V diagramu zelené čáry označují cesty pro tok provozu mezi virtuální sítí 1 a místnísítě. Modré čáry označují cesty pro tok provozu mezi virtuální sítí 2 a místními sítěmi. Plné čáry označují požadovanou cestu v ustáleném stavu a přerušované čáry označují trasu provozu v selhání odpovídajícího okruhu ExpressRoute, který nese tok provozu v ustáleném stavu. 

[![7]][7]

Scénář můžete navrhovat pomocí tloušťky připojení k ovlivnění virtuálních sítí, abyste upřednostňovali připojení k místnímu umístění partnerského vztahu ExpressRoute pro místní provoz vázaný na síť. Chcete-li řešení dokončit, musíte zajistit symetrický tok zpětného provozu. V relaci iBGP mezi směrovači Protokolu BGP (na kterých jsou obvody ExpressRoute ukončeny na místní straně) můžete použít místní předvolbu, abyste dali přednost okruhu ExpressRoute. Řešení je znázorněno na následujícím obrázku. 

[![8]][8]

### <a name="scenario-2"></a>Scénář 2

Scénář 2 je znázorněno v následujícím diagramu. V diagramu zelené čáry označují cesty pro tok provozu mezi virtuální sítí 1 a místnísítě. Modré čáry označují cesty pro tok provozu mezi virtuální sítí 2 a místními sítěmi. V ustáleném stavu (plné čáry v diagramu) veškerý provoz mezi virtuálními sítěmi a místními umístěními točí přes páteř ní microsoftu z větší části a protéká propojením mezi místními umístěními pouze ve stavu selhání (tečkované čáry diagramu) expressroute.

[![9]][9]

Řešení je znázorněno na následujícím obrázku. Jak je znázorněno na obrázku, můžete navrhovat scénář buď pomocí konkrétnější trasy (možnost 1) nebo PŘEDchlazení cesty AS (možnost 2) ovlivnit výběr cesty virtuální sítě. Chcete-li ovlivnit místní výběr tras sítě pro provoz vázaný na Azure, musíte nakonfigurovat propojení mezi místním umístěním jako méně vhodnější. Howe nakonfigurujete propojení jako vhodnější závisí na směrovací protokol používaný v rámci místní sítě. Místní předvolby můžete použít s iBGP nebo metrikou s IGP (OSPF nebo IS-IS).

[![10]][10]


## <a name="next-steps"></a>Další kroky

V tomto článku jsme diskutovali o tom, jak navrhnout zotavení po havárii privátnípartnerský vztah ový vztah ExpressRoute. Následující články se zabývají zotavení po havárii z aplikací a frontend přístup perspektivy:

- [Zotavení po havárii na podnikové úrovni][Enterprise DR]
- [Zotavení po havárii SMB pomocí Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "malé až středně velké aspekty místní sítě"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "ovlivňující výběr trasy pomocí konkrétnějších tras"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "konfigurace hmotnosti připojení přes portál Azure"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "ovlivňující výběr cesty pomocí tloušťky připojení"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "ovlivňující výběr cesty pomocí předváděcí cesty AS"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "velkých distribuovaných místních síťových aspekty"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scénář 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "aktivního řešení obvodů ExpressRoute 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scénář 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "aktivních obvodů ExpressRoute řešení 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





