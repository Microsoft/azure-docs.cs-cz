---
title: Přehled síťových služeb Azure
description: Přečtěte si o síťových službách v Azure, včetně možností připojení, ochrany aplikací, doručování aplikací a služeb monitorování sítě.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: kumud
ms.openlocfilehash: 791a248c8fb10bc4db483451971d452a359c8a07
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569229"
---
# <a name="azure-networking-services-overview"></a>Přehled síťových služeb Azure

Síťové služby v Azure poskytují nejrůznější možnosti sítě, které se dají použít společně nebo samostatně. Pokud se chcete dozvědět víc o těchto klíčových funkcích, klikněte na některou z těchto klíčových funkcí:
- [**Služby připojení**](#connect): Propojte prostředky Azure a místní prostředky pomocí kterékoli nebo kombinace těchto síťových služeb v Azure-Virtual Network (VNET), Virtual WAN, ExpressRoute, VPN Gateway, brány NAT pro virtuální sítě, Azure DNS, služby partnerského vztahu a Azure bastionu.
- [**Služby ochrany aplikací**](#protect): Chraňte své aplikace pomocí kterékoli nebo kombinace těchto síťových služeb v Azure – Private Link, DDoS Protection, brány firewall, skupin zabezpečení sítě, firewallu webových aplikací a koncových bodů Virtual Network.
- [**Služby doručování aplikací**](#deliver): doručovat aplikace do sítě Azure pomocí kterékoli nebo kombinace těchto síťových služeb v azure-Content DELIVERY Network (CDN), služby Azure front-dveří, Traffic Manager, Application Gateway, Internet Analyzer a Load Balancer.
- [**Monitorování sítě**](#monitor): Sledujte své síťové prostředky pomocí kterékoli nebo kombinace síťových služeb v Azure-Network Watcher, ExpressRoute Monitor, Azure monitor nebo terminálu pro terminálový přístup k virtuální síti (klepněte na).

## <a name="connectivity-services"></a><a name="connect"></a>Služby připojení
 
Tato část popisuje služby, které poskytují připojení mezi prostředky Azure, připojením z místní sítě k prostředkům Azure a větví k připojení větví v Azure-Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN Gateway, službě NAT služby Virtual Network NAT, Azure DNS, službě Azure peering Service a Azure bastionu.

|Služba|Proč použít?|Scénáře|
|---|---|---|
|[Virtuální síť](#vnet)|Umožňuje prostředkům Azure zabezpečeně komunikovat mezi sebou, internetem a místními sítěmi.| <p>[Filtrování síťového provozu](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Směrování síťového provozu](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Omezení síťového přístupu k prostředkům](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Propojení virtuálních sítí](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Rozšiřuje vaše místní sítě do cloudu Microsoftu přes soukromé připojení, které usnadňuje poskytovatel připojení.|<p>[Vytvoření a úprava okruhu ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Propojení virtuální sítě s okruhem ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Konfigurace a Správa filtrů tras pro okruhy ExpressRoute](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Odesílá zašifrovaný provoz mezi virtuální sítí Azure a místním umístěním přes veřejný Internet.|<p>[Připojení typu site-to-site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Připojení typu VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Připojení typu point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Virtual WAN](#virtualwan)|Optimalizuje a automatizuje připojení větví k Azure. Oblasti Azure slouží jako centra, ke kterým se můžete připojit ke svým větvím.|<p>[Připojení Site-to-site](../virtual-wan/virtual-wan-site-to-site-portal.md), [připojení ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Hostuje domény DNS, které poskytují překlad názvů pomocí Microsoft Azure infrastruktury.|<p>[Hostování vaší domény v Azure DNS](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Vytvoření záznamů DNS pro webovou aplikaci](../dns/dns-web-sites-custom-domain.md)</p> <p>[Vytvořit záznam aliasu pro Traffic Manager](../dns/tutorial-alias-tm.md)</p> <p>[Vytvořit záznam aliasu pro veřejnou IP adresu](../dns/tutorial-alias-pip.md)</p> <p>[Vytvoří záznam aliasu pro záznam prostředku zóny.](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Nakonfigurujte zabezpečené a bezproblémové připojení RDP/SSH k virtuálním počítačům přímo v Azure Portal přes TLS. Když se připojujete přes Azure bastionu, virtuální počítače nepotřebují veřejnou IP adresu.|<p>[Vytvoření hostitele služby Azure Bastion](../bastion/bastion-create-host-portal.md)</p><p>[Připojení pomocí SSH k virtuálnímu počítači se systémem Linux](../bastion/bastion-connect-vm-ssh.md)</p><p>[Připojení pomocí protokolu RDP k virtuálnímu počítači s Windows](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Brána NAT služby Virtual Network](#nat)|Vytvořte bránu NAT pro zajištění odchozího připojení pro virtuální počítač.|<p>[Vytvoření brány NAT](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure Peering Service](#azurepeeringservice)|Spolupráci s poskytovateli služeb pro zajištění optimálního a spolehlivého směrování do cloudu Microsoftu prostřednictvím veřejné sítě.|<p>[Registrovat službu Azure peering Service](../peering-service/azure-portal.md)</p>|
| [Azure Edge Zone](#edge-zones)| Nasazení virtuálních počítačů, kontejnerů a dalších služeb do hraničních zón pro řešení požadavků s nízkou latencí a vysokou propustností.| <p>[Hraniční zóny s operátorem](edge-zones-overview.md#carrier)<p> <p>[Zóny privátního okraje](edge-zones-overview.md#private-edge-zones)</p>|
| [Orbital Azure](#orbital)| Komunikujte s vašimi kosmickými kosmickými nebo satelitními Constellations, stahováním a odesíláním dat, zpracujte data v cloudu, řetězte služby se službami Azure v jedinečných scénářích a vygenerujte produkty pro vaše zákazníky.  | <p>[Globální komunikace](azure-orbital-overview.md#global-communication)</p>  <p>[Uzemněné stanice partnerů](azure-orbital-overview.md#digital-transformation)</p>|
||||

### <a name="virtual-network"></a><a name="vnet"></a>Virtuální síť

Azure Virtual Network (VNet) je základní stavební blok vaší privátní sítě v Azure. Virtuální sítě můžete použít k těmto akcím:
- **Komunikace mezi prostředky Azure**: můžete nasadit virtuální počítače a několik dalších typů prostředků Azure do virtuální sítě, jako jsou Azure App Service prostředí, Azure Kubernetes Service (AKS) a Azure Virtual Machine Scale Sets. Úplný seznam prostředků Azure, které můžete nasadit do virtuální sítě, najdete v tématu věnovaném [integraci virtuální sítě do služeb](../virtual-network/virtual-network-for-azure-services.md).
- Mezi sebou navzájem **komunikují**: virtuální sítě můžete vzájemně propojit a umožnit tak komunikaci prostředků v obou virtuálních sítích pomocí partnerského vztahu virtuálních sítí. Propojené virtuální sítě se můžou nacházet ve stejné oblasti Azure nebo v různých oblastech. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).
- **Komunikace s internetem**: všechny prostředky ve virtuální síti můžou ve výchozím nastavení komunikovat odchozí připojení k Internetu. Příchozí komunikaci s prostředkem můžete umožnit tím, že prostředku přiřadíte veřejnou IP adresu nebo veřejný Load Balancer. Ke správě odchozích připojení můžete použít taky [veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md) nebo veřejné [Load Balancer](../load-balancer/load-balancer-overview.md) .
- **Komunikace s místními sítěmi**: místní počítače a sítě můžete připojit k virtuální síti pomocí [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoute](../expressroute/expressroute-introduction.md).

Další informace najdete v tématu [co je Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute umožňuje rozšiřování místních sítí do cloudu Microsoftu přes soukromé připojení, které usnadňuje poskytovatel připojení. Toto připojení je soukromé. Provoz se nepřenáší přes internet. V ExpressRoute můžete vytvořit připojení ke cloudovým službám, jako je Microsoft Azure, Microsoft 365 a Dynamics 365.  Další informace najdete v tématu [co je ExpressRoute?](../expressroute/expressroute-introduction.md).

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN Gateway vám pomůže vytvořit šifrovaná připojení mezi různými místy k virtuální síti z místních umístění nebo vytvářet šifrovaná připojení mezi virtuální sítě. K dispozici jsou různé konfigurace pro VPN Gateway připojení, například site-to-site, Point-to-site nebo VNet-to-VNet.
Následující diagram znázorňuje více připojení VPN typu Site-to-site ke stejné virtuální síti.

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Azure ExpressRoute":::

Další informace o různých typech připojení k síti VPN najdete v tématu [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtual WAN
Azure Virtual WAN je síťová služba, která poskytuje optimalizované a automatizované připojení větví k Azure. Oblasti Azure slouží jako centra, ke kterým se můžete připojit ke svým větvím. Můžete využít páteřní síť Azure a taky připojit větve a využít připojení k virtuální síti. Služba Azure Virtual WAN přináší dohromady mnoho služeb cloudového připojení Azure, jako je VPN typu Site-to-site, ExpressRoute, User VPN typu Point-to-site, do jediného provozního rozhraní. Připojení ke službě Azure virtuální sítě se vytváří pomocí připojení k virtuální síti. Další informace najdete v tématu [co je Azure Virtual WAN?](../virtual-wan/virtual-wan-about.md).

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Azure ExpressRoute":::

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS je hostitelská služba pro domény DNS, která poskytuje překlad názvů na IP adresy pomocí infrastruktury Microsoft Azure. Pokud své domény hostujete v Azure, můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. Další informace najdete v tématu [co je Azure DNS?](../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Služba Azure bastionu je nová plně spravovaná služba PaaS spravovaná platformou, kterou zřizujete v rámci vaší virtuální sítě. Poskytuje zabezpečené a bezproblémové připojení RDP/SSH k virtuálním počítačům přímo v Azure Portal přes TLS. Když se připojíte přes Azure Bastion, virtuální počítače nepotřebují veřejnou IP adresu. Další informace najdete v tématu [co je Azure bastionu?](../bastion/bastion-overview.md).

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Azure ExpressRoute":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Brána NAT služby Virtual Network
Virtual Network NAT (překlad síťových adres) zjednodušuje připojení k Internetu pouze pro virtuální sítě. Při konfiguraci v podsíti všechna odchozí připojení používá vaše zadané statické veřejné IP adresy. Odchozí připojení je možné bez nástroje pro vyrovnávání zatížení nebo veřejných IP adres, které jsou přímo připojené k virtuálním počítačům. Další informace najdete v tématu [co je to brána NAT služby Virtual Network](../virtual-network/nat-overview.md).

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Azure ExpressRoute":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Služba partnerských vztahů Azure
Azure peering Service vylepšuje připojení zákazníků ke cloudovým službám Microsoftu, jako jsou Microsoft 365, Dynamics 365, služby software jako služba (SaaS), Azure nebo jakékoli služby Microsoftu přístupné prostřednictvím veřejného Internetu. Další informace najdete v tématu [co je služba Azure peering Service?](../peering-service/about.md).

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

Hraniční zóna Azure je skupina nabídek od Microsoft Azure, které umožňují zpracování dat blízko uživateli. Virtuální počítače, kontejnery a jiné vybrané služby Azure můžete nasadit do hraničních zón a řešit tak požadavky aplikací s nízkou latencí a vysokou propustností. Další informace najdete v tématu [co je Azure Edge Zones?](edge-zones-overview.md).

### <a name="azure-orbital"></a><a name="orbital"></a>Orbital Azure

Azure Orbital je plně spravovaná cloudová pozemní stanice jako služba, která umožňuje komunikaci s vašimi kosmickými a satelitními Constellations, stahování a odesílání dat, zpracování dat v cloudu, řetězení služeb se službami Azure v jedinečných scénářích a generování produktů pro vaše zákazníky. Tento systém je založený na globální infrastruktuře Azure a globální vláknové síti s nízkou latencí. Další informace najdete v tématu [co je Azure Orbital?](azure-orbital-overview.md).

:::image type="content" source="./media/azure-orbital-overview/orbital-communications-use-flow.png" alt-text="Azure ExpressRoute":::

## <a name="application-protection-services"></a><a name="protect"></a>Služby ochrany aplikací

Tato část popisuje síťové služby v Azure, které vám pomůžou chránit vaše síťové prostředky – chránit své aplikace pomocí kterékoli nebo kombinace těchto síťových služeb v Azure – privátní odkaz, ochrana DDoS, brána firewall, skupiny zabezpečení sítě, Firewall webových aplikací a koncové body Virtual Network.

|Služba|Proč použít?|Scénář|
|---|---|---|
|[Ochrana DDoS](#ddosprotection) |Vysoká dostupnost pro vaše aplikace s ochranou před nadměrnými poplatky za přenosy IP|[Správa Azure DDoS Protection](../virtual-network/manage-ddos-protection.md)|
|[Firewall webových aplikací](#waf)|<p>[Azure WAF s Application Gateway](../web-application-firewall/ag/ag-overview.md) poskytuje místní ochranu entit ve veřejných a privátních adresních prostorech.</p><p>[Azure WAF s předními dveřmi](../web-application-firewall/afds/afds-overview.md) zajišťuje ochranu v hraniční síti do veřejných koncových bodů.</p>|<p>[Konfigurace pravidel ochrany bot](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Konfigurace vlastního kódu odpovědi](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Konfigurovat pravidla omezení IP adres](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Konfigurovat pravidlo omezení četnosti](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure Firewall](#firewall)|Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností v cloudu.|<p>[Nasazení Azure Firewall ve virtuální síti](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[– Nasazení Azure Firewall v hybridní síti](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrování příchozího provozu pomocí Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Skupiny zabezpečení sítě](#nsg)|Úplný podrobný ovládací prvek distribuovaného uzlu na virtuálním počítači/podsíti pro všechny toky síťových přenosů|[Filtrování provozu sítě s využitím skupin zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)|
|[Koncové body služby pro virtuální síť](#serviceendpoints)|Umožňuje omezit síťový přístup k některým prostředkům služby Azure na podsíť virtuální sítě.|[Omezení síťového přístupu k prostředkům PaaS](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Privátní propojení](#privatelink)|Umožňuje přístup ke službám Azure PaaS (například Azure Storage a SQL Database) a službám, které hostuje zákaznická/Partnerská služba Azure, přes privátní koncový bod ve vaší virtuální síti.|<p>[Vytvoření privátního koncového bodu](../private-link/create-private-endpoint-portal.md)</p><p>[Vytvoření služby privátního propojení](../private-link/create-private-link-service-portal.md)</p>|
|||

### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) poskytuje protiopatření proti nejpropracovanějším hrozbám DDoS. Služba poskytuje rozšířené možnosti zmírnění DDoS pro vaši aplikaci a prostředky nasazené ve virtuálních sítích. Kromě toho zákazníci, kteří používají Azure DDoS Protection, mají přístup k DDoS podpoře rychlých odpovědí, aby při aktivním útoku zapojili odborníky na DDoS.

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="Azure ExpressRoute":::

### <a name="web-application-firewall"></a><a name="waf"></a>Firewall webových aplikací

[Firewall webových aplikací Azure](../web-application-firewall/overview.md) (WAF) poskytuje ochranu webových aplikací před běžnými webovými útoky a ohroženími zabezpečení, jako je například vkládání SQL a skriptování mezi weby. Azure WAF poskytuje předem chráněnou ochranu od OWASP hlavních chyb zabezpečení prostřednictvím spravovaných pravidel. Zákazníci navíc můžou nakonfigurovat také vlastní pravidla, která jsou zákaznická pravidla pro zajištění další ochrany založená na zdrojovém rozsahu IP adres, a požadavky na atributy, jako jsou hlavičky, soubory cookie, datová pole formuláře nebo parametry řetězce dotazu.

Zákazníci se můžou rozhodnout nasadit [Azure WAF s Application Gateway](../application-gateway/waf-overview.md) , která poskytuje regionální ochranu entit ve veřejných a privátních adresních prostorech. Zákazníci si také můžou nasadit [Azure WAF s předními dveřmi](../frontdoor/waf-overview.md) , které poskytují ochranu na hraničních sítích pro veřejné koncové body.

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Azure ExpressRoute":::

### <a name="azure-firewall"></a><a name="firewall"></a>Brána Azure Firewall
Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Pomocí Azure Firewall můžete centrálně vytvářet, vysazovat a protokolovat zásady pro připojení aplikací a sítí napříč předplatnými a virtuálními sítěmi. Brána Azure Firewall používá statickou veřejnou IP adresu pro prostředky virtuální sítě a díky tomu umožňuje venkovním bránám firewall identifikovat provoz pocházející z vaší virtuální sítě. 

Další informace o Azure Firewall najdete v dokumentaci k [Azure firewall](../firewall/overview.md).

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Azure ExpressRoute":::

### <a name="network-security-groups"></a><a name="nsg"></a>Skupiny zabezpečení sítě
Pomocí skupiny zabezpečení sítě můžete filtrovat síťový provoz do a z prostředků Azure ve virtuální síti Azure. Další informace najdete v tématu [Přehled zabezpečení](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Koncové body služby
Koncové body služby virtuální sítě rozšiřují privátní adresní prostor vaší virtuální sítě a její identitu do služeb Azure přes přímé připojení. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Provoz z vaší virtuální sítě do služby Azure vždy zůstává v páteřní síti Microsoft Azure. Další informace najdete v tématu [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="Azure ExpressRoute":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Privátní odkaz Azure](../private-link/private-link-overview.md) vám umožňuje přístup ke službám Azure PaaS (například Azure Storage a SQL Database) a službám, které hostuje zákaznická/Partnerská služba Azure, prostřednictvím privátního koncového bodu ve vaší virtuální síti.
Provoz mezi vaší virtuální sítí a službou přenáší páteřní síť Microsoftu. Vystavení vaší služby pro veřejný Internet již není nutné. Ve virtuální síti můžete vytvořit vlastní službu privátního propojení a předat ji zákazníkům.

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Azure ExpressRoute":::

## <a name="application-delivery-services"></a><a name="deliver"></a>Služby doručování aplikací

Tato část popisuje síťové služby v Azure, které vám pomůžou doručovat aplikace Content Delivery Network, službu Azure front-dveří, Traffic Manager, Load Balancer a Application Gateway.

|Služba|Proč použít?|Scénář|
|---|---|---|
|[Content Delivery Network](#cdn)|Poskytuje uživatelům obsah s vysokou šířkou pásma. Sítě CDN ukládat obsah uložený v mezipaměti na hraničních serverech, které jsou blízko koncovým uživatelům, aby se minimalizovala latence|<p>[Přidání CDN do webové aplikace](../cdn/cdn-add-to-web-app.md)</p> <p>[– Přístup k objektům blob úložiště pomocí Azure CDN vlastní domény přes HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Přidání vlastní domény do koncového bodu Azure CDN](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Konfigurace HTTPS pro vlastní doménu Azure CDN](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Umožňuje definovat, spravovat a monitorovat globální směrování pro webový provoz optimalizací pro nejlepší výkon a rychlé globální převzetí služeb při selhání pro zajištění vysoké dostupnosti.|<p>[Přidání vlastní domény do služby Azure Front Door Service](../frontdoor/front-door-custom-domain.md)</p> <p>[Konfigurace HTTPS pro vlastní doménu služby Front Door](../frontdoor/front-door-custom-domain-https.md)</p><p>[Nastavení zásad brány firewall webových aplikací geografického filtrování](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|Distribuuje provoz na základě DNS do služeb napříč globálními oblastmi Azure a zajišťuje vysokou dostupnost a rychlost odezvy.|<p> [Směrování provozu pro zajištění nízké latence](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Směrování provozu do prioritního koncového bodu](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Řízení provozu s váženými koncovými body](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Směrování provozu na základě geografického umístění koncového bodu](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Směrování provozu na základě podsítě uživatele](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Load Balancer](#loadbalancer)|Zajišťuje regionální vyrovnávání zatížení díky směrování provozu napříč zónami dostupnosti a do virtuální sítě. Poskytuje interní vyrovnávání zatížení díky směrování provozu mezi prostředky a mezi nimi a k sestavení vaší místní aplikace.|<p> [Vyrovnávání zatížení internetového provozu do virtuálních počítačů](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Vyrovnávání zatížení napříč virtuálními počítači uvnitř virtuální sítě](../load-balancer/tutorial-load-balancer-standard-internal-portal.md)<p>[Přeposílání provozu na konkrétní port na konkrétní virtuální počítače](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Konfigurace vyrovnávání zatížení a odchozích pravidel](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Azure Application Gateway je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací.|<p>[Přímý webový provoz s využitím služby Azure Application Gateway](../application-gateway/quick-create-portal.md)</p><p>[Kurz: Konfigurace aplikační brány s ukončením TLS pomocí Azure Portal](../application-gateway/create-ssl-portal.md)</p><p>[Vytvoření aplikační brány se směrováním založeným na cestě URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Azure Content Delivery Network (CDN) nabízí vývojářům globální řešení pro rychlé doručování širokopásmového obsahu uživatelům díky ukládání obsahu do mezipaměti na fyzických uzlech strategicky umístěných po celém světě. Další informace o Azure CDN najdete v tématu [Azure Content Delivery Network](../cdn/cdn-overview.md).

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure ExpressRoute":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Služba front-dveří Azure
Služba Azure Front Door vám umožňuje definovat, spravovat a monitorovat globální směrování webového provozu tím, že provádí optimalizaci pro nejlepší výkon a poskytuje okamžité globální převzetí služeb při selhání, aby byla zajištěna vysoká dostupnost. Se službou Front Door můžete transformovat svoje globální (zahrnující více oblastí) spotřebitelské a podnikové aplikace do robustních a vysoce výkonných přizpůsobených moderních aplikací, rozhraní API a obsahu, kterými s pomocí Azure oslovíte globální cílovou skupinu. Další informace najdete v tématu [přední dvířka Azure](../frontdoor/front-door-overview.md).

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager je nástroj pro vyrovnávání zatížení provozu na základě DNS, který umožňuje optimálně distribuovat provoz do služeb napříč globálními oblastmi Azure při zajištění vysoké dostupnosti a rychlosti odezvy. Traffic Manager poskytuje řadu metod směrování provozu pro distribuci provozu, jako je priorita, vážená, výkonná, geografická, více hodnot nebo podsíť. Další informace o metodách směrování provozu najdete v tématu [Traffic Manager metod směrování](../traffic-manager/traffic-manager-routing-methods.md).

Následující diagram znázorňuje směrování na základě priority koncového bodu s Traffic Manager:

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Azure ExpressRoute":::

Další informace o Traffic Manager najdete v tématu [co je Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Azure Load Balancer poskytuje vysoce výkonné a nízké latence vyrovnávání zatížení vrstvy 4 pro všechny protokoly UDP a TCP. Spravuje příchozí a odchozí připojení. Můžete nakonfigurovat veřejné a interní koncové body s vyrovnáváním zatížení. Můžete definovat pravidla pro mapování příchozích připojení k cílům back-end fondu pomocí možností nástroje pro zjišťování stavu TCP a HTTP pro správu dostupnosti služby. Další informace o Load Balancer najdete v článku [Load Balancer přehled](../load-balancer/load-balancer-overview.md) .

Následující obrázek znázorňuje internetovou vícevrstvou aplikaci, která využívá externí i interní nástroje pro vyrovnávání zatížení:

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Azure ExpressRoute":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací. Je to řadič pro doručování aplikací (ADC) jako služba a nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaše aplikace. Další informace najdete v tématu [co je Azure Application Gateway?](../application-gateway/overview.md).

Následující diagram znázorňuje směrování na základě cesty URL s Application Gateway.

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Azure ExpressRoute":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>Služby monitorování sítě
Tato část popisuje síťové služby v Azure, které vám pomůžou monitorovat síťové prostředky – Network Watcher, monitor ExpressRoute, Azure Monitor a Virtual Network klepněte na.

|Služba|Proč použít?|Scénář|
|---|---|---|
|[Network Watcher](#networkwatcher)|Pomáhá monitorovat a řešit potíže s připojením, pomáhá diagnostikovat problémy VPN, NSG a směrování, zachytávání paketů na vašem VIRTUÁLNÍm počítači automatizuje aktivaci diagnostických nástrojů pomocí Azure Functions a Logic Apps|<p>[Diagnostika potíží s filtrováním provozu virtuálních počítačů](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnostika potíží se směrováním virtuálních počítačů](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Monitorování komunikace mezi virtuálními počítači](../network-watcher/connection-monitor.md)</p><p>[Diagnostika potíží s komunikací mezi sítěmi](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Protokolování síťového provozu do a z virtuálního počítače](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[Monitorování ExpressRoute](#expressroutemonitor)|Poskytuje sledování výkonu, dostupnosti a využití sítě v reálném čase, pomáhá s automatickým zjišťováním síťové topologie, zajišťuje rychlejší izolaci chyb, detekuje dočasné problémy se sítí, pomáhá analyzovat historické charakteristiky výkonu sítě, podporuje více předplatných.|<p>[Konfigurace Network Performance Monitoru pro ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Monitorování, metriky a výstrahy služby ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Pomůže vám pochopit, jak vaše aplikace provádí a aktivně identifikuje problémy, které se jim ovlivňují, a prostředky, na kterých jsou závislé.|<p>[Traffic Manager metriky a výstrahy](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Diagnostika Azure monitor pro Standard Load Balancer](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Monitorování protokolů a metrik Azure Firewallu](../firewall/tutorial-diagnostics.md)</p><p>[Monitorování a protokolování Firewallu webových aplikací Azure](../frontdoor/waf-front-door-monitor.md)</p>|
|[Virtual Network klepněte na](#vnettap)|Poskytuje průběžné streamování provozu sítě virtuálních počítačů do sběrače paketů, umožňuje řešení pro správu výkonu sítě a aplikace a nástroje pro analýzu zabezpečení.|[Vytvoření virtuální sítě klepnutím na prostředek](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher poskytuje nástroje pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve virtuální síti Azure. Další informace najdete v tématu [co je Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Monitorování ExpressRoute
Další informace o tom, jak zobrazit metriky okruhů ExpressRoute, protokoly prostředků a výstrahy, najdete v tématu [monitorování ExpressRoute, metriky a výstrahy](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Azure Monitor maximalizuje dostupnost a výkon vašich aplikací tím, že poskytuje ucelené řešení pro shromažďování, analýzu a telemetrii z vašich cloudových a místních prostředí. Pomůže vám při zjišťování stavu vašich aplikací a proaktivně identifikuje problémy, které je ovlivňují, a prostředky, na kterých jsou závislé. Další informace najdete v tématu [přehled Azure monitor](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Virtual Network klepněte na
KLEPNUTÍ na virtuální síť Azure (terminálový přístupový bod) umožňuje nepřetržitě streamovat síťový provoz virtuálního počítače do nástroje pro shromažďování síťových paketů nebo pro analýzu. Sběrač nebo nástroj pro analýzu poskytuje síťový partner pro [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/) . 

Následující obrázek ukazuje, jak funguje virtuální síť. 

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="Azure ExpressRoute":::

Další informace najdete v tématu [co je Virtual Network klepněte na](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Další kroky

- Vytvořte svou první virtuální síť a připojte k ní několik virtuálních počítačů, a to provedením kroků v článku [Vytvoření první virtuální sítě](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .
- Připojte počítač k virtuální síti provedením kroků v [článku Konfigurace připojení typu Point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Vyrovnávání zatížení internetového provozu na veřejné servery provedením kroků v článku [vytvoření internetového nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .
