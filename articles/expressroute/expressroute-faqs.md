---
title: Nejčastější dotazy – Azure ExpressRoute | Dokumenty společnosti Microsoft
description: Nejčastější dotazy k ExpresRoute obsahují informace o podporovaných službách Azure, nákladech, datech a připojeních, sla, poskytovatelích a umístěních, šířce pásma a další choulamací.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 845c53ec970777901ae8d1c0abf5032ac705d3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264918"
---
# <a name="expressroute-faq"></a>ExpressRoute – nejčastější dotazy

## <a name="what-is-expressroute"></a>Co je ExpressRoute?

ExpressRoute je služba Azure, která umožňuje vytvářet privátní připojení mezi datovými centry Microsoftu a infrastrukturou, která je ve vašich prostorách nebo v zařízení pro společné umístění. Připojení ExpressRoute neprocházejí přes veřejný Internet a nabízejí vyšší zabezpečení, spolehlivost a rychlosti s nižší latencí než typická připojení přes Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Jaké jsou výhody používání expressroute a privátních síťových připojení?

Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Nabízejí vyšší zabezpečení, spolehlivost a rychlosti s nižší a konzistentní latencí než typická připojení přes Internet. V některých případech může použití připojení ExpressRoute k přenosu dat mezi místními zařízeními a Azure přinést významné výhody nákladů.

### <a name="where-is-the-service-available"></a>Kde je služba dostupná?

