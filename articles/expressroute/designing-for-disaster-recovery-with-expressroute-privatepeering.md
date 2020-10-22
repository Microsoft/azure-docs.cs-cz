---
title: 'Azure ExpressRoute: návrh pro zotavení po havárii'
description: Tato stránka poskytuje doporučení pro architekturu pro zotavení po havárii při používání Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: duau
ms.openlocfilehash: 2a5730cd75ccb76d25897e9109555113f7355c2f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92202409"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Návrh pro zotavení po havárii s privátním partnerským vztahem ExpressRoute

ExpressRoute je navržená tak, aby poskytovala vysokou dostupnost k zajištění privátní síťové připojení k prostředkům Microsoftu. Jinými slovy, v ExpressRoute cestě v síti Microsoftu neexistuje jediný bod selhání. Pokyny k návrhu pro maximalizaci dostupnosti okruhu ExpressRoute najdete v tématu [navrhování pro zajištění vysoké dostupnosti pomocí ExpressRoute][HA].

Pokud se ale Murphya oblíbená pořekadlem – Pokud se něco nepovede *, může se*v tomto článku soustředit na řešení, která přesahují selhání, která se dají řešit pomocí jednoho okruhu ExpressRoute. Jinými slovy, v tomto článku se podíváme na požadavky architektury sítě, které vám pomůžou vytvářet robustní back-end síť pro zotavení po havárii pomocí geograficky redundantních okruhů ExpressRoute.

>[!NOTE]
>Koncepty popsané v tomto článku platí stejně, když je v rámci virtuální sítě WAN nebo mimo ni vytvořen okruh ExpressRoute.
>

## <a name="need-for-redundant-connectivity-solution"></a>Nutné řešení redundantního připojení

Existují možnosti a instance, ve kterých se snižuje výkon celé místní služby (to je to, že poskytovatelé Microsoftu, poskytovatelé síťových služeb, zákazníci nebo jiní poskytovatelé cloudových služeb). Hlavní příčina tohoto dopadu na regionální služby zahrnuje přirozené Calamity. Proto je pro zajištění kontinuity podnikových a důležitých aplikací důležité naplánovat zotavení po havárii.   

Bez ohledu na to, jestli vaše nejdůležitější aplikace spouštíte v oblasti Azure nebo v místním prostředí nebo kdekoli jinde, můžete jako web pro převzetí služeb při selhání použít jinou oblast Azure. Následující články řeší zotavení po havárii z aplikací a perspektiv přístupu na front-end:

- [Zotavení po havárii na podnikové úrovni][Enterprise DR]
- [Zotavení po havárii SMB pomocí Azure Site Recovery][SMB DR]

Pokud jste se spoléhali na ExpressRoute konektivitu mezi vaší místní sítí a Microsoftem pro důležité operace, měl by váš plán zotavení po havárii zahrnovat taky geograficky redundantní síťové připojení. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Výzvy k používání více okruhů ExpressRoute

Při propojení stejné sady sítí pomocí více než jednoho připojení zavedete mezi sítě paralelní cesty. Paralelní směrování, pokud není správně navrženo, by mohlo vést k asymetrickému směrování. Pokud máte v cestě stavové entity (například NAT, firewall), může asymetrické směrování blokovat přenosový tok.  Obvykle se přes cestu soukromého partnerského vztahu ExpressRoute nepřijdete mezi stavové entity, jako jsou třeba NAT nebo brány firewall. Asymetrické směrování přes ExpressRoute privátní partnerské vztahy proto nutně neblokuje tok přenosů.
 
Pokud ale vyrovnáváte zatížení v rámci geograficky redundantních paralelních cest bez ohledu na to, jestli máte stavové entity, nebo ne, dojde k nekonzistentnímu výkonu sítě. V tomto článku se podíváme na to, jak tyto výzvy řešit.

## <a name="small-to-medium-on-premises-network-considerations"></a>Doporučení pro malé až střední požadavky na místní síť

Podívejme se na příklad sítě znázorněné v následujícím diagramu. V tomto příkladu je geograficky redundantní připojení ExpressRoute vytvořeno mezi místním umístěním společnosti Contoso a virtuální sítí contoso v oblasti Azure. V diagramu se plná zelená čára označuje jako upřednostňovaná cesta (přes ExpressRoute 1) a tečkovaná jedna představuje cestu k umístění (prostřednictvím ExpressRoute 2).

[![první]][1]

Pokud navrhujete ExpressRoute konektivitu pro zotavení po havárii, je třeba vzít v úvahu:

- použití geograficky redundantních okruhů ExpressRoute
- používání různých sítí poskytovatele služeb pro různé okruhy ExpressRoute
- návrh každého okruhu ExpressRoute pro zajištění [vysoké dostupnosti][HA]
- ukončení jiného okruhu ExpressRoute v jiném umístění v síti zákazníka

