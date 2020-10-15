---
title: Nejčastější dotazy – Azure ExpressRoute | Microsoft Docs
description: Nejčastější dotazy k ExpressRoute obsahují informace o podporovaných službách Azure, nákladech, datech a připojeních, smlouvě SLA, poskytovatelích a umístěních, šířce pásma a dalších technických podrobnostech.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: duau
ms.openlocfilehash: d91d896da21d9d96e45c0eab3d5d895364f3e149
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077350"
---
# <a name="expressroute-faq"></a>ExpressRoute – nejčastější dotazy

## <a name="what-is-expressroute"></a>Co je ExpressRoute?

ExpressRoute je služba Azure, která umožňuje vytvářet privátní připojení mezi datovými centry Microsoftu a infrastrukturou ve vašich prostorách nebo v zařízení se systémem. Připojení ExpressRoute nevyužívají veřejný Internet a nabízejí vyšší úroveň zabezpečení, spolehlivosti a rychlosti s nižší latencí než typická připojení přes Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Jaké jsou výhody používání ExpressRoute a privátních síťových připojení?

Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Nabízejí vyšší zabezpečení, spolehlivost a rychlost s nižší a konzistentní latencí než typická připojení přes Internet. V některých případech může použití připojení ExpressRoute k přenosu dat mezi místními zařízeními a Azure přinést významné cenové výhody.

### <a name="where-is-the-service-available"></a>Kde je služba k dispozici?

