---
title: Časté otázky – Azure ExpressRoute | Dokumentace Microsoftu
description: ExpressRoute – nejčastější dotazy, obsahuje informace o podporované služby Azure, náklady, Data a připojení, smlouvy SLA, poskytovatelé a umístění, šířky pásma a další technické podrobnosti.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 845c53ec970777901ae8d1c0abf5032ac705d3e3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264918"
---
# <a name="expressroute-faq"></a>ExpressRoute – nejčastější dotazy

## <a name="what-is-expressroute"></a>Co je ExpressRoute?

ExpressRoute je služba Azure, která umožňuje vytvářet privátní připojení mezi datacentry Microsoftu a infrastrukturou ve vlastních prostorách nebo v společně umístěného zařízení. Připojení ExpressRoute nemáte se přenášejí prostřednictvím veřejného Internetu a nabízí vyšší zabezpečení, spolehlivost a rychlost s nižší latencí než Typická připojení přes Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Jaké jsou výhody použití ExpressRoute a připojení k privátní síti?

Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Nabízí vyšší zabezpečení, spolehlivost a rychlost, s konzistentní vzhledem k aplikacím a nižší latencí než Typická připojení přes Internet. V některých případech může používání připojení ExpressRoute k přenosu dat mezi místním zařízením a Azure přispět k výraznému snížení nákladů.

### <a name="where-is-the-service-available"></a>Pokud je daná služba dostupná?