Ve výchozím nastavení platí, že pokud inzerujete trasy stejně jako všechny ExpressRoute cesty, Azure vyrovnává místní vázaný provoz napříč všemi ExpressRoute cestami pomocí směrování s rovnými náklady (ECMP).

U geograficky redundantních okruhů ExpressRoute ale musíme vzít v úvahu různé síťové výkony s různými síťovými cestami (zejména u latence sítě). Aby bylo možné dosáhnout větší konzistence sítě během normálního provozu, můžete chtít upřednostnit okruh ExpressRoute, který nabízí minimální latenci.

Můžete ovlivnit Azure tak, aby dával jeden okruh ExpressRoute přes jiný, a to pomocí jedné z následujících technik (uvedených v pořadí efektivity):

- inzerce konkrétnějšího směrování přes upřednostňovaný okruh ExpressRoute ve srovnání s ostatními okruhy ExpressRoute
- Konfigurace vysoké váhy připojení u připojení, které propojuje virtuální síť s preferovaným okruhem ExpressRoute
- inzerce tras přes méně upřednostňovaný okruh ExpressRoute s delším využitím cesty (jako předřazení cesty)

### <a name="more-specific-route"></a>Konkrétnější trasa

Následující diagram ilustruje vliv výběru cest ExpressRoute pomocí konkrétnější inzerce tras. V následujícím příkladu je místní/24 IP rozsah společnosti Contoso inzerován jako dva/25 rozsahů adres přes upřednostňovanou cestu (ExpressRoute 1) a jako/24 prostřednictvím cesty (ExpressRoute 2).

[![odst]][2]

Vzhledem k tomu, že/25 je přesnější, v porovnání s/24, Azure odešle provoz určený k 10.1.11.0/24 prostřednictvím ExpressRoute 1 v normálním stavu. Pokud připojení ExpressRoute 1 přestanou platit, pak síť VNet uvidí trasovou reklamu 10.1.11.0/24 pouze prostřednictvím ExpressRoute 2; Proto se v tomto stavu selhání používá pohotovostní okruh.

### <a name="connection-weight"></a>Váha připojení

Následující snímek obrazovky ukazuje konfiguraci váhy ExpressRoute připojení prostřednictvím Azure Portal.

[![1]][3]

Následující diagram ilustruje vliv výběru cesty ExpressRoute pomocí váhy připojení. Výchozí váha připojení je 0. V následujícím příkladu je váha připojení pro ExpressRoute 1 nakonfigurovaná jako 100. Když virtuální síť přijme předponu trasy inzerovanou přes více než jeden okruh ExpressRoute, virtuální síť bude upřednostňovat připojení s nejvyšší váhou.

[![4]][4]

Pokud připojení ExpressRoute 1 přestanou platit, pak síť VNet uvidí trasovou reklamu 10.1.11.0/24 pouze prostřednictvím ExpressRoute 2; Proto se v tomto stavu selhání používá pohotovostní okruh.

### <a name="as-path-prepend"></a>Jako předřadit jako cestu

Následující diagram ilustruje vliv výběru cesty ExpressRoute pomocí jako předřazení cesty. V diagramu indikuje inzerce trasy přes ExpressRoute 1 výchozí chování eBGP. Na trase inzerce přes ExpressRoute 2 se v cestě k této trase na cestě k místní síti přiřadí také číslo ASN. Pokud je stejná trasa přijata prostřednictvím několika okruhů ExpressRoute, na základě procesu výběru trasy eBGP, virtuální síť upřednostňuje směrování s nejkratší cestou. 

[![čl]][5]

Pokud se obě připojení ExpressRoute 1 rozstanou, pak síť VNet uvidí oznámení trasy 10.1.11.0/24 jenom prostřednictvím ExpressRoute 2. Postupně by měl být nepodstatný. Proto by byl pohotovostní okruh použit v tomto stavu selhání.

Pokud budete chtít, aby Azure při použití některé z těchto postupů dával přednost jednomu z vašich ExpressRoute, musíte taky zajistit, aby místní síť také dávala stejnou ExpressRoute cestu pro přenos dat na Azure, aby se předešlo asymetrickým tokům. Lokální hodnota předvolby se obvykle používá k ovlivnění místní sítě a upřednostňuje jeden okruh ExpressRoute přes jiné. Lokální preference je interní metrika protokolu BGP (iBGP). Upřednostňuje se trasa protokolu BGP s nejvyšší hodnotou místní předvolby.

> [!IMPORTANT]
> Pokud používáte určité okruhy ExpressRoute jako samostatné, musíte je aktivně spravovat a pravidelně testovat operace převzetí služeb při selhání. 
> 

## <a name="large-distributed-enterprise-network"></a>Velká distribuovaná podniková síť