Na této stránce najdete umístění a dostupnost služby: [ExpressRoute partneři a umístění](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Jak se dá používat ExpressRoute pro připojení k Microsoftu, pokud nemám partnerství s některým z partnerů ExpressRoute-dopravce?

V jednom z podporovaných umístění poskytovatelů Exchange můžete vybrat připojení k síti Ethernet pro místní dopravce a pozemky. V umístění poskytovatele pak můžete vytvořit partnera s Microsoftem. Podívejte se na poslední část [partnerů a umístění ExpressRoute](expressroute-locations.md) , abyste viděli, jestli se poskytovatel služeb nachází v žádném umístění Exchange. Pak můžete objednat okruh ExpressRoute prostřednictvím poskytovatele služby pro připojení k Azure.

### <a name="how-much-does-expressroute-cost"></a>Kolik stojí ExpressRoute náklady?

Podrobnosti o cenách najdete v [podrobnostech](https://azure.microsoft.com/pricing/details/expressroute/) o cenách.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Pokud platíte za okruh ExpressRoute dané šířky pásma, musí mít připojení k síti VPN, které zakoupíte od poskytovatele síťové služby, stejnou rychlost?

Ne. Připojení k síti VPN libovolné rychlosti můžete koupit od svého poskytovatele služeb. Připojení k Azure je ale omezené na šířku pásma okruhu ExpressRoute, kterou si koupíte.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Pokud platíte za okruh ExpressRoute dané šířky pásma, mám v případě potřeby možnost zvýšit zatížení na vyšší rychlost?

Yes. Okruhy ExpressRoute jsou nakonfigurované tak, aby vám umožnily nárůst limitu šířky pásma na více než dvě. Pokud chcete zjistit, jestli podporují tuto funkci, obraťte se na svého poskytovatele služeb. Nejedná se o trvalou dobu a není zaručena.  Pokud provoz přes bránu ExpressRoute, je šířka pásma pro SKU pevná a nedá se roztékat.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Můžu současně použít stejné privátní síťové připojení k virtuální síti a dalším službám Azure?

Yes. Okruh ExpressRoute, po nastavení, umožňuje přístup ke službám v rámci virtuální sítě a dalších služeb Azure současně. Připojíte se k virtuálním sítím přes privátní cestu partnerského vztahu a dalším službám přes cestu partnerského vztahu Microsoftu.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Jak se virtuální sítě inzeruje na soukromý partnerský vztah ExpressRoute?

Brána ExpressRoute bude inzerovat *adresní prostory* virtuální sítě Azure a nebude možné ji zahrnout ani vyloučit na úrovni podsítě. Vždy je adresní prostor virtuální sítě, který je inzerován. Pokud se používá partnerský vztah virtuálních sítí a virtuální síť s partnerským vztahem má povolenou možnost použít vzdálenou bránu, bude se taky inzerovat adresní prostor partnerské virtuální sítě.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Kolik předpon lze inzerovat z virtuální sítě do místní sítě v ExpressRoute privátního partnerského vztahu?

K jednomu ExpressRoute připojení nebo prostřednictvím partnerského vztahu virtuálních sítí s využitím přenosu brány se může inzerovat maximálně 200 předpon. Pokud máte třeba 199 adresních prostorů v jedné virtuální síti připojené k okruhu ExpressRoute, všechny 199 těchto předpon se budou inzerovat místně. Případně, pokud máte povolenou virtuální síť, která povoluje přenos brány s jedním adresním prostorem a 150-virtuální sítě paprsků povolených pomocí možnosti Povolit vzdálenou bránu, bude virtuální síť nasazená s bránou inzerovat 151 předpony místně.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Co se stane, když překročím limit předpony u připojení ExpressRoute?

Připojení mezi okruhem ExpressRoute a bránou (a partnerským virtuální sítě pomocí přenosu brány, pokud je k dispozici), bude mimo provoz. Znovu se vytvoří, když se limit předpony už nepřekračuje.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Můžu filtrovat trasy přicházející z místní sítě?

Jediným způsobem, jak filtrovat/zahrnout trasy, je na místním hraničním směrovači. Trasy definované uživatelem lze ve virtuální síti přidat, aby bylo možné ovlivnit konkrétní směrování, ale tato akce bude statická a nebude součástí inzerování protokolu BGP.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Nabízí ExpressRoute smlouva SLA (SLA)?

Informace najdete na stránce věnované [smlouvě SLA pro ExpressRoute](https://azure.microsoft.com/support/legal/sla/) .

## <a name="supported-services"></a>Podporované služby

ExpressRoute podporuje [tři domény směrování](expressroute-circuit-peerings.md) pro různé typy služeb: privátní partnerské vztahy, partnerské vztahy Microsoftu a veřejný partnerský vztah (zastaralé).

### <a name="private-peering"></a>Soukromý partnerský vztah

**Doložen**

* Virtuální sítě, včetně všech virtuálních počítačů a cloudových služeb

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu

Pokud je pro partnerský vztah Microsoft Azure povolen okruh ExpressRoute, můžete k [rozsahům veřejných IP adres](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) , které se používají v Azure, přistupovat přes okruh. Partnerský vztah Microsoftu Azure poskytne přístup ke službám, které jsou aktuálně hostované v Azure (s geografickými omezeními v závislosti na SKU vaší okruhu). Pokud chcete ověřit dostupnost konkrétní služby, můžete si prohlédnout dokumentaci k příslušné službě a zjistit, jestli je pro danou službu publikovaný rezervovaný rozsah. Pak vyhledejte rozsahy IP adres cílové služby a porovnejte je s rozsahy uvedenými v části [rozsahy IP adres Azure a značky služby – veřejný cloudový soubor XML](https://www.microsoft.com/download/details.aspx?id=56519). Případně můžete pro vyjasnění otevřít lístek podpory pro příslušnou službu.

**Doložen**

* [Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* Power BI – k dispozici prostřednictvím regionální komunity Azure, [najdete informace o](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) tom, jak zjistit oblast Power BI tenanta.
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (komunita Azure Global Services)
* Veřejné IP adresy Azure pro IaaS (Virtual Machines, Virtual Network brány, nástroje pro vyrovnávání zatížení atd.)  
* Podporuje se i většina ostatních služeb Azure. Podívejte se prosím přímo na službu, kterou chcete použít k ověření podpory.

**Nepodporováno:**

* CDN
* Azure Front Door
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* Multi-Factor Authentication Server (starší verze)
* Traffic Manager

### <a name="public-peering"></a>Veřejný partnerský vztah

Veřejný partnerský vztah byl zakázán pro nové okruhy ExpressRoute. Služby Azure jsou teď dostupné v partnerském vztahu Microsoftu. Pokud jste okruh, který byl vytvořen před veřejným partnerským vztahem, zastaralý, můžete zvolit používání partnerského vztahu Microsoftu nebo veřejného partnerského vztahu v závislosti na službách, které požadujete.

Další informace a kroky konfigurace veřejného partnerského vztahu najdete v tématu [veřejné partnerské vztahy ExpressRoute](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Proč se při konfiguraci partnerského vztahu Microsoftu zobrazuje stav inzerovat veřejné předpony jako nutné ověření?

Microsoft ověří, jestli se vám v registru směrování Internetu přiřadí zadané "inzerované veřejné předpony" a "partnerské číslo ASN" (nebo číslo ASN zákazníka). Pokud získáváte veřejné předpony z jiné entity a pokud přiřazení není zaznamenané u registru směrování, automatické ověřování se nedokončí a bude vyžadovat ruční ověření. Pokud se automatické ověření nezdaří, zobrazí se zpráva "vyžadováno ověření".

Pokud se zobrazí zpráva potřebná pro ověření, shromážděte dokumenty, které zobrazují veřejné předpony, do vaší organizace podle entity, která je uvedena jako vlastník předpony v registru směrování, a odešlete tyto dokumenty k ručnímu ověření otevřením lístku podpory, jak je uvedeno níže.

![Snímek obrazovky s novou žádostí o podporu (lístek podpory) pro "ověření vlastnictví veřejných předpon".](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Je Dynamics 365 podporován na ExpressRoute?

Prostředí Dynamics 365 a Common Data Service (CD) jsou hostována v Azure, takže zákazníci využívají základní podporu ExpressRoute pro prostředky Azure. Můžete se připojit ke svým koncovým bodům služby, pokud váš filtr směrovače obsahuje oblasti Azure, ve kterých jsou vaše prostředí Dynamics 365 a CD hostovaná.

> [!NOTE]
> [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) se **nevyžaduje** pro připojení Dynamics 365 přes Azure ExpressRoute, pokud je okruh ExpressRoute nasazený ve stejné [geopolitické oblasti](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers#expressroute-locations).

## <a name="data-and-connections"></a>Data a připojení

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Existují omezení množství dat, která lze přenést pomocí ExpressRoute?

Nenastavuje se limit pro objem přenosů dat. Informace o sazbách šířky pásma najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Jaké rychlosti připojení podporuje ExpressRoute?

Podporované nabídky šířky pásma:

50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s

### <a name="which-service-providers-are-available"></a>Kteří poskytovatelé služeb jsou k dispozici?

Seznam poskytovatelů a umístění služeb najdete v tématu [ExpressRoute partneři a umístění](expressroute-locations.md) .

## <a name="technical-details"></a>Technické podrobnosti

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Jaké jsou technické požadavky pro připojení k místnímu umístění do Azure?

Požadavky najdete na [stránce s požadavky na ExpressRoute](expressroute-prerequisites.md) .

### <a name="are-connections-to-expressroute-redundant"></a>Jsou připojení k ExpressRoute redundantní?

Yes. Každý okruh ExpressRoute má redundantní dvojici vzájemného připojení, která je nakonfigurovaná tak, aby poskytovala vysokou dostupnost.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Ztratí se připojení v případě selhání některého z mých odkazů na ExpressRoute?

V případě, že jedno z vzájemných připojení selže, připojení k síti nebude ztraceno. K dispozici je redundantní připojení pro podporu zatížení vaší sítě a zajištění vysoké dostupnosti okruhu ExpressRoute. Můžete také vytvořit okruh v jiném umístění partnerského vztahu, abyste dosáhli odolnosti na úrovni okruhu.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Návody implementovat redundanci s privátním partnerským vztahem?

Několik okruhů ExpressRoute z různých umístění partnerských vztahů nebo až čtyř připojení ze stejného umístění partnerského vztahu může být připojeno ke stejné virtuální síti, aby se zajistila vysoká dostupnost v případě, že jeden okruh nebude k dispozici. Pak můžete [přiřadit vyšší váhu](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) jednomu z místních připojení a preferovat konkrétní okruh. Důrazně doporučujeme, aby si zákazníci nastavili aspoň dva okruhy ExpressRoute, aby se předešlo jednomu bodu selhání. 

V tématu Navrhování pro zajištění vysoké [dostupnosti najdete tady, kde najdete](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) [Návrh pro zotavení](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) po havárii.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Jak implementovat redundanci v partnerském vztahu Microsoftu?

Důrazně doporučujeme, když zákazníci používají partnerské vztahy Microsoftu pro přístup k veřejným službám Azure, jako je Azure Storage nebo Azure SQL, a také zákazníkům, kteří používají partnerský vztah Microsoftu pro Microsoft 365, že implementují více okruhů v různých umístěních partnerských vztahů, aby nedocházelo k jednomu bodu selhání. Zákazníci můžou inzerovat stejnou předponu na obou okruhech a používat [je jako nedokončená cesta](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) nebo inzerovat jiné předpony k určení cesty z místního prostředí.

Pro návrh pro zajištění vysoké dostupnosti se podívejte [sem](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) .

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Návody zajistěte vysokou dostupnost ve virtuální síti připojené k ExpressRoute?

Vysokou dostupnost můžete dosáhnout připojením až čtyř okruhů ExpressRoute ve stejném umístění partnerského vztahu k vaší virtuální síti nebo připojením okruhů ExpressRoute v různých umístěních partnerských vztahů (například Singapur, Singapur2) do vaší virtuální sítě. Pokud dojde k výpadku jednoho okruhu ExpressRoute, připojení převezme služby při selhání jiným okruhem ExpressRoute. Provoz, který opouští vaši virtuální síť, se ve výchozím nastavení směruje na základě ECMP (EQUAL cost multi-Path Routing). Pomocí váhy připojení můžete upřednostnit jeden okruh na jiný. Další informace najdete v tématu [optimalizace směrování ExpressRoute](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Návody zajistěte, aby byl můj provoz určený pro veřejné služby Azure, jako je Azure Storage a Azure SQL v partnerském vztahu Microsoftu nebo veřejný partnerský vztah, upřednostňován na cestě ExpressRoute?

Ve směrovačích musíte implementovat atribut *místní předvolby* , aby se zajistilo, že cesta z místního prostředí do Azure je vždy upřednostňovaná na okruhech ExpressRoute.

Další podrobnosti najdete [na základě](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) výběru cest protokolu BGP a konfigurací běžných směrovačů. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Pokud nemám společné umístění v cloudové výměně a poskytovatel mých služeb nabízí připojení typu Point-to-Point, musím objednat dvě fyzická připojení mezi místní sítí a Microsoftem?

Pokud poskytovatel služeb může navázat dva virtuální okruhy sítě Ethernet přes fyzické připojení, budete potřebovat jenom jedno fyzické připojení. Fyzické připojení (například optické vlákno) je ukončeno na zařízení vrstvy 1 (L1) (viz obrázek). Dva virtuální okruhy sítě Ethernet jsou označeny různými ID sítě VLAN, jeden pro primární okruh a druhý pro sekundární. Identifikátory sítě VLAN jsou v hlavičce vnějšího 802.1 Q Ethernet. Hlavička vnitřního 802.1 Q Ethernet (není zobrazená) je namapovaná na konkrétní [doménu směrování ExpressRoute](expressroute-circuit-peerings.md).

![Diagram zvýrazňuje primární a sekundární virtuální okruhy vrstvy 1 (L1), které tvoří fyzické připojení mezi přepínači na webu zákazníka a umístěním ExpressRoute.](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Můžu do Azure roztáhnout jednu z těchto sítí VLAN pomocí ExpressRoute?

Ne. Do Azure nepodporujeme rozšíření pro připojení vrstvy 2.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Můžu ve svém předplatném více než jeden okruh ExpressRoute?

Yes. Ve vašem předplatném můžete mít více než jeden okruh ExpressRoute. Výchozí omezení je nastaveno na hodnotu 10. V případě potřeby můžete kontaktovat podpora Microsoftu pro zvýšení limitu.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Můžu mít ExpressRoute okruhy od různých poskytovatelů služeb?

Yes. Můžete mít okruhy ExpressRoute s mnoha poskytovateli služeb. Každý okruh ExpressRoute je přidružen pouze k jednomu poskytovateli služeb. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Zobrazuje se dvě umístění partnerských vztahů ExpressRoute ve stejné metrice, například Singapur a Singapur2. Které umístění partnerského vztahu mám zvolit pro vytvoření svého okruhu ExpressRoute?
Pokud váš poskytovatel služeb nabízí ExpressRoute na obou lokalitách, můžete s vaším poskytovatelem spolupracovat a vybrat buď lokalitu, a nastavit ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Můžu ve stejné metrice více okruhů ExpressRoute? Můžu je propojit se stejnou virtuální sítí?

Yes. Můžete mít více okruhů ExpressRoute se stejným nebo jiným poskytovatelem služeb. Pokud má Metro více umístění partnerských vztahů ExpressRoute a okruhy se vytvářejí v různých umístěních partnerského vztahu, můžete je propojit se stejnou virtuální sítí. Pokud jsou okruhy vytvořeny ve stejném umístění partnerského vztahu, můžete propojit až čtyři okruhy se stejnou virtuální sítí.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Návody připojení svých virtuálních sítí k okruhu ExpressRoute

Základní kroky:

* Navažte okruh ExpressRoute a umožněte mu poskytovatele služeb.
* Vy nebo poskytovatel musíte nakonfigurovat partnerské vztahy protokolu BGP.
* Připojte virtuální síť k okruhu ExpressRoute.

Další informace najdete v tématu [pracovní postupy ExpressRoute pro zřizování okruhů a stavy okruhů](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Existují pro svůj okruh ExpressRoute hranice připojení?

Yes. Článek o [partnerech a umístěních ExpressRoute](expressroute-locations.md) poskytuje přehled hranic připojení pro okruh ExpressRoute. Připojení okruhu ExpressRoute je omezené na jednu geopolitickou oblast. Připojení se dá rozšířit na průřezové oblasti tím, že povolíte funkci ExpressRoute Premium.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Můžu se připojit k více než jedné virtuální síti okruhu ExpressRoute?

Yes. Na standardním okruhu ExpressRoute můžete mít až 10 připojení k virtuálním sítím a až 100 na [okruhu Premium ExpressRoute](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Mám několik předplatných Azure, která obsahují virtuální sítě. Můžu propojit virtuální sítě, které jsou v samostatných předplatných pro jeden okruh ExpressRoute?

Yes. Můžete propojit až 10 virtuálních sítí ve stejném předplatném, jako je okruh nebo různá předplatná, a to pomocí jednoho okruhu ExpressRoute. Toto omezení se dá zvýšit povolením funkce ExpressRoute Premium. Všimněte si, že pro vlastníka okruhu ExpressRoute se použijí poplatky za připojení a šířku pásma pro vyhrazený okruh. všechny virtuální sítě mají stejnou šířku pásma.

Další informace najdete v tématu [sdílení okruhu ExpressRoute napříč několika předplatnými](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Mám několik předplatných Azure přidružených k různým klientům Azure Active Directory nebo registraci smlouva Enterprise. Můžu propojit virtuální sítě, které jsou v samostatných klientech a registraci k jednomu okruhu ExpressRoute, který není ve stejném tenantovi nebo registraci?

Yes. ExpressRoute autorizace můžou zahrnovat hranice předplatného, tenanta a registrace bez nutnosti další konfigurace. Všimněte si, že pro vlastníka okruhu ExpressRoute se použijí poplatky za připojení a šířku pásma pro vyhrazený okruh. všechny virtuální sítě mají stejnou šířku pásma.

Další informace najdete v tématu [sdílení okruhu ExpressRoute napříč několika předplatnými](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Jsou virtuální sítě připojené ke stejnému okruhu izolované od sebe?

Ne. Z perspektivy směrování jsou všechny virtuální sítě propojené se stejným okruhem ExpressRoute součástí stejné domény směrování a nejsou od sebe vzájemně izolované. Pokud potřebujete izolaci tras, je nutné vytvořit samostatný okruh ExpressRoute.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Můžu mít jednu virtuální síť připojenou k více než jednomu okruhu ExpressRoute?

Yes. Jednu virtuální síť můžete propojit s až čtyřmi okruhy ExpressRoute ve stejném umístění nebo v různých umístěních partnerského vztahu. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Můžu získat přístup k Internetu z mých virtuálních sítí připojených k okruhům ExpressRoute?

Yes. Pokud jste v relaci protokolu BGP neinzerovali výchozí trasy (0.0.0.0/0) nebo předpony trasy k Internetu, můžete se připojit k Internetu z virtuální sítě propojené s okruhem ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Můžu blokovat připojení k Internetu virtuálním sítím, které jsou připojené k okruhům ExpressRoute?

Yes. Můžete inzerovat výchozí trasy (0.0.0.0/0) pro blokování veškerého připojení k Internetu virtuálním počítačům nasazeným ve virtuální síti a směrování všech přenosů přes okruh ExpressRoute.

> [!NOTE]
> Pokud je inzerovaná trasa 0.0.0.0/0 vyjmuta z inzerovaných tras (například z důvodu výpadku nebo chyby konfigurace), Azure poskytne [systémovou trasu](../virtual-network/virtual-networks-udr-overview.md#system-routes) k prostředkům v připojené Virtual Network, aby bylo zajištěno připojení k Internetu.  Aby se zajistilo zablokování provozu na internetu, doporučuje se umístit skupinu zabezpečení sítě ve všech podsítích s odchozím pravidlem Odepřít pro internetový provoz.

Pokud budete inzerovat výchozí trasy, vynutíme provoz na služby nabízené přes partnerský vztah Microsoftu (například Azure Storage a SQL DB) zpátky do svého místního prostředí. Budete muset nakonfigurovat směrovače tak, aby vracely provoz do Azure prostřednictvím cesty partnerského vztahu Microsoftu nebo přes Internet. Pokud jste povolili koncový bod služby pro danou službu, není přenos do služby vynucený pro vaše místní prostředí. Provoz zůstává v páteřní síti Azure. Další informace o koncových bodech služby najdete v tématu [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json) .

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Můžou se virtuální sítě propojené se stejným okruhem ExpressRoute vzájemně mluvit?

Yes. Virtuální počítače nasazené ve virtuálních sítích, které jsou připojené ke stejnému okruhu ExpressRoute, můžou vzájemně komunikovat.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Můžu v kombinaci s ExpressRoute používat připojení Site-to-site pro virtuální sítě?

Yes. ExpressRoute může existovat společně s sítěmi VPN typu Site-to-site. Přečtěte si téma [Konfigurace ExpressRoute a připojení mezi lokalitami](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Proč je ve virtuální síti přidružená veřejná IP adresa k bráně ExpressRoute?

Veřejná IP adresa se používá jenom pro interní správu a nepředstavuje bezpečnostní riziko vaší virtuální sítě.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Existují omezení počtu tras, jak můžu inzerovat?

Yes. U privátního partnerského vztahu a 200 pro partnerský vztah Microsoftu přijímáme až 4000 předpony tras. Pokud povolíte funkci ExpressRoute Premium, můžete to zvýšit na trasy 10 000 pro privátní partnerské vztahy.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Existují omezení pro rozsahy IP adres, které mohu inzerovat přes relaci protokolu BGP?

Pro relaci protokolu BGP partnerského vztahu Microsoftu nepřijímáme privátní předpony (RFC1918). U Microsoftu i privátního partnerského vztahu přijímáme libovolnou velikost předpon (až do/32).

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Co se stane, když překročím limity protokolu BGP?

Relace protokolu BGP budou vyřazeny. Budou obnoveny až po překročení počtu předpon.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Jaká je doba blokování protokolu BGP ExpressRoute? Dá se upravit?

Doba blokování je 180. Zprávy Keep-Alive se odesílají každých 60 sekund. Jedná se o pevná nastavení na straně Microsoftu, která se nedají změnit. Je možné nakonfigurovat jiné časovače a parametry relace protokolu BGP budou odpovídajícím způsobem vyjednávat.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Můžu změnit šířku pásma okruhu ExpressRoute?

Ano, můžete se pokusit zvýšit šířku pásma okruhu ExpressRoute v Azure Portal nebo pomocí PowerShellu. Pokud je k dispozici kapacita na fyzickém portu, na kterém byl váš okruh vytvořen, vaše změna bude úspěšná. 

Pokud se vaše změna nezdařila, znamená to, že na aktuálním portu není dostatek volné kapacity a potřebujete vytvořit nový okruh ExpressRoute s vyšší šířkou pásma nebo pokud v tomto umístění není žádná další kapacita, v takovém případě nebudete moci zvětšit šířku pásma. 

Bude také nutné dodržet poskytovatele připojení, aby bylo zajištěno, že budou aktualizovat omezení v rámci jejich sítí, aby podporovaly zvýšení šířky pásma. Nemůžete ale omezit šířku pásma okruhu ExpressRoute. Musíte vytvořit nový okruh ExpressRoute s menší šířkou pásma a odstranit starý okruh.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Návody změnit šířku pásma okruhu ExpressRoute?

Šířku pásma okruhu ExpressRoute můžete aktualizovat pomocí rutiny REST API nebo PowerShellu.

## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>Co je ExpressRoute Premium?

ExpressRoute Premium je kolekce následujících funkcí:

* Zvýšil se limit směrovací tabulky z 4000 tras na 10 000 tras pro privátní partnerské vztahy.
* Vyšší počet virtuální sítě a Global Reach ExpressRoute připojení, která je možné povolit u okruhu ExpressRoute (výchozí hodnota je 10). Další informace najdete v tabulce [omezení ExpressRoute](#limits) .
* Připojení k Microsoft 365
* Globální konektivita přes Microsoft Core Network. Nyní můžete propojit virtuální síť v jedné geopolitické oblasti s okruhem ExpressRoute v jiné oblasti.<br>
    **Příklady:**

    *  Virtuální síť vytvořená v Evropě se dá propojit s okruhem ExpressRoute vytvořeným v silikonovém sedlu. 
    *  V partnerském vztahu Microsoftu se dá inzerovat předpony z dalších geopolitických oblastí, například SQL Azure v oblasti Evropa – západ od okruhu v Silicon sedlu.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Kolik připojení virtuální sítě a ExpressRoute Global Reach můžu povolit u okruhu ExpressRoute, pokud jsem povolil ExpressRoute Premium?

V následujících tabulkách jsou uvedena omezení ExpressRoute a počet virtuální sítě a Global Reach ExpressRoute připojení na ExpressRoute okruh:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Návody povolit ExpressRoute Premium?

Pokud je funkce povolená, můžete povolit funkce ExpressRoute Premium a můžete je vypnout aktualizací stavu okruhu. V době vytváření okruhu můžete povolit ExpressRoute Premium nebo můžete volat rutinu REST API/PowerShellu.

### <a name="how-do-i-disable-expressroute-premium"></a>Návody zakázat ExpressRoute Premium?

ExpressRoute Premium můžete zakázat voláním rutiny REST API nebo PowerShellu. Než zakážete ExpressRoute Premium, musíte se ujistit, že vaše připojení musí splňovat výchozí omezení. Pokud vaše využití škáluje více než výchozí omezení, požadavek na zakázání ExpressRoute Premium se nezdařil.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Můžu si vybrat požadované funkce ze sady funkcí Premium?

Ne. Nemůžete si vybrat tyto funkce. Když zapnete ExpressRoute Premium, povolíme všechny funkce.

### <a name="how-much-does-expressroute-premium-cost"></a>Kolik stojí ExpressRoute Premium?

Náklady najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Platíte za ExpressRoute Premium navíc ke standardním ExpressRoute poplatkům?

Yes. Poplatky za ExpressRoute Premium se vztahují na poplatky za okruhy ExpressRoute a poplatky, které vyžaduje poskytovatel připojení.

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
ExpressRoute Local je k dispozici v umístěních partnerských vztahů, kde jedna nebo dvě oblasti Azure jsou blízko. Není k dispozici v umístění partnerského vztahu, kde neexistuje oblast Azure v tomto státě nebo zemi nebo oblasti. Přečtěte si prosím přesná mapování na [stránce umístění](expressroute-locations-providers.md).  

## <a name="expressroute-for-microsoft-365"></a>ExpressRoute pro Microsoft 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-microsoft-365-services"></a>Návody vytvořit okruh ExpressRoute pro připojení ke službám Microsoft 365?

1. Přečtěte si [stránku předpoklady ExpressRoute](expressroute-prerequisites.md) a ujistěte se, že splňujete požadavky.
2. Pokud chcete zajistit splnění požadavků na připojení, Projděte si seznam poskytovatelů služeb a umístění v článku [partneři a umístění ExpressRoute](expressroute-locations.md) .
3. Naplánujte požadavky na kapacitu pomocí kontroly [Plánování sítě a optimalizace výkonu pro Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance).
4. Postupujte podle kroků uvedených v pracovních postupech a nastavte možnosti připojení [ExpressRoute pracovní postupy pro zřizování okruhů a stavy okruhů](expressroute-workflows.md).

> [!IMPORTANT]
> Ujistěte se, že jste povolili doplněk ExpressRoute Premium při konfiguraci připojení ke službě Microsoft 365 Services.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-microsoft-365-services"></a>Můžou moje stávající okruhy ExpressRoute podporovat připojení ke službě Microsoft 365 Services?

Yes. Váš stávající okruh ExpressRoute je možné nakonfigurovat tak, aby podporoval připojení k Microsoft 365 službám. Ujistěte se, že máte dostatečnou kapacitu pro připojení k Microsoft 365 Services a že jste povolili doplněk Premium. [Plánování sítě a optimalizace výkonu pro Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance) vám pomůžou plánovat potřeby připojení. Přečtěte si také článek [Vytvoření a úprava okruhu ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-microsoft-365-services-can-be-accessed-over-an-expressroute-connection"></a>K jakým službám Microsoft 365 je možné přistupovat prostřednictvím připojení ExpressRoute?

Aktuální seznam služeb podporovaných přes ExpressRoute Microsoft 365 najdete na stránce [adresy URL a rozsahy IP adres](/microsoft-365/enterprise/urls-and-ip-address-ranges) .

### <a name="how-much-does-expressroute-for-microsoft-365-services-cost"></a>Kolik stojí ExpressRoute Microsoft 365 služeb?

Služba Microsoft 365 Services vyžaduje, aby byl povolený doplněk Premium. Náklady najdete na [stránce s podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-regions-is-expressroute-for-microsoft-365-supported-in"></a>Které oblasti jsou ExpressRoute pro Microsoft 365 podporovány v?

Informace najdete v tématu [partneři a umístění ExpressRoute](expressroute-locations.md) .

### <a name="can-i-access-microsoft-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Můžu získat přístup k Microsoft 365 přes Internet, i když byl ExpressRoute nakonfigurovaný pro moji organizaci?

Yes. Koncové body služby Microsoft 365 jsou dosažitelné přes Internet, i když je ExpressRoute pro vaši síť nakonfigurovaný. Pokud je síť ve vašem umístění nakonfigurovaná tak, aby se připojovala k Microsoft 365 službám prostřednictvím ExpressRoute, obraťte se prosím na síťový tým organizace.

### <a name="how-can-i-plan-for-high-availability-for-microsoft-365-network-traffic-on-azure-expressroute"></a>Jak mohu naplánovat vysokou dostupnost pro Microsoft 365 síťový provoz v Azure ExpressRoute?
Přečtěte si doporučení k zajištění [vysoké dostupnosti a převzetí služeb při selhání pomocí Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Můžu získat přístup ke službám pro Office 365 USA státní komunity (RSZ) prostřednictvím okruhu ExpressRoute pro státní správu Azure USA?

Yes. Koncové body služby Office 365 RSZ jsou dosažitelné prostřednictvím ExpressRoute pro státní správu Azure USA. Nejdřív ale musíte otevřít lístek podpory na Azure Portal a zadat tak předpony, které chcete inzerovat Microsoftu. Připojení ke službám Office 365 RSZ bude po vyřešení lístku podpory navázáno. 

## <a name="route-filters-for-microsoft-peering"></a>Filtry tras pro partnerský vztah Microsoftu

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Při prvním zapnutí partnerského vztahu Microsoftu se tyto trasy zobrazí?

Nebudete se zobrazovat žádné trasy. Abyste mohli začít s předponami, musíte k tomuto okruhu připojit filtr směrování. Pokyny najdete v tématu [Konfigurace filtrů tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Vypnul (a) jsem partnerský vztah Microsoftu a teď se snažím vybrat Exchange Online, ale při tom se zobrazí chyba, že k tomu nemám autorizaci.

Při použití filtrů směrování může libovolný zákazník zapnout partnerský vztah Microsoftu. Pro využívání služeb Microsoft 365 však stále potřebujete oprávnění Microsoft 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Jak mám povolit partnerské vztahy Microsoftu před 1. srpna 2017, jak mohu využít filtry tras?

Stávající okruh bude pokračovat ve inzerci prefixů pro Microsoft 365. Pokud chcete přidat reklamy veřejné předpony Azure přes stejný partnerský vztah Microsoftu, můžete vytvořit filtr tras, vybrat služby, které potřebujete inzerovat (včetně Microsoft 365ch služeb, které potřebujete), a připojit filtr k partnerskému vztahu Microsoftu. Pokyny najdete v tématu [Konfigurace filtrů tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Partnerský vztah Microsoftu mám na jednom místě, teď se ho snažím povolit v jiném umístění a nezobrazuje se žádné předpony.

* Partnerské vztahy Microsoftu okruhů ExpressRoute, které byly nakonfigurované před 1. srpna 2017, budou mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, a to i v případě, že nejsou definované filtry směrování.

* Partnerský vztah Microsoftu pro okruhy ExpressRoute, které jsou nakonfigurované na nebo od 1. srpna 2017, nebudou mít inzerované předpony, dokud není k okruhu připojen filtr tras. Ve výchozím nastavení se nezobrazí žádné předpony.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
