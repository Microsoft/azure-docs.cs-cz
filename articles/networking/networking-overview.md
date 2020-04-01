---
title: Přehled síťových služeb Azure
description: Získejte informace o síťových službách v Azure a jejich možnostech – služby připojení, služby ochrany aplikací, služby doručování aplikací a monitorování sítě.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: c6b845eda1df39ccf5e4b2b1d6a615f3bc932b66
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474932"
---
# <a name="azure-networking-services-overview"></a>Přehled síťových služeb Azure

Síťové služby v Azure poskytují celou řadu síťových funkcí, které lze použít společně nebo samostatně. Další informace o nich zobrazíte kliknutím na některou z následujících klíčových funkcí:
- [**Služby připojení:**](#connect)Propojte prostředky Azure a místní prostředky pomocí některé nebo kombinace těchto síťových služeb v Azure – virtuální síť (Virtuální síť), Virtuální WAN, ExpressRoute, BRÁNA VPN, Brána virtuální sítě NAT, Azure DNS, služba partnerského vztahu a Azure Bastion.
- [**Služby ochrany aplikací**](#protect) Chraňte své aplikace pomocí některé nebo kombinace těchto síťových služeb v Azure – privátní odkaz, ochrana DDoS, brána firewall, skupiny zabezpečení sítě, brána firewall webových aplikací a koncové body virtuální sítě.
- [**Služby doručování aplikací**](#deliver) Doručujte aplikace v síti Azure pomocí některé nebo kombinace těchto síťových služeb v Azure – síť doručování obsahu (CDN), služba Azure Front Door Service, Traffic Manager, Aplikační brána, Internet Analyzer a Nástroj pro vyrovnávání zatížení.
- [**Monitorování sítě**](#monitor) – Monitorujte síťové prostředky pomocí některé nebo kombinace těchto síťových služeb v Azure – sledování sítě, monitor expressroute, azure monitor nebo přístupový bod terminálu virtuální sítě (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Služby připojení
 
Tato část popisuje služby, které poskytují připojení mezi prostředky Azure, připojení z místní sítě k prostředkům Azure a pobočky připojení v Azure – virtuální síť (VNet), Virtuální WAN, ExpressRoute, BRÁNA VPN, virtuální síť NAT Gateway, Azure DNS, Služba partnerského vztahu Azure a Azure Bastion.

|Služba|Proč používat?|Scénáře|
|---|---|---|
|[Virtuální síť](#vnet)|Umožňuje prostředkům Azure bezpečně komunikovat mezi sebou, přes Internet a místní sítě.| <p>[Filtrování provozu sítě](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Směrování provozu sítě](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Omezení síťového přístupu k prostředkům](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Připojení virtuálních sítí](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Rozšíří vaše místní sítě do cloudu Microsoftu přes privátní připojení, které usnadňuje poskytovatel připojení.|<p>[Vytvoření a úprava okruhu ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Propojení virtuální sítě s okruhem ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Konfigurace a správa filtrů tras pro okruhy ExpressRoute](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Odešle šifrovaný provoz mezi virtuální sítí Azure a místním umístěním přes veřejný Internet.|<p>[Připojení typu site-to-site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Připojení virtuální sítě k virtuální síti](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Připojení typu point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Virtuální síť WAN](#virtualwan)|Optimalizuje a automatizuje připojení poboček do Azure a jeho prostřednictvím. Oblasti Azure slouží jako centra, ke kterým můžete připojit pobočky.|<p>[Připojení mezi lokalitami](../virtual-wan/virtual-wan-site-to-site-portal.md), [připojení ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Hostiteli domén DNS, které poskytují překlad názvů pomocí infrastruktury Microsoft Azure.|<p>[Hostování vaší domény v Azure DNS](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Vytvoření záznamů DNS pro webovou aplikaci](../dns/dns-web-sites-custom-domain.md)</p> <p>[Vytvoření záznamu aliasu pro Traffic Manager](../dns/tutorial-alias-tm.md)</p> <p>[Vytvoření záznamu aliasu pro veřejnou IP adresu](../dns/tutorial-alias-pip.md)</p> <p>[Vytvoření záznamu aliasu pro záznam prostředku zóny](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Nakonfigurujte zabezpečené a bezproblémové připojení RDP/SSH k virtuálním počítačům přímo na portálu Azure přes TLS. Když se připojíte přes Azure Bastion, vaše virtuální počítače nepotřebují veřejnou IP adresu|<p>[Vytvoření hostitele služby Azure Bastion](../bastion/bastion-create-host-portal.md)</p><p>[Připojení pomocí SSH k virtuálnímu počítači s Linuxem](../bastion/bastion-connect-vm-ssh.md)</p><p>[Připojení pomocí rdp k virtuálnímu virtuálnímu mísu windows](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Brána NAT virtuální sítě](#nat)|Vytvořte bránu NAT, která zajistí odchozí připojení pro virtuální počítač.|<p>[Vytvoření brány NAT](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Služba partnerského vztahu Azure (preview)](#azurepeeringservice)|Spolupracujte s poskytovateli služeb a zajistěte optimální a spolehlivé směrování do cloudu Microsoftu přes veřejnou síť.|<p>[Registrace služby Partnerského vztahu Azure](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Virtuální síť

Virtuální síť Azure (VNet) je základní stavební blok pro vaši privátní síť v Azure. Virtuální sítě můžete použít k:
- **Komunikujte mezi prostředky Azure**: Virtuální počítače a několik dalších typů prostředků Azure můžete nasadit do virtuální sítě, jako jsou prostředí služby Azure App Service, služba Azure Kubernetes Service (AKS) a škálovací sady virtuálních počítačů Azure. Úplný seznam prostředků Azure, které můžete nasadit do virtuální sítě, najdete v tématu věnovaném [integraci virtuální sítě do služeb](../virtual-network/virtual-network-for-azure-services.md).
- **Vzájemná komunikace**: Virtuální sítě můžete vzájemně propojit a umožnit tak, aby prostředky v obou virtuálních sítích vzájemně komunikovaly pomocí partnerského vztahu virtuální sítě. Propojené virtuální sítě se můžou nacházet ve stejné oblasti Azure nebo v různých oblastech. Další informace naleznete v [tématu Partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md).
- **Komunikovat k Internetu**: Všechny prostředky ve virtuální síti mohou ve výchozím nastavení komunikovat odchozí k internetu. Příchozí komunikaci s prostředkem můžete umožnit tím, že prostředku přiřadíte veřejnou IP adresu nebo veřejný Load Balancer. Ke správě [odchozích](../virtual-network/virtual-network-public-ip-address.md) připojení můžete také použít veřejné IP adresy nebo veřejný [systém vyrovnávání zatížení.](../load-balancer/load-balancer-overview.md)
- **Komunikace s místními sítěmi**: Místní počítače a sítě můžete připojit k virtuální síti pomocí [brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoute](../expressroute/expressroute-introduction.md).

Další informace najdete v tématu [Co je virtuální síť Azure?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute umožňuje rozšířit místní sítě do cloudu Microsoftu přes privátní připojení, které usnadňuje poskytovatel připojení. Toto připojení je soukromé. Provoz se nepřenáší přes internet. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365.  Další informace naleznete v tématu [Co je ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
Brána VPN pomáhá vytvářet šifrovaná mezimístní připojení k vaší virtuální síti z místních umístění nebo vytvářet šifrovaná připojení mezi virtuálními sítěmi. Existují různé konfigurace, které jsou k dispozici pro připojení brány VPN, jako je například site-to-site, point-to-site nebo vnet k virtuální síti.
Následující diagram znázorňuje více připojení VPN mezi lokalitami ke stejné virtuální síti.

![Připojení brány Azure vpn od webu k webu](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Další informace o různých typech připojení VPN naleznete v tématu [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtuální síť WAN
Azure Virtual WAN je síťová služba, která poskytuje optimalizované a automatizované připojení poboček do Azure a jeho prostřednictvím. Oblasti Azure slouží jako centra, ke kterým můžete připojit pobočky. Páteřní síť Azure můžete využít k propojení větví a připojení k síti mezi větvemi. Azure Virtual WAN sdružuje mnoho cloudových připojení Azure služby, jako je site-to-site VPN, ExpressRoute, point-to-site uživatele VPN do jednoho provozního rozhraní. Připojení k virtuálním sítím Azure se navíjí pomocí připojení k virtuální síti. Další informace najdete v tématu [Co je virtuální wan Azure?](../virtual-wan/virtual-wan-about.md).

![Diagram virtuální sítě WAN](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS je hostitelská služba určená pro domény DNS, která zajišťuje překlad názvů s využitím infrastruktury Microsoft Azure. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. Další informace najdete v tématu [Co je Azure DNS?](../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Služba Azure Bastion je nová plně platformně spravovaná služba PaaS, kterou zřídíte ve virtuální síti. Poskytuje bezpečné a bezproblémové připojení RDP/SSH k vašim virtuálním počítačům přímo na portálu Azure přes TLS. Když se připojíte přes Azure Bastion, virtuální počítače nepotřebují veřejnou IP adresu. Další informace najdete v tématu [Co je Azure Bastion?](../bastion/bastion-overview.md).

![Architektura Azure Bastion](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Brána NAT virtuální sítě
Virtuální síť NAT (překlad síťových adres) zjednodušuje pouze odchozí připojení k Internetu pro virtuální sítě. Při konfiguraci v podsíti používá všechna odchozí připojení zadané statické veřejné IP adresy. Odchozí připojení je možné bez nástroje pro vyrovnávání zatížení nebo veřejných IP adres přímo připojených k virtuálním počítačům. Další informace najdete v tématu [Co je brána NAT virtuální sítě?](../virtual-network/nat-overview.md) 

![Brána NAT virtuální sítě](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Služba partnerského vztahu Azure
Služba Partnerského vztahu Azure vylepšuje připojení zákazníků ke cloudovým službám Microsoftu, jako jsou Office 365, Dynamics 365, software jako služba (SaaS), Azure nebo jakékoli služby Microsoftu přístupné prostřednictvím veřejného internetu. Další informace najdete v tématu [Co je služba partnerského vztahu Azure?](../peering-service/about.md).

## <a name="application-protection-services"></a><a name="protect"></a>Služby ochrany aplikací

Tato část popisuje síťové služby v Azure, které pomáhají chránit vaše síťové prostředky – Chraňte své aplikace pomocí některé nebo kombinace těchto síťových služeb v Azure – Privátní odkaz, ochrana DDoS, brána firewall, skupiny zabezpečení sítě, brána firewall pro webové aplikace a koncové body virtuální sítě.

|Služba|Proč používat?|Scénář|
|---|---|---|
|[Ochrana DDoS](#ddosprotection) |Vysoká dostupnost pro vaše aplikace s ochranou před nadměrnými poplatky za provoz IP|[Správa ochrany Azure DDoS](../virtual-network/manage-ddos-protection.md)|
|[Brána firewall webové aplikace](#waf)|<p>[Azure WAF s aplikační bránou](../web-application-firewall/ag/ag-overview.md) poskytuje místní ochranu entitám ve veřejném a soukromém adresním prostoru</p><p>[Azure WAF s předními dveřmi](../web-application-firewall/afds/afds-overview.md) poskytuje ochranu na okraji sítě pro veřejné koncové body.</p>|<p>[Konfigurace pravidel ochrany botů](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Konfigurace vlastního kódu odpovědi](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Konfigurace pravidel omezení PROTOKOLU IP](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Konfigurovat pravidlo omezení rychlosti](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Brána Azure Firewall](#firewall)|Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností v cloudu.|<p>[Nasazení brány Azure firewall ve virtuální síti](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- Nasazení brány Azure Firewall v hybridní síti](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrování příchozích přenosů pomocí DNAT brány Azure Firewall](../firewall/tutorial-firewall-dnat.md)</p>|
|[Skupiny zabezpečení sítě](#nsg)|Úplné podrobné řízení distribuovaných koncových uzlů ve Virtuálním virtuálním míse/podsíti pro všechny toky síťového provozu|[Filtrování provozu sítě s využitím skupin zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)|
|[Koncové body služby pro virtuální síť](#serviceendpoints)|Umožňuje omezit přístup k síti na některé prostředky služby Azure na podsíť virtuální sítě.|[Omezení síťového přístupu k prostředkům PaaS](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Privátní propojení](#privatelink)|Umožňuje přístup ke službám Azure PaaS (například Azure Storage a SQL Database) a službám Hostovaným zákazníkem nebo partnerem Azure prostřednictvím privátního koncového bodu ve vaší virtuální síti.|<p>[Vytvoření privátního koncového bodu](../private-link/create-private-endpoint-portal.md)</p><p>[Vytvoření služby privátního propojení](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) poskytuje protiopatření proti nejsofistikovanějším hrozbám DDoS. Služba poskytuje rozšířené možnosti zmírnění DDoS pro vaše aplikace a prostředky nasazené ve virtuálních sítích. Zákazníci využívající azure ddos ochranu mají navíc přístup k podpoře Rychlé odezvy DDoS, aby během aktivního útoku zapojili odborníky na DDoS.

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Brána firewall webové aplikace

[Azure Web Application Firewall](../web-application-firewall/overview.md) (WAF) poskytuje ochranu webových aplikací před běžnými webovými zneužitími a chybami zabezpečení, jako je vkládání SQL a skriptování mezi weby. Azure WAF poskytuje ochranu mimo pole z 10 nejvyšších chyb zabezpečení OWASP prostřednictvím spravovaných pravidel. Zákazníci mohou navíc také konfigurovat vlastní pravidla, což jsou pravidla spravovaná zákazníkem, která poskytují dodatečnou ochranu na základě zdrojového rozsahu IP adres a atributy požadavků, jako jsou záhlaví, soubory cookie, datová pole formuláře nebo parametry řetězce dotazu.

Zákazníci se můžou rozhodnout nasadit [Azure WAF s aplikační bránou,](../application-gateway/waf-overview.md) která poskytuje místní ochranu entitám ve veřejném a soukromém adresním prostoru. Zákazníci se také mohou rozhodnout nasadit [Azure WAF s front door,](../frontdoor/waf-overview.md) který poskytuje ochranu na okraji sítě pro veřejné koncové body.

![Web Application Firewall (Brána firewall webových aplikací)](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Brána Azure Firewall
Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Pomocí azure firewall, můžete centrálně vytvářet, vynucovat a protokolovat zásady aplikace a připojení k síti napříč předplatnými a virtuálními sítěmi. Brána Azure Firewall používá statickou veřejnou IP adresu pro prostředky virtuální sítě a díky tomu umožňuje venkovním bránám firewall identifikovat provoz pocházející z vaší virtuální sítě. 

Další informace o Azure Firewall najdete v [dokumentaci k bráně Azure Firewall](../firewall/overview.md).

![Přehled brány firewall](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Skupiny zabezpečení sítě
Pomocí skupiny zabezpečení sítě můžete filtrovat síťový provoz do a z prostředků Azure ve virtuální síti Azure. Další informace naleznete v [tématu Přehled zabezpečení](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Koncové body služby
Koncové body služby virtuální sítě rozšiřují privátní adresní prostor vaší virtuální sítě a její identitu do služeb Azure přes přímé připojení. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Provoz z vaší virtuální sítě do služby Azure vždy zůstává v páteřní síti Microsoft Azure. Další informace naleznete v tématu [Koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).

![Koncové body služby pro virtuální síť](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Azure Private Link](../private-link/private-link-overview.md) umožňuje přístup ke službám Azure PaaS (například Azure Storage a SQL Database) a službám Azure hostovaným zákazníkům nebo partnerským službám prostřednictvím privátního koncového bodu ve vaší virtuální síti.
Provoz mezi vaší virtuální sítí a službou putuje páteřní sítí Microsoftu. Vystavení vaší služby veřejnému internetu již není nutné. Můžete vytvořit vlastní službu privátního propojení ve virtuální síti a doručit ji svým zákazníkům.

![Soukromý koncový bod – přehled](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Služby doručování aplikací

Tato část popisuje síťové služby v Azure, které pomáhají doručovat aplikace – Sledování sítě, ExpressRoute Monitor, Azure Monitor nebo Přístupový bod terminálu virtuální sítě (TAP).

|Služba|Proč používat?|Scénář|
|---|---|---|
|[Síť pro doručování obsahu](#cdn)|Poskytuje uživatelům obsah s velkou šířkou pásma. Sítě CDN ukládají obsah uložený v mezipaměti na hraničních serverech v umístěních bodů přítomnosti (POP), která jsou blízko koncovým uživatelům, aby se minimalizovala latence|<p>[Přidání cdn do webové aplikace](../cdn/cdn-add-to-web-app.md)</p> <p>[- Přístup k objektům BLOB úložiště pomocí vlastní domény Azure CDN přes protokol HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Přidání vlastní domény do koncového bodu Azure CDN](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Konfigurace HTTPS pro vlastní doménu Azure CDN](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Umožňuje definovat, spravovat a monitorovat globální směrování pro webový provoz optimalizací pro nejlepší výkon a okamžité globální převzetí služeb při selhání pro vysokou dostupnost.|<p>[Přidání vlastní domény do služby Azure Front Door Service](../frontdoor/front-door-custom-domain.md)</p> <p>[Konfigurace protokolu HTTPS ve vlastní doméně předních dveří](../frontdoor/front-door-custom-domain-https.md)</p><p>[Nastavení zásad brány firewall pro geografické filtrování webových aplikací](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|Distribuuje provoz na základě SLUŽBY DNS do služeb v globálních oblastech Azure a zároveň poskytuje vysokou dostupnost a odezvu.|<p> [Směrování provozu pro zajištění nízké latence](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Směrování provozu do prioritního koncového bodu](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Řízení provozu s váženými koncovými body](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Směrovat provoz na základě geografické polohy koncového bodu](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Směrování provozu na základě podsítě uživatele](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Load Balancer](#loadbalancer)|Poskytuje regionální vyrovnávání zatížení směrováním provozu napříč zónami dostupnosti a do virtuálních sítí. Poskytuje interní vyrovnávání zatížení směrováním provozu napříč a mezi prostředky k vytvoření místní aplikace.|<p> [Vyrovnávání zatížení internetového provozu do virtuálních počítačů](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Provoz vyvažovacího diagramu napříč virtuálními počítači ve virtuální síti](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Port forwardový provoz na konkrétní port na konkrétních virtuálních počítačích](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Konfigurace pravidel vyrovnávání zatížení a odchozích pravidel](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Azure Application Gateway je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací.|<p>[Přímý webový provoz s využitím služby Azure Application Gateway](../application-gateway/quick-create-portal.md)</p><p>[Kurz: Konfigurace aplikační brány s ukončením TLS pomocí portálu Azure](../application-gateway/create-ssl-portal.md)</p><p>[Vytvoření aplikační brány se směrováním založeným na cestě URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Síť pro doručování obsahu
Azure Content Delivery Network (CDN) nabízí vývojářům globální řešení pro rychlé doručování širokopásmového obsahu uživatelům díky ukládání obsahu do mezipaměti na fyzických uzlech strategicky umístěných po celém světě. Další informace o Azure CDN najdete v tématu [Azure Content Delivery Network](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Služba Azure Front Door
Služba Azure Front Door vám umožňuje definovat, spravovat a monitorovat globální směrování webového provozu tím, že provádí optimalizaci pro nejlepší výkon a poskytuje okamžité globální převzetí služeb při selhání, aby byla zajištěna vysoká dostupnost. Se službou Front Door můžete transformovat svoje globální (zahrnující více oblastí) spotřebitelské a podnikové aplikace do robustních a vysoce výkonných přizpůsobených moderních aplikací, rozhraní API a obsahu, kterými s pomocí Azure oslovíte globální cílovou skupinu. Další informace naleznete v tématu [Azure Front Door](../frontdoor/front-door-overview.md).


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager je nástroj pro vyrovnávání zatížení provozu na základě DNS, který umožňuje optimálně distribuovat provoz do služeb napříč globálními oblastmi Azure při zajištění vysoké dostupnosti a rychlosti odezvy. Traffic Manager poskytuje řadu metod směrování provozu k distribuci provozu, jako je priorita, vážený provoz, výkon, geografické, vícehodnotové nebo podsítě. Další informace o metodách směrování provozu naleznete v tématu [Traffic Manager routing methods](../traffic-manager/traffic-manager-routing-methods.md).

Následující diagram znázorňuje směrování založené na prioritách koncového bodu pomocí Traffic Manageru:

![Metoda směrování provozu služby Priority azure traffic manageru](./media/networking-overview/priority.png)

Další informace o Traffic Manageru najdete v tématu [Co je Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Nástroj Pro vyrovnávání zatížení Azure poskytuje vysoce výkonnou vyrovnávání zatížení vrstvy 4 s nízkou latencí pro všechny protokoly UDP a TCP. Spravuje příchozí a odchozí připojení. Můžete nakonfigurovat veřejné a interní koncové body s vyrovnáváním zatížení. Můžete definovat pravidla pro mapování příchozích připojení do cílů back-endového fondu pomocí možností zjišťování stavu TCP a HTTP pro správu dostupnosti služeb. Další informace o nástroje pro vyrovnávání zatížení najdete v článku [přehled nástroje pro vyrovnávání zatížení.](../load-balancer/load-balancer-overview.md)

Následující obrázek znázorňuje vícevrstvou aplikaci orientosí na Internet, která využívá externí i interní nástroje pro vyrovnávání zatížení:

![Příklad azure balanceru](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací. Jedná se o řadič pro doručování aplikací (ADC) jako služba, která nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaše aplikace. Další informace najdete v tématu [Co je Azure Application Gateway?](../application-gateway/overview.md).

Následující diagram znázorňuje směrování založené na cestě url pomocí brány aplikace.

![Příklad brány aplikace](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Služby monitorování sítě
Tato část popisuje síťové služby v Azure, které pomáhají monitorovat síťové prostředky – Sledování sítě, ExpressRoute Monitor, Azure Monitor a Virtual Network TAP.

|Služba|Proč používat?|Scénář|
|---|---|---|
|[Network Watcher](#networkwatcher)|Pomáhá monitorovat a řešit problémy s připojením, pomáhá diagnostikovat problémy s VPN, NSG a směrováním, zachytávat pakety na vašem virtuálním počítači, automatizovat nástroje pro spouštění diagnostiky pomocí funkcí Azure a logických aplikací|<p>[Diagnostika potíží s filtrováním provozu virtuálních počítačů](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnostika potíží se směrováním virtuálních počítačů](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Monitorování komunikace mezi virtuálními virtuálními společnostmi](../network-watcher/connection-monitor.md)</p><p>[Diagnostika potíží s komunikací mezi sítěmi](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Protokolování síťového provozu do a z virtuálního počítače](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute Monitor](#expressroutemonitor)|Poskytuje monitorování výkonu sítě, dostupnosti a využití v reálném čase, pomáhá s automatickým zjišťováním topologie sítě, poskytuje rychlejší izolaci poruch, detekuje přechodné problémy se sítí, pomáhá analyzovat historické charakteristiky výkonu sítě, podporuje více předplatného|<p>[Konfigurace Network Performance Monitor pro ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Monitorování, metriky a výstrahy služby ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Pomáhá vám pochopit, jak si vaše aplikace vedou, a proaktivně identifikuje problémy, které je ovlivňují, a prostředky, na kterých jsou závislé.|<p>[Metriky a upozornění Traffic Manageru](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Diagnostika monitorování Azure pro standardní nástroj pro vyrovnávání zatížení](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Monitorování protokolů a metrik Azure Firewallu](../firewall/tutorial-diagnostics.md)</p><p>[Monitorování a protokolování Firewallu webových aplikací Azure](../frontdoor/waf-front-door-monitor.md)</p>|
|[TAP virtuální sítě](#vnettap)|Poskytuje nepřetržité streamování síťového provozu virtuálních strojů do kolektoru paketů, umožňuje řešení pro správu výkonu sítě a aplikací a nástroje pro analýzu zabezpečení.|[Vytvoření prostředku TAP virtuální sítě](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher poskytuje nástroje pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve virtuální síti Azure. Další informace naleznete v tématu [Co je sledování sítě?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute Monitor
Informace o zobrazení metrik obvodů ExpressRoute, diagnostických protokolů a výstrah najdete v tématu [Monitorování ExpressRoute, metriky a výstrahy](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Azure Monitor maximalizuje dostupnost a výkon vašich aplikací tím, že poskytuje ucelené řešení pro shromažďování, analýzu a telemetrii z vašich cloudových a místních prostředí. Pomůže vám při zjišťování stavu vašich aplikací a proaktivně identifikuje problémy, které je ovlivňují, a prostředky, na kterých jsou závislé. Další informace najdete v tématu [Přehled sledování Azure](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>TAP virtuální sítě
Azure virtual network TAP (Terminal Access Point) umožňuje nepřetržitě streamovat provoz sítě virtuálních strojů do kolektoru síťových paketů nebo analytického nástroje. Kolektor nebo analytický nástroj je poskytován partnerem [síťového virtuálního zařízení.](https://azure.microsoft.com/solutions/network-appliances/) 

Následující obrázek ukazuje, jak funguje tap virtuální sítě. 

![Jak funguje tap virtuální sítě](./media/networking-overview/virtual-network-tap-architecture.png)

Další informace naleznete v tématu [Co is Virtual Network TAP](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Další kroky

- Vytvořte si první virtuální síť a připojte k ní několik virtuálních počítačů, a to tak, že dokončíte kroky v článku [Vytvoření první virtuální sítě.](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
- Připojte počítač k virtuální síti provedením kroků v [článku Konfigurace připojení bodu k webu](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Vyrovnávání zatížení internetový provoz na veřejné servery dokončením kroky v [článku Vytvořit internetovou balancer.](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
 
 
   