Pokud máte rozsáhlou distribuovanou podnikovou síť, pravděpodobně budete mít více okruhů ExpressRoute. V této části se podíváme na návrh zotavení po havárii pomocí okruhů aktivní-aktivní ExpressRoute, aniž byste museli potřebovat další obvody. 

Pojďme považovat příklad znázorněný v následujícím diagramu. V tomto příkladu má společnost Contoso dvě místní umístění připojená ke dvěma různým nasazením contoso IaaS ve dvou různých oblastech Azure prostřednictvím ExpressRoute okruhů ve dvou různých umístěních partnerských vztahů. 

[![6]][6]

Způsob, jakým architekt zotavení po havárii má vliv na směrování provozu mezi různými oblastmi (Region1/region2 a Location2/location1). Pojďme se považovat za dvě různé architektury havárií, které směrují provoz mezi oblastmi a různými oblastmi.

### <a name="scenario-1"></a>Scénář 1

V prvním scénáři navrhneme zotavení po havárii tak, aby veškerý provoz mezi oblastí Azure a místní sítí pokračoval prostřednictvím místního okruhu ExpressRoute v rovnovážném stavu. Pokud se místní okruh ExpressRoute nepovede, použije se vzdálený okruh ExpressRoute pro všechny přenosy dat mezi Azure a místní sítí.

Scénář 1 je znázorněný v následujícím diagramu. V diagramu zelené čáry označují cesty pro přenos toků mezi VNet1 a místními sítěmi. Modré čáry označují cesty pro přenos toků mezi VNet2 a místními sítěmi. Plné čáry označují požadovanou cestu v ustáleném stavu a přerušované čáry označují cestu provozu při selhání odpovídajícího okruhu ExpressRoute, který přenáší tok přenosů v ustáleném stavu. 

[![čl]][7]

Scénář můžete rozpravit pomocí váhy připojení, aby se virtuální sítěy preferované připojení k umístění místních partnerských vztahů ExpressRoute pro provoz vázaný na místní síť. K dokončení řešení je potřeba zajistit symetrický tok zpětného provozu. V relaci iBGP můžete použít místní preference mezi směrovači BGP (na kterých jsou ExpressRoute okruhy ukončeny na místní straně) a upřednostnit okruh ExpressRoute. Řešení je znázorněno v následujícím diagramu. 

[![8]][8]

### <a name="scenario-2"></a>Scénář 2

Scénář 2 je znázorněn v následujícím diagramu. V diagramu zelené čáry označují cesty pro přenos toků mezi VNet1 a místními sítěmi. Modré čáry označují cesty pro přenos toků mezi VNet2 a místními sítěmi. V ustáleném stavu (pevné čáry v diagramu) se veškerý provoz mezi virtuální sítě a místními umístěními vede přes páteřní síť Microsoftu a natéká mezi propojeními mezi místními umístěními pouze ve stavu selhání (tečkované čáry v diagramu) ExpressRoute.

[![9]][9]

Řešení je znázorněno v následujícím diagramu. Jak je znázorněno, můžete scénář navrhovat buď pomocí konkrétnější trasy (možnost 1), nebo jako předřazení AS-Path (možnost 2) pro ovlivnění výběru cest virtuální sítě. Aby bylo možné ovlivnit místní síťové směrování pro přenos dat na pracovišti Azure, je nutné nakonfigurovat vzájemné propojení mezi místním umístěním, a to tak, aby bylo možné méně preferovat. Howa nakonfigurujete propojení propojení tak, jak upřednostňujete, závisí na směrovacím protokolu používaném v místní síti. Můžete použít místní preference s iBGP nebo metrikou s IGP (OSPF nebo IS-IS).

[![10pruhový]][10]


## <a name="next-steps"></a>Další kroky

V tomto článku jsme probrali, jak navrhnout zotavení po havárii privátního partnerského vztahu ExpressRoute okruhu. Následující články řeší zotavení po havárii z aplikací a perspektiv přístupu na front-end:

- [Zotavení po havárii na podnikové úrovni][Enterprise DR]
- [Zotavení po havárii SMB pomocí Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "malé až středně velké požadavky na místní síť"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "vliv výběru cesty pomocí konkrétnějších tras"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Konfigurace váhy připojení prostřednictvím Azure Portal"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "výběr cesty s použitím váhy připojení"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "vliv výběru cesty s použitím jako předřazení jako cesty"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "velkých distribuovaných místních síťových požadavků"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png. "scénář 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "ExpressRoutech okruhů aktivní-aktivní – řešení 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scénář 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "řešení ExpressRoute okruhů aktivních – aktivní 2"

<!--Link References-->
[HA]: ./designing-for-high-availability-with-expressroute.md
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: ./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: ./expressroute-optimize-routing.md#solution-use-as-path-prepending