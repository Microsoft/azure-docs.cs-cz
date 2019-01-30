---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 98ea4d78a473123708be6e371587252acad6ffcd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205090"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Jaký je rozdíl mezi bránou virtuální sítě Azure (VPN Gateway) a Azure Virtual WAN vpngateway?

Virtual WAN poskytuje možnosti připojení typu Site-to-Site ve velkém měřítku a je určená pro zajištění propustnosti, škálovatelnosti a snadného použití. Funkce připojení ExpressRoute a Point-to-Site jsou v současnosti ve verzi Preview. Pobočková zařízení CPE automaticky zřizují službu Azure Virtual WAN a připojují se k ní. Tato zařízení jsou dostupná od stále rostoucího ekosystému partnerů pro SD-WAN a VPN. Viz [Seznam upřednostňovaných partnerů](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Kteří poskytovatelé zařízení (partneři pro Virtual WAN) se podporují při uvedení na trh? 

Plně automatizované prostředí Virtual WAN v současnosti podporuje celá řada partnerů. Další informace najdete v tématu [Partneři Virtual WAN](https://go.microsoft.com/fwlink/p/?linkid=2019615). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Jaké jsou kroky automatizace Virtual WAN u partnerů?

Informace o krocích automatizace u partnerů najdete v tématu o [automatizaci Virtual WAN u partnerů](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Musím použít preferované partnerské zařízení?

Ne. Můžete použít libovolné zařízení s podporou VPN, které vyhovuje požadavkům Azure na podporu IKEv2/IKEv1 protokolu IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partneři pro Virtual WAN automatizují možnosti připojení s využitím Azure Virtual WAN?

Softwarově definovaná řešení možností připojení obvykle spravují svá pobočková zařízení s využitím kontroleru nebo centra pro zřizování zařízení. Kontroler může k automatizaci připojení k Azure Virtual WAN využívat rozhraní API Azure. Další informace najdete v tématu o automatizaci Virtual WAN u partnerů.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Mění Virtual WAN stávající funkce připojení?   

Stávající funkce připojení Azure se nemění.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké nové prostředky Resource Manageru?
  
Ano, Virtual WAN zavádí nové prostředky Resource Manageru. Další informace najdete v [přehledu](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Kolik zařízení VPN se může připojit k jednomu rozbočovači?

Podporuje se až 1000 připojení na jeden virtuální rozbočovač. Každé připojení se skládá ze dvou tunelů, které jsou v konfiguraci aktivní–aktivní. Tyto tunely končí v Azure Virtual Hub vpngateway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Může se místní zařízení VPN připojit k více rozbočovačům?

Ano. Počáteční komunikace by probíhala z místního zařízení na nejbližší hraniční zařízení Microsoft a potom do virtuálního rozbočovače.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Podporuje se pro Azure Virtual WAN globální VNet Peering? 

 Ne.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Mohou paprskové virtuální sítě připojené k virtuálnímu rozbočovači vzájemně komunikovat?

Ano. Paprsků virtuální sítě může komunikovat přímo přes partnerské vztahy virtuálních sítí. Virtuální sítě přechodně komunikaci přes Centrum však nepodporujeme. Další informace najdete v tématu [VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Můžu při použití Azure Virtual WAN nasadit a použít moje oblíbené síťové virtuální zařízení (ve virtuální síti NVA)?

Ano, virtuální síť svého oblíbeného virtuálního síťového zařízení (NVA) můžete připojit k síti Azure Virtual WAN. Virtuální síť virtuálního síťového zařízení nejprve připojte k rozbočovači pomocí připojení k centrální virtuální síti. Pak vytvořte trasu virtuálního rozbočovače, jejíž další segment směrování bude odkazovat na virtuální zařízení. V tabulce směrování virtuálního rozbočovače můžete použít více tras. Všechny paprsky připojené k virtuální síti virtuálního síťového zařízení musí být navíc připojené k virtuálnímu rozbočovači kvůli zajištění šíření tras hvězdicové virtuální sítě do místních systémů.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Může virtuální síť NVA mít bránu virtuální sítě?

Ne. Virtuální síť připojená k virtuálnímu rozbočovači nemůže mít bránu virtuální sítě. 

### <a name="is-there-support-for-bgp"></a>Podporuje se BGP?

Ano, BGP se podporuje. Aby bylo zajištěno, že trasy z virtuální sítě virtuálního síťového zařízení budou odpovídajícím způsobem inzerovány, musí paprsky zakázat BGP, pokud jsou připojené k virtuální síti virtuálního síťového zařízení, která je pak připojená k virtuálnímu rozbočovači. Hvězdicové virtuální sítě dále připojte k virtuálnímu rozbočovači, abyste zajistili, že trasy hvězdicových virtuálních budou šířeny do místních systémů.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Dá se ve virtuálním rozbočovači směrovat provoz s využitím UDR?

Ano, provoz do virtuální sítě můžete směrovat pomocí tabulky směrování virtuálního rozbočovače.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké informace o cenách nebo licencích?
 
Ano. Prohlédněte si stránku s [cenami](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Jak se onboardují noví partneři, kteří nejsou uvedení ve vašem seznamu partnerů pro spouštění?

Odešlete email na adresu azurevirtualwan@microsoft.com. Ideální partner je takový, pro jehož zařízení se dá zřídit připojení IKEv1 nebo IKEv2 protokolu IPSec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Je možné vytvořit Azure Virtual WAN s využitím šablony Resource Manageru?

Jednoduchou konfiguraci jedné sítě Virtual WAN s jedním rozbočovačem a jedním vpnsite můžete vytvořit pomocí [šablony pro rychlé zprovoznění Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtual WAN je služba, která je primárně založená na rozhraní REST nebo na portálu.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Jsou ve Virtual WAN povolené možnosti připojení mezi jednotlivými pobočkami?

Ano, možnosti připojení mezi jednotlivými pobočkami jsou dostupné v síti Virtual WAN u připojení VPN a u připojení VPN na ExpressRoute. Připojení VPN typu Site-to-Site je obecně dostupné, připojení ExpressRoute a Point-to-Site jsou momentálně ve verzi Preview.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Prochází přenos mezi pobočkami přes Azure Virtual WAN?

Ano.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Jak se Virtual WAN liší od existující brány virtuální sítě Azure?

Síť VPN brány virtuální sítě je omezená na 30 tunelů. Pro připojení byste měli pro rozsáhlé sítě VPN používat Virtual WAN. Ve všech oblastech kromě oblasti USA – středozápad můžete v centru připojit až 1000 připojení poboček s rychlostí 2 GB/s. Pro oblast USA – středozápad je dostupná rychlost 20 GB/s. V budoucnu budeme postupně zavádět rychlost 20 GB/s i do dalších oblastí. Připojení je tunel typu aktivní-aktivní z místního zařízení VPN do virtuálního rozbočovače. V každé oblasti můžete mít jeden rozbočovač, což znamená, že můžete připojit více než 1000 poboček mezi různými rozbočovači.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Vyžaduje tato služba Virtual WAN ExpressRoute z každé lokality?

Ne, Virtual WAN nevyžaduje ExpressRoute z každé lokality. Využívá standardní připojení IPsec typu Site-to-Site prostřednictvím internetových linek ze zařízení do rozbočovače Azure Virtual WAN. Vaše lokality můžou být připojené k síti poskytovatele prostřednictvím okruhu ExpressRoute. V případě lokalit, které jsou připojené pomocí okruhu ExpressRoute ve virtuálním rozbočovači (ve verzi Preview), je možné využívat provoz pobočka-pobočka mezi VPN a ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Platí při použití Azure Virtual WAN nějaké omezení propustnosti sítě?

Počet poboček je omezený na 1000 připojení na rozbočovač/oblast a celkem 2 Gb/s v rozbočovači. Výjimkou je oblast USA – středozápad, která nabízí celkem 20 GB/s. Rychlost 20 GB/s budeme postupně zavádět i do jiných oblastí.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Umožňuje Virtual WAN místním zařízením využívat paralelně několik poskytovatelů internetových služeb, nebo jde vždycky o jeden tunel VPN?

Ano, v závislosti na zařízení pobočky můžete mít z jedné pobočky tunely typu aktivní-aktivní (2 tunely = 1 připojení Azure Virtual WAN).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Jak se směruje provoz v páteřní síti Azure?

Přenos probíhá takto: zařízení pobočky -> poskytovatel internetu -> Microsoft Edge -> datové centrum Microsoftu -> Microsoft Edge -> poskytovatel internetu -> zařízení pobočky.

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Co je při použití tohoto modelu potřeba na jednotlivých lokalitách? Jenom připojení k internetu?

Ano. Připojení k internetu a fyzické zařízení, ideálně od některého z našich integrovaných [partnerů](https://go.microsoft.com/fwlink/p/?linkid=2019615). Volitelně můžete konfiguraci a připojení k Azure z vašeho preferovaného zařízení spravovat ručně.