Na této stránce naleznete umístění a dostupnost služby: [Partneři a umístění ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Jak se mohu pomocí expressroute připojit k Microsoftu, pokud nemám partnerství s jedním z partnerů nosiče ExpressRoute?

Můžete vybrat regionálního operátora a vytvořit připojení Ethernet do jednoho z podporovaných umístění zprostředkovatelů výměny. Potom můžete peer s Microsoft v umístění zprostředkovatele. V poslední části [partnerů a umístění ExpressRoute](expressroute-locations.md) zjistěte, zda je váš poskytovatel služeb přítomen v některém z umístění pro výměnu. Okruh ExpressRoute pak můžete objednat prostřednictvím poskytovatele služeb pro připojení k Azure.

### <a name="how-much-does-expressroute-cost"></a>Kolik stojí ExpressRoute?

Informace o cenách najdete [v podrobnostech o cenách.](https://azure.microsoft.com/pricing/details/expressroute/)

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Pokud platím za okruh ExpressRoute s danou šířkou pásma, musí mít připojení VPN, které nakupuji od svého poskytovatele síťových služeb, stejnou rychlost?

Ne. Připojení VPN libovolné rychlosti si můžete zakoupit u svého poskytovatele služeb. Vaše připojení k Azure je však omezeno na šířku pásma okruhu ExpressRoute, kterou zakoupíte.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Pokud platím za okruh ExpressRoute dané šířky pásma, mám v případě potřeby možnost vybuchnout na vyšší rychlosti?

Ano. Obvody ExpressRoute jsou konfigurovány tak, aby vám umožnily roztrhnout až dvakrát větší limit šířky pásma, který jste získali, bez dalších nákladů. Obraťte se na svého poskytovatele služeb a zjistěte, zda tuto funkci podporuje. To není na delší dobu a není zaručeno.  Pokud provoz prochází expressroute brány, šířka pásma pro sku je pevná a nelze burstable.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Můžu současně používat stejné privátní síťové připojení s virtuální sítí a dalšími službami Azure?

Ano. Okruh ExpressRoute po nastavení umožňuje přístup ke službám v rámci virtuální sítě a dalších služeb Azure současně. K virtuálním sítím se připojujete přes cestu soukromého partnerského vztahu a k dalším službám přes cestu partnerského vztahu Microsoftu.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Jak se virtuální sítě inzerují v soukromém partnerské síti ExpressRoute?

Brána ExpressRoute bude inzerovat *adresní prostor (y)* virtuální sítě Azure, nemůžete zahrnout nebo vyloučit na úrovni podsítě. Je to vždy adresní prostor virtuální sítě, který je inzerovaný. Taky pokud se používá partnerský vztah virtuální sítě a partnerská virtuální síť má povolenou "Použít vzdálenou bránu", bude inzerovaný také adresní prostor partnerské virtuální sítě.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Kolik předpon může být inzerováno z virtuální sítě do místního soukromého partnerského vztahu ExpressRoute?

Existuje maximálně 200 předpon inzerovaných v jednom připojení ExpressRoute nebo prostřednictvím partnerského vztahu virtuální sítě pomocí přenosu brány. Například pokud máte 199 adresních prostorů na jedné virtuální síti připojené k okruhu ExpressRoute, všech 199 těchto předpon bude inzerováno místně. Případně pokud máte povolenou virtuální síť pro povolení přenosu brány s 1 adresním prostorem a 150 paprskovými virtuálními sítěmi povolenými pomocí možnosti "Povolit vzdálenou bránu", virtuální síť nasazená s bránou inzeruje 151 předpon do místního prostředí.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Co se stane, když překročím limit předpony připojení ExpressRoute?

Připojení mezi okruhem ExpressRoute a bránou (a partnerskými virtuálními sítěmi pomocí přenosu brány, pokud je to možné) se vypne. Obnoví se, když již není překročen limit předpony.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Můžu filtrovat trasy přicházející z místní sítě?

Jediný způsob, jak filtrovat nebo zahrnout trasy, je na místním hraničním směrovači. Uživatelem definované trasy lze přidat do virtuální sítě ovlivnit konkrétní směrování, ale to bude statické a není součástí reklamy Protokolu BGP.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Nabízí ExpressRoute smlouvu o úrovni služeb (SLA)?

Další informace naleznete na stránce [SLA ExpressRoute.](https://azure.microsoft.com/support/legal/sla/)

## <a name="supported-services"></a>Podporované služby

ExpressRoute podporuje [tři směrovací domény](expressroute-circuit-peerings.md) pro různé typy služeb: privátní partnerský vztah, partnerský vztah Microsoftu a veřejný partnerský vztah (zastaralé).

### <a name="private-peering"></a>Soukromý partnerský vztah

**Podporovány:**

* Virtuální sítě, včetně všech virtuálních počítačů a cloudových služeb

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu

Pokud je okruh ExpressRoute povolený pro partnerský vztah Azure Microsoft, můžete přistupovat k [rozsahům veřejných IP adres používaných](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure přes okruh. Partnerský vztah Azure Microsoft bude poskytovat přístup ke službám aktuálně hostovaným v Azure (s geografickými omezeními v závislosti na sku vašeho okruhu). Chcete-li ověřit dostupnost pro konkrétní službu, můžete zkontrolovat dokumentaci pro tuto službu a zjistit, zda je pro tuto službu publikován vyhrazený rozsah. Potom vyhledejte rozsahy IP adres cílové služby a porovnejte rozsahy uvedené v [azure IP rozsahy a značky služeb – veřejný cloud XML soubor](https://www.microsoft.com/download/details.aspx?id=56519). Případně můžete otevřít lístek podpory pro dotyčnou službu pro objasnění.

**Podporovány:**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI – Dostupné prostřednictvím místní komunity Azure [najdete tady,](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) kde najdete postup, jak zjistit oblast vašeho tenanta Power BI.
* Azure Active Directory
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (komunita globálních služeb Azure)
* Veřejné IP adresy Azure pro IaaS (virtuální počítače, brány virtuálních sítí, nástroje pro vyrovnávání zatížení atd.)  
* Většina ostatních služeb Azure jsou taky podporované. Zkontrolujte přímo službu, kterou chcete použít k ověření podpory.

**Není podporováno:**

* CDN
* Azure Front Door
* Vícefaktorový ověřovací server (starší verze)
* Traffic Manager

### <a name="public-peering"></a>Veřejný partnerský vztah

Veřejný partnerský vztah byl zakázán v nových obvodech ExpressRoute. Služby Azure jsou teď dostupné v partnerské společnosti Microsoft. Pokud jste okruh, který byl vytvořen před zastaralou veřejnou partnerovou partnerta, můžete použít partnerský vztah Microsoftu nebo veřejný partnerský vztah, v závislosti na službách, které chcete.

Další informace a kroky konfigurace pro veřejný partnerský vztah naleznete v [tématu ExpressRoute public peering](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Proč se mi při konfiguraci partnerského vztahu Microsoftu zobrazuje stav Inzerované veřejné předpony jako "Ověření je potřeba"?

Společnost Microsoft ověří, zda jsou v registru směrování v Internetu přiřazeny zadané předpony inzerované veřejné a peer asn (nebo "ASN zákazníka". Pokud získáváte veřejné předpony z jiné entity a přiřazení není zaznamenáno v registru směrování, automatické ověření nebude dokončeno a bude vyžadovat ruční ověření. Pokud se automatické ověření nezdaří, zobrazí se zpráva "Ověření je potřeba".

Pokud se zobrazí zpráva "Ověření je potřeba", shromážděte dokumenty, které ukazují, že veřejné předpony jsou přiřazeny vaší organizaci entitou, která je uvedena jako vlastník předpon v registru směrování, a odešlete tyto dokumenty k ručnímu ověření otevření lístku podpory, jak je uvedeno níže.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Je dynamics 365 podporovánna ExpressRoute?

Prostředí Dynamics 365 a Common Data Service (CDS) se hostují v Azure, a proto zákazníci využívají základní podporu ExpressRoute pro prostředky Azure. Ke koncovým bodům služby se můžete připojit, pokud filtr směrovače obsahuje oblasti Azure, ve kterých jsou hostována prostředí Dynamics 365/CDS.

> [!NOTE]
> [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) **není** vyžadováno pro připojení Dynamics 365 přes Azure ExpressRoute.

## <a name="data-and-connections"></a>Data a připojení

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Existují omezení množství dat, která lze přenášet pomocí expressroute?

Nestanovíme limit množství přenosu dat. Informace [o sazbách](https://azure.microsoft.com/pricing/details/expressroute/) za šířku pásma naleznete v podrobnostech o cenách.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Jaké rychlosti připojení jsou podporovány ExpressRoute?

Podporované nabídky šířky pásma:

50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s

### <a name="which-service-providers-are-available"></a>Kteří poskytovatelé služeb jsou k dispozici?

Seznam poskytovatelů služeb a umístění najdete v [tématu Partneři a umístění ExpressRoute.](expressroute-locations.md)

## <a name="technical-details"></a>Technické podrobnosti

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Jaké jsou technické požadavky na připojení mého místního umístění k Azure?

Požadavky naleznete na [stránce s požadavky na stránce s požadavky požadavků expressroute.](expressroute-prerequisites.md)

### <a name="are-connections-to-expressroute-redundant"></a>Jsou připojení k ExpressRoute redundantní?

Ano. Každý okruh ExpressRoute má redundantní dvojici křížových připojení nakonfigurovaných tak, aby poskytovaly vysokou dostupnost.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Ztratím připojení, pokud jeden z mých odkazů ExpressRoute selže?

Připojení neztratíte, pokud se nezdaří jedno z křížových připojení. Redundantní připojení je k dispozici pro podporu zatížení vaší sítě a poskytují vysokou dostupnost okruhu ExpressRoute. Můžete navíc vytvořit okruh v jiném umístění partnerského vztahu k dosažení odolnosti na úrovni okruhu.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Jak implementovat redundanci v soukromém partnerském vztahu?

Více okruhů ExpressRoute z různých partnerských umístění lze připojit ke stejné virtuální síti a zajistit tak vysokou dostupnost v případě, že jeden okruh nebude k dispozici. Pak můžete [přiřadit vyšší váhy](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) k místnímu připojení, abyste upřednostňovali určitý obvod. Důrazně doporučujeme, aby zákazníci nastavili alespoň dva okruhy ExpressRoute, aby se zabránilo selhání jednotlivých bodů. 

Zde [here](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) naleznete návrh pro vysokou dostupnost a [zde](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) pro navrhování pro zotavení po havárii.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Jak implementuji redundanci v partnerské společnosti Microsoft?

Důrazně se doporučuje, když zákazníci používají partnerský vztah Microsoftu pro přístup k veřejným službám Azure, jako je Azure Storage nebo Azure SQL, a také zákazníci, kteří používají partnerský vztah Microsoftu pro Office 365, kteří implementují více okruhů v různých partnerských společnostech. umístění, aby se zabránilo selhání jednotlivých bodů. Zákazníci mohou inzerovat stejnou předponu na obou obvodech a použít [jako cestu před volbami](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) nebo inzerovat různé předpony k určení cesty z místního prostředí.

Podívejte se [zde](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) pro navrhování pro vysokou dostupnost.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Jak zajistím vysokou dostupnost ve virtuální síti připojené k ExpressRoute?

Vysokou dostupnost můžete dosáhnout připojením okruhů ExpressRoute v různých umístěních partnerského vztahu (například Singapur, Singapur2) k virtuální síti. Pokud jeden okruh ExpressRoute přejde dolů, připojení převezme služby při selhání do jiného okruhu ExpressRoute. Ve výchozím nastavení je provoz opouštějící virtuální síť směrován na základě směrování ECMP (Equal Cost Multi-path Routing). Můžete použít hmotnost připojení preferovat jeden obvod na jiný. Další informace naleznete [v tématu Optimalizace směrování ExpressRoute](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Jak zajistím, aby se můj provoz určený pro veřejné služby Azure, jako je Azure Storage a Azure SQL v partnerském vztahu Microsoftu nebo veřejný partnerský vztah, upřednostňoval na cestě ExpressRoute?

Musíte implementovat atribut *Místní předvolby* na směrovačích, abyste zajistili, že cesta z místního do Azure je vždy upřednostňovaná na okruhech ExpressRoute.

Další podrobnosti [naleznete zde](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) o výběru cesty Protokolu BGP a běžných konfiguracích směrovačů. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Pokud nejsem umístěn a můj poskytovatel služeb nabízí připojení point-to-point, je třeba objednat dvě fyzická připojení mezi místní sítí a microsoftem?

Pokud váš poskytovatel služeb dokáže vytvořit dva virtuální okruhy Ethernet přes fyzické připojení, potřebujete pouze jedno fyzické připojení. Fyzické připojení (například optické vlákno) je ukončeno na zařízení vrstvy 1 (L1) (viz obrázek). Dva ethernetové virtuální okruhy jsou označeny různými ID sítě VLAN, jedním pro primární okruh a jedním pro sekundární. Tato ID Sítě VLAN jsou ve vnějším ethernetovém konektoru 802.1Q. Vnitřní ethernetová hlavička 802.1Q (není zobrazena) je mapována na konkrétní [směrovací doménu ExpressRoute](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Můžu pomocí ExpressRoute rozšířit jeden ze svých virtuálních linenů do Azure?

Ne. Nepodporujeme rozšíření připojení vrstvy 2 do Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Mohu mít více než jeden okruh ExpressRoute v mém předplatném?

Ano. V předplatném můžete mít více než jeden okruh ExpressRoute. Výchozí limit je nastaven na 10. V případě potřeby můžete kontaktovat podporu společnosti Microsoft a zvýšit tak limit.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Mohu mít okruhy ExpressRoute od různých poskytovatelů služeb?

Ano. Můžete mít ExpressRoute okruhy s mnoha poskytovateli služeb. Každý okruh ExpressRoute je přidružen pouze k jednomu poskytovateli služeb. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Vidím dvě umístění peeringu ExpressRoute ve stejném metru, například Singapur a Singapur2. Které umístění partnerského vztahu mám zvolit vytvoření okruhu ExpressRoute?
Pokud váš poskytovatel služeb nabízí ExpressRoute v obou lokalitách, můžete spolupracovat se svým poskytovatelem a vybrat oba weby a nastavit ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Mohu mít více okruhů ExpressRoute ve stejném metru? Můžu je propojit se stejnou virtuální sítí?

Ano. Můžete mít více okruhů ExpressRoute se stejnými nebo různými poskytovateli služeb. Pokud metro má více umístění partnerského vztahu ExpressRoute a okruhy jsou vytvořeny v různých umístěních partnerského vztahu, můžete je propojit se stejnou virtuální sítí. Pokud jsou okruhy vytvořeny ve stejném umístění partnerského vztahu, můžete propojit až 4 okruhy se stejnou virtuální sítí.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Jak lze připojit virtuální sítě k okruhu ExpressRoute

Základní kroky jsou:

* Vytvořte okruh ExpressRoute a povolte poskytovatele služeb.
* Vy nebo zprostředkovatel musí nakonfigurovat partnerský vztah Protokolu BGP.
* Propojte virtuální síť s okruhem ExpressRoute.

Další informace naleznete v [tématu Pracovní postupy ExpressRoute pro zřizování okruhů a stavy okruhů](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Existují pro okruh ExpressRoute hranice připojení?

Ano. Článek [Partnerů a umístění ExpressRoute](expressroute-locations.md) poskytuje přehled hranic připojení okruhu ExpressRoute. Připojení pro okruh ExpressRoute je omezeno na jednu geopolitickou oblast. Konektivita může být rozšířena na příč geopolitické oblasti povolením funkce ExpressRoute premium.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Můžu propojit více než jednu virtuální síť s okruhem ExpressRoute?

Ano. Můžete mít až 10 připojení virtuálních sítí na standardníokruh ExpressRoute a až 100 na [okruhu Premium ExpressRoute](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Mám několik předplatných Azure, které obsahují virtuální sítě. Je možné připojit virtuální sítě, které jsou v samostatných předplatných, k jednomu okruhu ExpressRoute?

Ano. Můžete propojit až 10 virtuálních sítí ve stejném předplatném jako okruh nebo různé odběry pomocí jednoho okruhu ExpressRoute. Tento limit lze zvýšit povolením funkce Premium ExpressRoute.

Další informace naleznete [v tématu Sdílení okruhu ExpressRoute mezi více předplatnými](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Mám několik předplatných Azure přidružených k různým tenantům Služby Azure Active Directory nebo k registracím smlouvy Enterprise. Můžu připojit virtuální sítě, které jsou v samostatných tenantech a registrace k jednomu okruhu ExpressRoute, který není ve stejném tenantovi nebo registraci?

Ano. Oprávnění ExpressRoute může span předplatné, klienta a registrace hranice bez nutnosti další konfigurace. 

Další informace naleznete [v tématu Sdílení okruhu ExpressRoute mezi více předplatnými](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Jsou virtuální sítě připojené ke stejnému okruhu izolované od sebe navzájem?

Ne. Z hlediska směrování jsou všechny virtuální sítě propojené se stejným okruhem ExpressRoute součástí stejné domény směrování a nejsou od sebe izolované. Pokud potřebujete izolaci trasy, musíte vytvořit samostatný okruh ExpressRoute.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Mohu mít jednu virtuální síť připojenou k více než jednomu okruhu ExpressRoute?

Ano. Můžete propojit jednu virtuální síť až se čtyřmi okruhy ExpressRoute ve stejném nebo jiném umístění partnerského vztahu. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Mohu přistupovat k Internetu z mých virtuálních sítí připojených k okruhům ExpressRoute?

Ano. Pokud jste neinzerovali výchozí trasy (0.0.0.0/0) nebo předpony internetové trasy prostřednictvím relace protokolu BGP, můžete se připojit k Internetu z virtuální sítě propojené s okruhem ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Mohu blokovat připojení k internetu k virtuálním sítím připojeným k okruhům ExpressRoute?

Ano. Můžete inzerovat výchozí trasy (0.0.0.0/0) zablokovat veškeré připojení k Internetu virtuálním počítačům nasazeným v rámci virtuální sítě a směrovat veškerý provoz přes okruh ExpressRoute.

Pokud inzerujete výchozí trasy, vynutíme provoz na služby nabízené prostřednictvím partnerského vztahu Microsoftu (například úložiště Azure a SQL DB) zpět do vašich prostor. Budete muset nakonfigurovat směrovače tak, aby vracely provoz do Azure prostřednictvím cesty partnerského vztahu Microsoftu nebo přes Internet. Pokud jste povolili koncový bod služby pro službu, provoz na službu není vynuceno do vašich prostor. Provoz zůstane v rámci páteřní sítě Azure. Další informace o koncových bodech služby najdete [v tématu Koncové body služby virtuální sítě.](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Mohou virtuální sítě propojené se stejným okruhem ExpressRoute vzájemně hovořit?

Ano. Virtuální počítače nasazené ve virtuálních sítích připojených ke stejnému okruhu ExpressRoute spolu můžou komunikovat.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Mohu ve spojení s ExpressRoute používat připojení k webu k webu pro virtuální sítě?

Ano. ExpressRoute může existovat společně s sítěmi VIRTUÁLNÍCH SÍTÍ site-to-site. Viz [Konfigurace expressroute a koexistujících připojení mezi lokalitami](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Proč je ve virtuální síti přidružena veřejná IP adresa s bránou ExpressRoute?

Veřejná IP adresa se používá pouze pro interní správu a nepředstavuje ohrožení zabezpečení vaší virtuální sítě.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Existují omezení počtu tras, které mohu inzerovat?

Ano. Přijímáme až 4000 předpon tras pro soukromý partnerský vztah a 200 pro partnerský vztah Microsoftu. Tuto možnost můžete zvýšit na 10 000 tras pro soukromý partnerský vztah, pokud povolíte funkci Premium ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Existují omezení na rozsahy IP adres, které mohu inzerovat v relaci Protokolu BGP?

Nepřijímáme privátní předpony (RFC1918) pro relaci protokolu BGP partnerského vztahu Microsoftu. Přijímáme libovolnou velikost předpony (až /32) v microsoftu i soukromém partnerovi.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Co se stane, když překročím limity Protokolu BGP?

Relace protokolu BGP budou zrušeny. Budou resetovány, jakmile počet předponek klesne pod limit.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Co je doba držení protokolu BGP ExpressRoute? Dá se to upravit?

Doba držení je 180. Zprávy o udržení jsou odesílány každých 60 sekund. Jedná se o pevná nastavení na straně společnosti Microsoft, která nelze změnit. Je možné nakonfigurovat různé časovače a parametry relace Protokolu BGP budou odpovídajícím způsobem vyjednány.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Mohu změnit šířku pásma okruhu ExpressRoute?

Ano, můžete se pokusit zvýšit šířku pásma okruhu ExpressRoute na webu Azure Portal nebo pomocí prostředí PowerShell. Pokud je kapacita k dispozici na fyzickém portu, na kterém byl vytvořen okruh, vaše změna proběhne úspěšně. 

Pokud se vaše změna nezdaří, znamená to, že buď na aktuálním portu není dostatečná kapacita a potřebujete vytvořit nový okruh ExpressRoute s vyšší šířkou pásma, nebo že v tomto umístění není žádná další kapacita, v takovém případě nebudete moci zvýšit Šířky pásma. 

Budete také muset navázat s poskytovatelem připojení, abyste zajistili, že aktualizují omezení v rámci svých sítí, aby podporovali zvýšení šířky pásma. Nelze však snížit šířku pásma okruhu ExpressRoute. Musíte vytvořit nový okruh ExpressRoute s nižší šířkou pásma a odstranit starý obvod.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Jak změním šířku pásma okruhu ExpressRoute?

Šířku pásma okruhu ExpressRoute můžete aktualizovat pomocí rutiny REST API nebo PowerShell.

## <a name="expressroute-premium"></a>Prémie ExpressRoute

### <a name="what-is-expressroute-premium"></a>Co je prémiová služba ExpressRoute?

ExpressRoute premium je kolekce následujících funkcí:

* Byl zvýšen limit směrovací tabulky ze 4000 tras na 10 000 tras pro soukromý partnerský vztah.
* Zvýšený počet připojení virtuálních sítí a ExpressRoute Global Reach, které lze povolit v okruhu ExpressRoute (výchozí hodnota je 10). Další informace naleznete v tabulce [ExpressRoute Limits.](#limits)
* Připojení k Office 365
* Globální připojení přes hlavní síť společnosti Microsoft. Teď můžete propojit virtuální síť v jedné geopolitické oblasti s okruhem ExpressRoute v jiné oblasti.<br>
    **Příklady:**

    *  Virtuální síť vytvořená v Evropě – západ můžete propojit s okruhem ExpressRoute vytvořeným v Silicon Valley. 
    *  V partnerském vztahu Microsoftu jsou inzerovány předpony z jiných geopolitických oblastí tak, že se můžete připojit například k SQL Azure v Europe West z okruhu v Silicon Valley.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Kolik připojení virtuálních sítí a globálního dosahu ExpressRoute lze povolit v okruhu ExpressRoute, pokud jsem povolil prémii ExpressRoute?

V následujících tabulkách jsou uvedeny limity ExpressRoute a počet připojení virtuálních sítí a globálního dosahu služby ExpressRoute na okruh ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Jak povolím expressroute premium?

Funkce premium ExpressRoute lze povolit, když je tato funkce povolena, a lze je vypnout aktualizací stavu okruhu. Můžete povolit ExpressRoute premium v době vytvoření okruhu nebo můžete volat rutinu REST API / PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Jak zakážu prémii ExpressRoute?

Službu ExpressRoute premium můžete zakázat voláním rutiny REST API nebo PowerShellu. Před zakázáním služby ExpressRoute premium se musíte ujistit, že jste změnili velikost připojení, abyste splnili výchozí limity. Pokud se využití škáluje nad výchozí limity, požadavek na zakázání expresní trasy se nezdaří.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Mohu si vybrat funkce, které chci, ze sady prémiových funkcí?

Ne. Nemůžete vybrat funkce. Všechny funkce povolujeme, když zapnete expressroute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Kolik stojí prémiová služba ExpressRoute?

Informace [o nákladech naleznete v podrobnostech o cenách.](https://azure.microsoft.com/pricing/details/expressroute/)

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Platím za prémii ExpressRoute kromě standardních poplatků ExpressRoute?

Ano. Poplatky za prémii ExpressRoute se účtují nad poplatky za okruh y ExpressRoute a poplatky požadované poskytovatelem připojení.

## <a name="expressroute-local"></a>ExpressRoute Místní
### <a name="what-is-expressroute-local"></a>Co je ExpressRoute Místní?
ExpressRoute Local je skladová položka okruhu ExpressRoute, kromě standardní skladové položky a premium skladové položky. Klíčovou vlastností Local je, že místní okruh v umístění partnerského vztahu ExpressRoute umožňuje přístup pouze do jedné nebo dvou oblastí Azure ve stejném metru nebo v jeho blízkosti. Naproti tomu okruh Standard umožňuje přístup do všech oblastí Azure v geopolitické oblasti a okruh Premium do všech oblastí Azure po celém světě. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Jaké jsou výhody ExpressRoute Local?
I když je třeba zaplatit odchozí přenos dat pro váš okruh Standard nebo Premium ExpressRoute, neplatíte odchozí přenos dat samostatně pro váš okruh ExpressRoute Local. Jinými slovy, cena ExpressRoute Local zahrnuje poplatky za přenos dat. ExpressRoute Local je ekonomičtější řešení, pokud máte obrovské množství dat k přenosu a můžete přenést data přes soukromé připojení do umístění partnerského vztahu ExpressRoute v blízkosti požadovaných oblastí Azure. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Jaké funkce jsou k dispozici a co nejsou na ExpressRoute Local?
Ve srovnání s okruhem Standard ExpressRoute má místní okruh stejnou sadu funkcí s výjimkou:
* Rozsah přístupu k oblastem Azure, jak je popsáno výše
* Globální dosah ExpressRoute není k dispozici v místním

ExpressRoute Local má také stejná omezení pro prostředky (např. počet virtuálních sítí na okruh) jako standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Kde je ExpressRoute Local k dispozici a které oblasti Azure je každý partnerský vztah umístění mapovány?
ExpressRoute Local je k dispozici v partnerských umístěních, kde jedna nebo dvě oblasti Azure jsou blízko. Není k dispozici v umístění partnerského vztahu, kde v tomto státě nebo provincii nebo zemi neexistuje žádná oblast Azure. Přesné mapování naleznete [na stránce Umístění](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>ExpressRoute pro Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Jak vytvořím okruh ExpressRoute pro připojení ke službám Office 365?

1. Zkontrolujte [stránku požadavků ExpressRoute a](expressroute-prerequisites.md) ujistěte se, že splňujete požadavky.
2. Chcete-li zajistit splnění potřeb připojení, zkontrolujte seznam poskytovatelů služeb a umístění v článku [partnerů a umístění ExpressRoute.](expressroute-locations.md)
3. Naplánujte si požadavky na kapacitu na [recenze Plánování sítě a optimalizace výkonu pro Office 365](https://aka.ms/tune/).
4. Podle pokynů uvedených v pracovních postupech nastavte konektivitu [pracovních postupů ExpressRoute pro zřizování okruhů a stavy okruhů](expressroute-workflows.md).

> [!IMPORTANT]
> Ujistěte se, že jste při konfiguraci připojení ke službám Office 365 povolili doplněk ExpressRoute premium.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>Podporují moje stávající okruhy ExpressRoute připojení ke službám Office 365?

Ano. Váš stávající okruh ExpressRoute lze nakonfigurovat tak, aby podporoval připojení ke službám Office 365. Ujistěte se, že máte dostatečnou kapacitu pro připojení ke službám Office 365 a že jste povolili prémiové doplňky. [Plánování sítě a optimalizace výkonu pro Office 365](https://aka.ms/tune/) vám pomůže naplánovat potřeby připojení. Viz také [vytvoření a úprava okruhu ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>K jakým službám Office 365 lze přistupovat přes připojení ExpressRoute?

Aktuální seznam služeb podporovaných přes ExpressRoute najdete na stránce [Adres URL a rozsahů IP adres Office 365.](https://aka.ms/o365endpoints)

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Kolik stojí expressroute pro služby Office 365?

Služby Office 365 vyžadují, aby bylo možné povolit prémiový doplněk. Podívejte se na [stránku s podrobnostmi o cenách,](https://azure.microsoft.com/pricing/details/expressroute/) kde najdete náklady.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>V jakých oblastech je ExpressRoute pro Office 365 podporovaný?

Informace naleznete v [tématu Partneři a umístění ExpressRoute.](expressroute-locations.md)

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Můžu přistupovat k Office 365 přes internet, i když expressroute byl nakonfigurovaný pro mou organizaci?

Ano. Koncové body služby Office 365 jsou dostupné přes Internet, i když expressroute byl nakonfigurovaný pro vaši síť. Pokud je síť ve vašem umístění nakonfigurovaná pro připojení ke službám Office 365 prostřednictvím ExpressRoute, obraťte se na síťový tým vaší organizace.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Jak můžu naAzure ExpressRoute naplánovat vysokou dostupnost pro síťový provoz Office 365?
Podívejte se na doporučení pro [vysokou dostupnost a převzetí služeb při selhání s Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Můžu přistupovat ke službám Office 365 US Government Community (GCC) přes okruh Azure US Government ExpressRoute?

Ano. Koncové body služeb Office 365 GCC jsou dostupné prostřednictvím Azure US Government ExpressRoute. Nejprve však musíte otevřít lístek podpory na webu Azure Portal, abyste poskytli předpony, které chcete inzerovat společnosti Microsoft. Vaše připojení ke službám Office 365 GCC bude vytvořeno po vyřešení lístku podpory. 

## <a name="route-filters-for-microsoft-peering"></a>Filtry směrování pro partnerský vztah Microsoftu

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Poprvé se obracím na peering microsoftu, jaké trasy uvidím?

Neuvidíte žádné trasy. Chcete-li spustit předponu reklamy, musíte k okruhu připojit filtr trasy. Pokyny naleznete v [tématu Konfigurace filtrů postupu pro partnerský vztah společnosti Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Zapnul jsem partnerský vztah společnosti Microsoft a nyní se snažím vybrat Exchange Online, ale je to mi dává chybu, že nejsem oprávněn to udělat.

Při použití filtrů tras může každý zákazník zapnout partnerský vztah Microsoftu. Pokud však chcete spotřebovat služby Office 365, musíte získat autorizaci office 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Povolil jsem partnerský vztah Microsoftu před srpnem 1, 2017, jak mohu využít filtry tras?

Váš stávající okruh bude pokračovat v inzerování předpon Office 365. Pokud chcete přidat veřejné předpony Azure reklamy přes stejný partnerský vztah Microsoftu, můžete vytvořit filtr trasy, vybrat služby, které potřebujete inzerovat (včetně služeb Office 365, které potřebujete) a připojit filtr k partnerovi Microsoftu. Pokyny naleznete v [tématu Konfigurace filtrů postupu pro partnerský vztah společnosti Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Mám Microsoft peering na jednom místě, teď se snažím povolit na jiném místě a já nevidím žádné předpony.

* Microsoft peering obvodů ExpressRoute, které byly nakonfigurovány před srpnem 1, 2017 bude mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, i když nejsou definovány filtry tras.

* Microsoft peering obvodů ExpressRoute, které jsou nakonfigurovány na nebo po srpnu 1, 2017 nebude mít žádné předpony inzerované, dokud není připojen filtr trasy k okruhu. Ve výchozím nastavení se nezobrazí žádné předpony.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Globální dosah

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