Na této stránce najdete umístění a dostupnost služby: [ExpressRoute partneři a umístění](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Jak můžou využít ExpressRoute a připojení k Microsoftu, když nemám partnerství s jedním z partnerů ExpressRoute dopravce?

Můžete vybrat místní dopravce a ŘIDICÍ ethernetová připojení k jednomu z podporovaných exchange umístění poskytovatele. Můžete navázat partnerský stav s Microsoftem v umístění poskytovatele. Podívejte se na poslední část [partnerů a umístění ExpressRoute](expressroute-locations.md) , abyste viděli, jestli se poskytovatel služeb nachází v žádném umístění Exchange. Pak si můžete objednat okruh ExpressRoute prostřednictvím poskytovatele služeb pro připojení k Azure.

### <a name="how-much-does-expressroute-cost"></a>Kolik stojí ExpressRoute?

Podrobnosti o cenách najdete v [podrobnostech](https://azure.microsoft.com/pricing/details/expressroute/) o cenách.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Pokud budu platit pro okruh ExpressRoute danou šířku pásma, má připojení k síti VPN, které můžu zakoupit od svého poskytovatele síťových služeb do stejné rychlost?

Ne. Připojení k síti VPN libovolné rychlosti si můžete zakoupit od svého poskytovatele služeb. Připojení k Azure je však omezená na šířku pásma okruhu ExpressRoute můžete zakoupit.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Pokud budu platit pro okruh ExpressRoute danou šířku pásma, musím schopnost burst až vyšších rychlostí v případě potřeby?

Ano. Okruhy ExpressRoute jsou nakonfigurovány a umožňuje tak burst až dvakrát limit šířky pásma, kterou jste si opatřili bez jakýchkoli dalších nákladů. Obraťte se na svého poskytovatele služby, pokud podporuje tuto možnost. Nejedná se o trvalou dobu a není zaručena.  Pokud provoz přes bránu ExpressRoute, je šířka pásma pro SKU pevná a nedá se roztékat.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Můžu použít stejné připojení privátní sítě pomocí virtuální sítě a dalšími službami Azure současně?

Ano. Okruh ExpressRoute, jakmile je nastavená, umožňuje přístup k služby v rámci virtuální sítě a dalšími službami Azure současně. Připojení k virtuálním sítím přes cestou soukromého partnerského vztahu a k dalším službám přes cesta partnerského vztahu Microsoftu.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Jak se virtuální sítě inzeruje na soukromý partnerský vztah ExpressRoute?

Brána ExpressRoute bude inzerovat *adresní prostory* virtuální sítě Azure a nebude možné ji zahrnout ani vyloučit na úrovni podsítě. Vždy je adresní prostor virtuální sítě, který je inzerován. Pokud se používá partnerský vztah virtuálních sítí a virtuální síť s partnerským vztahem má povolenou možnost použít vzdálenou bránu, bude se taky inzerovat adresní prostor partnerské virtuální sítě.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Kolik předpon lze inzerovat z virtuální sítě do místní sítě v ExpressRoute privátního partnerského vztahu?

K jednomu ExpressRoute připojení nebo prostřednictvím partnerského vztahu virtuálních sítí s využitím přenosu brány se může inzerovat maximálně 200 předpon. Pokud máte třeba 199 adresních prostorů v jedné virtuální síti připojené k okruhu ExpressRoute, všechny 199 těchto předpon se budou inzerovat místně. Případně, pokud máte povolenou virtuální síť, která povoluje přenos brány s jedním adresním prostorem a 150-virtuální sítě paprsků povolených pomocí možnosti Povolit vzdálenou bránu, bude virtuální síť nasazená s bránou inzerovat 151 předpony místně.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Co se stane, když překročím limit předpony u připojení ExpressRoute?

Připojení mezi okruhem ExpressRoute a bránou (a partnerským virtuální sítě pomocí přenosu brány, pokud je k dispozici), bude mimo provoz. Znovu se vytvoří, když se limit předpony už nepřekračuje.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Můžu filtrovat trasy přicházející z místní sítě?

Jediným způsobem, jak filtrovat/zahrnout trasy, je na místním hraničním směrovači. Trasy definované uživatelem lze ve virtuální síti přidat, aby bylo možné ovlivnit konkrétní směrování, ale tato akce bude statická a nebude součástí inzerování protokolu BGP.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Nabízí ExpressRoute smlouva o úrovni služeb (SLA)?

Informace najdete na stránce věnované [smlouvě SLA pro ExpressRoute](https://azure.microsoft.com/support/legal/sla/) .

## <a name="supported-services"></a>Podporované služby

ExpressRoute podporuje [tři domény směrování](expressroute-circuit-peerings.md) pro různé typy služeb: privátní partnerské vztahy, partnerské vztahy Microsoftu a veřejný partnerský vztah (zastaralé).

### <a name="private-peering"></a>Soukromý partnerský vztah

**Doložen**

* Virtuální sítě, včetně všech virtuálních počítačů a cloudových služeb

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu

Pokud je pro partnerský vztah Microsoft Azure povolen okruh ExpressRoute, můžete k [rozsahům veřejných IP adres](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) , které se používají v Azure, přistupovat přes okruh. Partnerský vztah Microsoftu Azure poskytne přístup ke službám, které jsou aktuálně hostované v Azure (s geografickými omezeními v závislosti na SKU vaší okruhu). Pokud chcete ověřit dostupnost konkrétní služby, můžete si prohlédnout dokumentaci k příslušné službě a zjistit, jestli je pro danou službu publikovaný rezervovaný rozsah. Pak vyhledejte rozsahy IP adres cílové služby a porovnejte je s rozsahy uvedenými v části [rozsahy IP adres Azure a značky služby – veřejný cloudový soubor XML](https://www.microsoft.com/download/details.aspx?id=56519). Případně můžete pro vyjasnění otevřít lístek podpory pro příslušnou službu.

**Doložen**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI – k dispozici prostřednictvím regionální komunity Azure, [najdete informace o](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) tom, jak zjistit oblast Power BI tenanta.
* Azure Active Directory
* [Virtuální klient Windows](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (komunita Azure Global Services)
* Veřejné IP adresy Azure pro IaaS (Virtual Machines, Virtual Network brány, nástroje pro vyrovnávání zatížení atd.)  
* Podporuje se i většina ostatních služeb Azure. Zkontrolujte prosím přímo se službou, kterou chcete použít k ověření podpory.

**Nepodporováno:**

* CDN
* Azure Front Door
* Multi-Factor Authentication Server (starší verze)
* Traffic Manager

### <a name="public-peering"></a>Veřejný partnerský vztah

Veřejný partnerský vztah se zakázalo na nové okruhy ExpressRoute. Služby Azure jsou teď dostupné v partnerském vztahu Microsoftu. Pokud jste okruh, který byl vytvořen před veřejným partnerským vztahem, zastaralý, můžete zvolit používání partnerského vztahu Microsoftu nebo veřejného partnerského vztahu v závislosti na službách, které požadujete.

Další informace a kroky konfigurace veřejného partnerského vztahu najdete v tématu [veřejné partnerské vztahy ExpressRoute](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Proč se při konfiguraci partnerského vztahu Microsoftu zobrazuje stav inzerovat veřejné předpony jako nutné ověření?

Microsoft ověří, jestli se vám v registru směrování Internetu přiřadí zadané "inzerované veřejné předpony" a "partnerské číslo ASN" (nebo číslo ASN zákazníka). Pokud získáváte veřejné předpony z jiné entity a pokud přiřazení není zaznamenané u registru směrování, automatické ověřování se nedokončí a bude vyžadovat ruční ověření. Pokud se automatické ověření nezdaří, zobrazí se zpráva "vyžadováno ověření".

Pokud se zobrazí zpráva potřebná pro ověření, shromážděte dokumenty, které zobrazují veřejné předpony, do vaší organizace podle entity, která je uvedena jako vlastník předpon v registru směrování, a odešlete tyto dokumenty k ručnímu ověření pomocí Otevřete lístek podpory, jak je znázorněno níže.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Je Dynamics 365 podporován na ExpressRoute?

Prostředí Dynamics 365 a Common Data Service (CD) jsou hostována v Azure, takže zákazníci využívají základní podporu ExpressRoute pro prostředky Azure. Můžete se připojit ke svým koncovým bodům služby, pokud váš filtr směrovače obsahuje oblasti Azure, ve kterých jsou vaše prostředí Dynamics 365 a CD hostovaná.

> [!NOTE]
> [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) se **nevyžaduje** pro připojení Dynamics 365 přes Azure ExpressRoute.

## <a name="data-and-connections"></a>Připojení a dat.

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Je nějak omezený objem dat, který jsem se můžou přenášet přes ExpressRoute?

Objem přenosu dat jsme nenastavujte omezení. Informace o sazbách šířky pásma najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>ExpressRoute podporuje jaké rychlosti připojení?

Podporované šířky pásma nabídky:

50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s

### <a name="which-service-providers-are-available"></a>Které poskytovatelé služeb jsou k dispozici?

Seznam poskytovatelů a umístění služeb najdete v tématu [ExpressRoute partneři a umístění](expressroute-locations.md) .

## <a name="technical-details"></a>Technické podrobnosti

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Jaké jsou technické požadavky pro připojení k Azure v místním umístění?

Požadavky najdete na [stránce s požadavky na ExpressRoute](expressroute-prerequisites.md) .

### <a name="are-connections-to-expressroute-redundant"></a>Jsou připojení k ExpressRoute redundantní?

Ano. Každý okruh ExpressRoute má redundantní dvojici křížové připojení nakonfigurovaná k zajištění vysoké dostupnosti.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Bude ztrátě připojení, pokud jeden z odkazů ExpressRoute?

Pokud jeden z křížové připojení selže, nedojde ke ztrátě připojení. Redundantní připojení je k dispozici pro podporu zatížení sítě a zajištění vysoké dostupnosti váš okruh ExpressRoute. Kromě toho můžete vytvořit okruhu v jiném umístění partnerského vztahu k dosažení okruh úroveň odolnosti.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Návody implementovat redundanci s privátním partnerským vztahem?

Více okruhů ExpressRoute z různých umístění partnerského vztahu může být připojeno ke stejné virtuální síti, aby bylo zajištěno vysoké dostupnosti v případě, že jeden okruh nebude k dispozici. Pak můžete [přiřadit vyšší váhu](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) místnímu připojení a upřednostnit konkrétní okruh. Důrazně doporučujeme, aby si zákazníci nastavili aspoň dva okruhy ExpressRoute, aby se předešlo jednomu bodu selhání. 

V tématu Navrhování pro zajištění vysoké [dostupnosti najdete tady, kde najdete](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) [Návrh pro zotavení](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) po havárii.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Jak implementovat redundanci v partnerském vztahu Microsoftu?

Důrazně doporučujeme, když zákazníci používají partnerské vztahy Microsoftu pro přístup k veřejným službám Azure, jako je Azure Storage nebo Azure SQL, a také zákazníkům, kteří používají partnerské vztahy Microsoftu pro Office 365, které implementují více okruhů v různých partnerských vztahůch. umístění, aby nedocházelo k jednomu bodu selhání. Zákazníci můžou inzerovat stejnou předponu na obou okruhech a používat [je jako nedokončená cesta](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) nebo inzerovat jiné předpony k určení cesty z místního prostředí.

Pro návrh pro zajištění vysoké dostupnosti se podívejte [sem](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) .

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Jak můžu zajistit vysokou dostupnost na virtuální síť připojená k ExpressRoute?

Dosáhněte vysoké dostupnosti pomocí připojení k vaší virtuální sítě okruhů ExpressRoute v různých umístění partnerského vztahu (například, Singapur, singapur2). Pokud jeden okruh ExpressRoute ocitne mimo provoz, přestane fungovat připojení prostřednictvím jiného okruhu ExpressRoute. Ve výchozím nastavení je směrování přenosů z vaší virtuální sítě založené na stejné náklady Multipath směrování ECMP (). Váhy připojení můžete použít k preferovali jeden okruh do jiného. Další informace najdete v tématu [optimalizace směrování ExpressRoute](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Návody zajistěte, aby byl můj provoz určený pro veřejné služby Azure, jako je Azure Storage a Azure SQL v partnerském vztahu Microsoftu nebo veřejný partnerský vztah, upřednostňován na cestě ExpressRoute?

Ve směrovačích musíte implementovat atribut *místní předvolby* , aby se zajistilo, že cesta z místního prostředí do Azure je vždy upřednostňovaná na okruhech ExpressRoute.

Další podrobnosti najdete [na základě](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) výběru cest protokolu BGP a konfigurací běžných směrovačů. 

### <a name="onep2plink"></a>Pokud nemám společné umístění v cloudové výměně a poskytovatel mých služeb nabízí připojení typu Point-to-Point, musím objednat dvě fyzická připojení mezi místní sítí a Microsoftem?

Pokud váš poskytovatel služeb můžete vytvořit dvě sítě Ethernet virtuálních okruhů přes fyzické připojení, vám stačí jedno fyzické připojení. Fyzické připojení (například optického vlákna) je ukončen ve vrstvě 1 (L1) zařízení (viz obrázek). Označená dva okruhy virtuální sítě Ethernet s různými ID sítě VLAN, jeden pro primární okruh a jeden pro sekundární. ID sítě VLAN jsou ve vnější 802.1Q Ethernet záhlaví. Hlavička vnitřního 802.1 Q Ethernet (není zobrazená) je namapovaná na konkrétní [doménu směrování ExpressRoute](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Můžu prodloužit jeden z mé virtuální místní sítě do Azure pomocí ExpressRoute?

Ne. Nepodporujeme rozšíření vrstvy 2 připojení do Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Může mít více než jeden okruh ExpressRoute ve svém předplatném?

Ano. Můžete mít více než jeden okruh ExpressRoute v rámci vašeho předplatného. Výchozí limit je nastaven na 10. Microsoft Support o zvýšení limitu, můžete kontaktovat v případě potřeby.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Může mít okruhy ExpressRoute od různých poskytovatelů služeb?

Ano. Okruhy ExpressRoute může mít s mnoho poskytovatelů služeb. Každý okruh ExpressRoute je přidružen pouze jeden poskytovatel. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Zobrazila se dvěma umístění partnerských vztahů ExpressRoute ve stejné metro, například Singapur a singapur2. Které umístění partnerského vztahu je nejvhodnější k vytvoření okruhu ExpressRoute?
Pokud váš poskytovatel služeb nabízí ExpressRoute v obou lokalitách, můžete spolupracovat se svým poskytovatelem a vyberte buď server a nastavte ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Může mít víc okruhů ExpressRoute ve stejné metro? Můžete propojit je ke stejné virtuální síti?

Ano. Máte víc okruhů ExpressRoute s poskytovateli stejných nebo různých služeb. Pokud metro má více umístění partnerského vztahu ExpressRoute a obvody se vytvářejí na jiné umístění partnerského vztahu, můžete je propojit do stejné virtuální síti. Pokud jsou okruhy vytvořeny ve stejném umístění partnerského vztahu, můžete propojit až 4 okruhy se stejnou virtuální sítí.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Jak připojit své virtuální sítě k okruhu ExpressRoute

Toto jsou základní kroky:

* Vytvoření okruhu ExpressRoute a poskytovatele služeb, povolte ho.
* Je, nebo poskytovatele, musíte nakonfigurovat protokol BGP partnerského vztahu (s).
* Propojení virtuální sítě k okruhu ExpressRoute.

Další informace najdete v tématu [pracovní postupy ExpressRoute pro zřizování okruhů a stavy okruhů](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Existují hranice připojení v okruhu ExpressRoute?

Ano. Článek o [partnerech a umístěních ExpressRoute](expressroute-locations.md) poskytuje přehled hranic připojení pro okruh ExpressRoute. Připojení pro okruh ExpressRoute je omezené na jeden geopolitické oblasti. Připojení lze rozšířit pro různé geopolitické oblasti tím, že funkce ExpressRoute premium.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Můžete propojit více než jedné virtuální sítě k okruhu ExpressRoute?

Ano. Na standardním okruhu ExpressRoute můžete mít až 10 připojení k virtuálním sítím a až 100 na [okruhu Premium ExpressRoute](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Mám několik předplatných Azure, které obsahují virtuální sítě. Můžete propojit virtuální sítě, které jsou v samostatné předplatné na jeden okruh ExpressRoute?

Ano. Můžete propojit až 10 virtuálních sítí ve stejném předplatném, jako je okruh nebo různá předplatná, a to pomocí jednoho okruhu ExpressRoute. Tento limit můžete zvýšit tím, že funkce ExpressRoute premium.

Další informace najdete v tématu [sdílení okruhu ExpressRoute napříč několika předplatnými](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Mám několik předplatných Azure, které jsou přidružené k jiné tenantů Azure Active Directory nebo registrace smlouvy Enterprise. Je možné připojit virtuální sítě, které jsou v samostatné tenanty a registrace na jeden okruh ExpressRoute není ve stejném tenantovi nebo registrace?

Ano. Autorizací do ExpressRoute může mít rozsah hranice předplatné klienta a registrace bez další nezbytné konfigurace. 

Další informace najdete v tématu [sdílení okruhu ExpressRoute napříč několika předplatnými](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Připojeni ke stejnému okruhu navzájem izolované virtuální sítě?

Ne. Všechny virtuální sítě propojené ke stejnému okruhu ExpressRoute z hlediska směrování jsou součástí stejné domény směrování a nejsou vzájemně izolované. Pokud potřebujete izolaci trasy, musíte vytvořit samostatný okruh ExpressRoute.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Může mít jednu virtuální sítě připojené k více než jeden okruh ExpressRoute?

Ano. Jednu virtuální síť můžete propojit s až čtyřmi okruhy ExpressRoute ve stejném umístění nebo v různých umístěních partnerského vztahu. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Z mé virtuální sítě připojené k okruhy ExpressRoute, přístup k Internetu?

Ano. Pokud jste neinzerovali výchozí trasy (0.0.0.0/0) nebo předpony trasy Internet prostřednictvím relace protokolu BGP, můžete se připojit k Internetu z virtuální sítě propojené s okruhem ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Můžete blokovat připojení k Internetu k virtuálním sítím, které jsou připojené k okruhy ExpressRoute?

Ano. Můžete Inzerovat výchozí trasy (0.0.0.0/0) k blokování všech připojení k Internetu pro virtuální počítače nasazené v rámci virtuální sítě a směrování veškerého provozu si přes okruh ExpressRoute.

Pokud jste inzerování výchozích tras, jsme zpět do místního vynucení provozu ke službám nabízeným prostřednictvím partnerského vztahu (jako je například Azure storage a SQL DB) Microsoftu. Budete muset nakonfigurovat směrovače vrátit provoz do Azure prostřednictvím cesta partnerského vztahu Microsoftu nebo přes Internet. Pokud jste povolili koncový bod služby pro službu, není na místním vynutit provoz do služby. Provoz zůstává v páteřní síti Azure. Další informace o koncových bodech služby najdete v tématu [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json) .

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Můžete k sobě navzájem hovořit virtuální sítě propojené ke stejnému okruhu ExpressRoute?

Ano. Virtuální počítače nasazené ve virtuálních sítích, které jsou připojené ke stejnému okruhu ExpressRoute může komunikovat mezi sebou.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Můžete použít připojení site-to-site pro virtuální sítě ve spojení s ExpressRoute?

Ano. Mohou existovat vedle sebe ExpressRoute s VPN typu site-to-site. Přečtěte si téma [Konfigurace ExpressRoute a připojení mezi lokalitami](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Proč je veřejná IP adresa přidružená k bráně ExpressRoute ve virtuální síti?

Veřejná IP adresa se používá pouze interní správy a nepředstavuje bezpečnostní rizika vaší virtuální sítě.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Je nějak omezený počet tras, které můžu můžete inzerovat?

Ano. Přijímáme až 4000 předpon trasy pro soukromý partnerský vztah a 200 pro partnerský vztah Microsoftu. Můžete to zvýšit na 10 000 tras pro soukromý partnerský vztah, pokud povolíte funkci ExpressRoute premium.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Existují omezení týkající se rozsahy IP adres, které můžu můžete inzerovat přes relaci protokolu BGP?

Pro relace partnerského vztahu protokolu BGP Microsoft nepřijímáme privátní předpony (definice RFC1918). U Microsoftu i privátního partnerského vztahu přijímáme libovolnou velikost předpon (až do/32).

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Co se stane, když překročím protokol BGP omezuje?

Relace protokolu BGP se ukončí. Se obnoví po počet předponu klesne pod limit.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Co je doba uchování protokolu BGP ExpressRoute? Lze jej upravit?

Doba uchování je 180. Zprávy keep-alive odesílají každých 60 sekund. Tyto jsou pevně dané nastavení na straně Microsoftu, který se nedá změnit. Je možné, je možné nakonfigurovat jiné časovače a parametry relace protokolu BGP se vyjedná odpovídajícím způsobem.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Můžete změnit šířku pásma okruhu ExpressRoute?

Ano, zkuste zvětšit šířku pásma váš okruh ExpressRoute na webu Azure Portal nebo pomocí prostředí PowerShell. Pokud je kapacity k dispozici na fyzický port na kterém byl vytvořen váš okruh, proběhne úspěšně změny. 

Pokud změny selže, znamená to buď není dostatek kapacity na aktuální portu a je potřeba vytvořit nový okruh ExpressRoute s větší šířku pásma, nebo, který v tomto umístění není žádný další kapacitu, v takovém případě nebude možné zvýšit Šířka pásma. 

Budete také muset se na svého poskytovatele připojení ověřte, že aktualizují omezení v rámci svých sítí, podpořte zvýšení šířky pásma. Nejde, ale snížit šířku pásma váš okruh ExpressRoute. Budete muset vytvořit nový okruh ExpressRoute s menší šířkou pásma a odstranit staré okruh.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Jak změnit šířku pásma okruhu ExpressRoute?

Můžete aktualizovat šířky pásma okruhu ExpressRoute pomocí rozhraní REST API nebo rutiny prostředí PowerShell.

## <a name="expressroute-premium"></a>ExpressRoute premium

### <a name="what-is-expressroute-premium"></a>Co je ExpressRoute premium?

ExpressRoute premium je kolekce následující funkce:

* Zvýšená směrovací tabulky limit ze 4000 na 10 000 tras pro soukromý partnerský vztah.
* Zvýšit počet virtuálních sítí a globální dosah ExpressRoute připojení, která se dá nastavit pro okruh ExpressRoute (výchozí hodnota je 10). Další informace najdete v tabulce [omezení ExpressRoute](#limits) .
* Připojení k Office 365
* Globální konektivita v základní síti Microsoft. Teď můžete propojit virtuální síť, která v geopolitické oblasti jeden okruh ExpressRoute v jiné oblasti.<br>
    **4.6**

    *  Můžete propojit virtuální sítě vytvořené v oblasti Evropa – západ na okruh ExpressRoute vytvořený v Silicon Valley. 
    *  Na partnerského vztahu Microsoftu byly inzerované předpony z jiných geopolitických oblastí, můžete z připojíte k, například SQL Azure v oblasti Evropa – západ okruh v Silicon Valley.


### <a name="limits"></a>Kolik připojení virtuální sítě a ExpressRoute Global Reach můžu povolit u okruhu ExpressRoute, pokud jsem povolil ExpressRoute Premium?

Následující tabulky popisují limity pro ExpressRoute a počet připojení virtuální sítě a ExpressRoute globální dosah jeden okruh ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Jak povolím ExpressRoute premium?

Funkce ExpressRoute premium je povolit, pokud je povolena funkce a může být vypnut stačí aktualizovat stav okruhu. Umožňuje v okamžiku vytvoření okruhu ExpressRoute premium, nebo může volat rozhraní REST API nebo powershellu.

### <a name="how-do-i-disable-expressroute-premium"></a>Jak zakázat ExpressRoute premium?

ExpressRoute premium můžete zakázat pomocí volání rozhraní REST API nebo powershellu. Ujistěte se, že provedete škálování vašeho připojení musí splňovat výchozí omezení, než vypnete ExpressRoute premium. Pokud vaše využití škáluje nad výchozí omezení, požadavek na zákaz ExpressRoute premium se nezdaří.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Můžete mi zvolit funkce, které chci, aby ze sady funkcí úrovně premium?

Ne. Nelze vybrat funkce. Můžeme povolit všechny funkce, když zapnete ExpressRoute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Kolik stojí ExpressRoute premium?

Náklady najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Platí pro ExpressRoute premium kromě standardních poplatků za ExpressRoute?

Ano. ExpressRoute premium poplatky nad rámec poplatků za okruhu ExpressRoute a poplatky, které musí poskytovatelem připojení.

## <a name="expressroute-local"></a>ExpressRoute místní
### <a name="what-is-expressroute-local"></a>Co je ExpressRoute místní?
ExpressRoute Local je SKU okruhu ExpressRoute, kromě standardní SKU a SKU úrovně Premium. Klíčovou funkcí místní je, že místní okruh v umístění partnerského vztahu ExpressRoute poskytuje přístup pouze k jedné nebo dvěma oblastem Azure v nebo téměř stejné Metro. Na rozdíl od standardního okruhu získáte přístup ke všem oblastem Azure v geopolitické oblasti a okruhu Premium pro všechny oblasti Azure globálně. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Jaké jsou výhody ExpressRoute Local?
I když potřebujete platit přenos odchozích dat pro okruh Standard nebo Premium ExpressRoute, nebudete za váš přenos odchozích dat platit samostatně pro místní okruh ExpressRoute. Jinými slovy, cena za ExpressRoute Local zahrnuje poplatky za přenos dat. ExpressRoute Local je výhodnější řešení, pokud máte obrovské množství dat, která se mají přenést, a můžete přenést data prostřednictvím privátního připojení k umístění partnerského vztahu ExpressRoute v blízkosti požadovaných oblastí Azure. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Jaké funkce jsou k dispozici a co nejsou v ExpressRoute místním prostředí?
V porovnání se standardním okruhem ExpressRoute má místní okruh stejnou sadu funkcí s výjimkou:
* Rozsah přístupu k oblastem Azure, jak je popsáno výše
* ExpressRoute Global Reach není k dispozici v místním prostředí.

ExpressRoute Local má také stejné limity pro prostředky (např. počet virtuální sítě na okruh) jako standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Kde je ExpressRoute k dispozici místní a u kterých oblastí Azure je každé umístění partnerského vztahu namapované?
ExpressRoute Local je k dispozici v umístěních partnerských vztahů, kde jedna nebo dvě oblasti Azure jsou blízko. Není k dispozici v umístění partnerského vztahu, kde neexistuje oblast Azure v tomto státě nebo provincii nebo zemi. Přečtěte si prosím přesná mapování na [stránce umístění](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>ExpressRoute pro Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Jak vytvořit okruh ExpressRoute pro připojení ke službám Office 365?

1. Přečtěte si [stránku předpoklady ExpressRoute](expressroute-prerequisites.md) a ujistěte se, že splňujete požadavky.
2. Pokud chcete zajistit splnění požadavků na připojení, Projděte si seznam poskytovatelů služeb a umístění v článku [partneři a umístění ExpressRoute](expressroute-locations.md) .
3. Naplánujte požadavky na kapacitu pomocí kontroly [Plánování sítě a optimalizace výkonu pro Office 365](https://aka.ms/tune/).
4. Postupujte podle kroků uvedených v pracovních postupech a nastavte možnosti připojení [ExpressRoute pracovní postupy pro zřizování okruhů a stavy okruhů](expressroute-workflows.md).

> [!IMPORTANT]
> Ujistěte se, že jste povolili doplněk ExpressRoute premium, při konfiguraci připojení ke službám Office 365.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>Můžou moje stávající okruhy ExpressRoute podporovat připojení ke službám Office 365?

Ano. Stávající okruh ExpressRoute může být nakonfigurované pro podporu připojení ke službám Office 365. Ujistěte se, že máte dostatečnou kapacitu pro připojení ke službám Office 365 a že jste povolili doplněk premium. [Plánování sítě a optimalizace výkonu pro Office 365](https://aka.ms/tune/) vám pomůžou plánovat potřeby připojení. Přečtěte si také článek [Vytvoření a úprava okruhu ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Jaké Office 365 služby přístupné přes připojení ExpressRoute?

Aktuální seznam služeb podporovaných přes ExpressRoute najdete na stránce [adresy URL a rozsahy IP adres sady Office 365](https://aka.ms/o365endpoints) .

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Jaká je ExpressRoute pro náklady na služby Office 365?

Služby Office 365 vyžadují doplněk premium, aby byla povolená. Náklady najdete na [stránce s podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Jaké oblasti je ExpressRoute pro Office 365 podporuje?

Informace najdete v tématu [partneři a umístění ExpressRoute](expressroute-locations.md) .

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Můžete mi přístup k Office 365 přes Internet, i v případě ExpressRoute se nakonfigurovalo pro moji organizaci?

Ano. Koncové body služeb Office 365 jsou dostupné prostřednictvím Internetu, i když byl nakonfigurován pro vaši síť ExpressRoute. Poraďte se síťovým týmem vaší organizace, pokud sítě v umístění nakonfigurovaná pro připojení ke službám Office 365 přes ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Jak se můžete naplánovat pro zajištění vysoké dostupnosti pro Office 365 síťový provoz na Azure ExpressRoute?
Přečtěte si doporučení k zajištění [vysoké dostupnosti a převzetí služeb při selhání pomocí Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Můžete přistupovat služeb Office 365 US Government Community (GCC) přes okruh Azure US Government ExpressRoute?

Ano. Koncové body služeb Office 365 GCC jsou dostupné prostřednictvím Azure US Government ExpressRoute. Ale nejprve musíte vytvořit lístek podpory na portálu Azure portal k poskytování předpony, které máte v úmyslu inzerovat Microsoftu. Připojení ke službám Office 365 GCC naváže se po vyřešení lístek podpory. 

## <a name="route-filters-for-microsoft-peering"></a>Filtrů směrování pro partnerský vztah Microsoftu

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Můžu jsem zapnutí partnerský vztah Microsoftu poprvé, jaké trasy si můžu zobrazit?

Neuvidíte žádné trasy. Budete muset připojit filtr tras pro váš okruh spustit oznámení o inzerovaných programech předponu. Pokyny najdete v tématu [Konfigurace filtrů tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Po zapnutí partnerský vztah Microsoftu a teď pokouším vyberte Exchange Online, ale se mi chyba, že mám mi oprávnění k tomu poskytuje.

Při použití filtrů tras, každý zákazník se může zapnout partnerský vztah Microsoftu. Ale určená pro služby Office 365, stále musíte získat autorizováno uživatelem služeb Office 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Můžu povolené partnerského vztahu před 1. srpna 2017, jak můžete využít výhod filtry tras Microsoftu?

Váš stávající okruh bude pokračovat ve inzerci předpon pro Office 365. Pokud chcete přidat reklamy veřejné předpony Azure přes stejný partnerský vztah Microsoftu, můžete vytvořit filtr tras, vybrat služby, které potřebujete inzerovat (včetně služeb Office 365, které potřebujete), a připojit filtr k partnerskému vztahu Microsoftu. Pokyny najdete v tématu [Konfigurace filtrů tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Mám v jednom umístění partnerského vztahu Microsoftu, nyní pokouším předat ji povolit na jiném místě a mi nezobrazují všechny předpony.

* Partnerský vztah Microsoftu okruhů ExpressRoute, které byly nakonfigurovány před 1. srpna 2017 budou mít všechny služby předpony inzerované prostřednictvím Microsoft partnerský vztah, i když nejsou definovány filtry tras.

* Partnerský vztah Microsoftu okruhů ExpressRoute, které jsou nakonfigurované 1. srpna 2017 nebo později nebude mít všechny předpony inzerované, dokud se filtr tras je připojen k okruhu. Ve výchozím nastavení se zobrazí bez předpony.

## <a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
