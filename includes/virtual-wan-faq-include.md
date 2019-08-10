---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 08/06/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8a4bbe92cc1b34801abffa1e905d873d4382025a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912333"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Jaký je rozdíl mezi bránou virtuální sítě Azure (VPN Gateway) a Azure Virtual WAN vpngateway?

Virtual WAN poskytuje možnosti připojení typu Site-to-Site ve velkém měřítku a je určená pro zajištění propustnosti, škálovatelnosti a snadného použití. ExpressRoute pro připojení k virtuální síti WAN je v tuto chvíli ve verzi Preview. Zařízení větve CPE pro připojení ke službě Azure Virtual WAN Tato zařízení jsou dostupná od stále rostoucího ekosystému partnerů pro SD-WAN a VPN. Podívejte se na [seznam upřednostňovaných partnerů](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Co je připojení ke větvi Azure Virtual WAN?

Připojení ze zařízení z pobočky do Azure Virtual WAN, skládající se ze dvou tunelů aktivní/aktivní IPsec.

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

Pro jedno virtuální centrum se podporuje až 1 000 připojení. Každé připojení se skládá ze dvou tunelů, které jsou v konfiguraci aktivní–aktivní. Tyto tunely končí v Azure Virtual Hub vpngateway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Může se místní zařízení VPN připojit k více rozbočovačům?

Ano. Počáteční komunikace by probíhala z místního zařízení na nejbližší hraniční zařízení Microsoft a potom do virtuálního rozbočovače.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Podporuje se pro Azure Virtual WAN globální VNet Peering? 

 Ne.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Mohou paprskové virtuální sítě připojené k virtuálnímu rozbočovači vzájemně komunikovat?

Ano. Paprskový virtuální sítě může komunikovat přímo prostřednictvím partnerského vztahu Virtual Network. Virtuální sítě komunikaci ale nepodporujeme prostřednictvím centra. Další informace najdete v tématu [VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Můžu při použití Azure Virtual WAN nasadit a použít moje oblíbené síťové virtuální zařízení (ve virtuální síti NVA)?

Ano, virtuální síť svého oblíbeného virtuálního síťového zařízení (NVA) můžete připojit k síti Azure Virtual WAN. Virtuální síť virtuálního síťového zařízení nejprve připojte k rozbočovači pomocí připojení k centrální virtuální síti. Pak vytvořte trasu virtuálního rozbočovače, jejíž další segment směrování bude odkazovat na virtuální zařízení. V tabulce směrování virtuálního rozbočovače můžete použít více tras. Všechny paprsky připojené k virtuální síti virtuálního síťového zařízení musí být navíc připojené k virtuálnímu rozbočovači kvůli zajištění šíření tras hvězdicové virtuální sítě do místních systémů.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Může virtuální síť NVA mít bránu virtuální sítě?

Ne. Virtuální síť připojená k virtuálnímu rozbočovači nemůže mít bránu virtuální sítě. 

### <a name="is-there-support-for-bgp"></a>Podporuje se BGP?

Ano, BGP se podporuje. Při vytváření sítě VPN můžete zadat parametry protokolu BGP. To znamená, že pro protokol BGP bude povolená jakákoli připojení vytvořená v Azure pro tuto lokalitu. Pokud jste navíc používali virtuální síť s síťové virtuální zařízení a pokud byla tato virtuální síť síťové virtuální zařízení připojená k virtuálnímu rozbočovači WAN, aby bylo zajištěno, že se trasy z virtuální sítě síťové virtuální zařízení inzerují vhodně, paprsky připojené ke síťové virtuální zařízení virtuální síti musí protokol BGP zakázat. Kromě toho připojte tyto paprsky virtuální sítě k virtuální síti virtuálního centra a zajistěte, aby byly trasy sítě rozbočovače šířeny do místních systémů.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Dá se ve virtuálním rozbočovači směrovat provoz s využitím UDR?

Ano, provoz do virtuální sítě můžete směrovat pomocí tabulky směrování virtuálního rozbočovače.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké informace o cenách nebo licencích?
 
Ano. Prohlédněte si stránku s [cenami](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Návody vypočítat cenu centra?
 
Za službu platíte v centru. Například 10 větví nebo místních zařízení vyžadujících připojení k Azure Virtual WAN by znamenaly připojení k koncovým bodům VPN v centru. To znamená, že se jedná o síť VPN s 1 jednotkou škálování = 500 MB/s, účtuje se za $0.361/hod. Každé připojení se účtuje za $0,08/hod. U 10 připojení by celková cena za službu/HR byla $0,361 + $. 8/ oddělení. Poplatky za přenosy dat, které opouští Azure, se použijí. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Jak se onboardují noví partneři, kteří nejsou uvedení ve vašem seznamu partnerů pro spouštění?

Odešlete email na adresu azurevirtualwan@microsoft.com. Ideální partner je takový, pro jehož zařízení se dá zřídit připojení IKEv1 nebo IKEv2 protokolu IPSec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Co když zařízení, které používám, není v seznamu partnerů virtuální sítě WAN? Můžu ho I nadále používat pro připojení k síti VPN Azure Virtual WAN?

Ano, pokud zařízení podporuje protokol IPsec IKEv1 nebo IKEv2. Virtuální partneři sítě WAN automatizují připojení ze zařízení k koncovým bodům VPN Azure. To zahrnuje automatizaci kroků, jako je například nahrání informací o větvích, protokol IPsec a konfigurace a připojení. Vzhledem k tomu, že vaše zařízení nepochází z virtuálního partnerského serveru pro partnery WAN, budete muset provést těžkou zvedání ručního nastavování konfigurace Azure a aktualizace vašeho zařízení, aby se nastavilo připojení pomocí protokolu IPsec. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Je možné vytvořit Azure Virtual WAN s využitím šablony Resource Manageru?

Jednoduchá konfigurace jedné virtuální sítě WAN s jedním rozbočovačem a jedním vpnsite se dá vytvořit pomocí [šablony Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtual WAN je služba, která je primárně založená na rozhraní REST nebo na portálu.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Jsou ve Virtual WAN povolené možnosti připojení mezi jednotlivými pobočkami?

Ano, možnosti připojení mezi jednotlivými pobočkami jsou dostupné v síti Virtual WAN u připojení VPN a u připojení VPN na ExpressRoute. Síť VPN typu Site-to-site je GA, ale ExpressRoute je aktuálně ve verzi Preview.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Prochází provoz mezi větví přes Azure Virtual WAN?

Ano.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Jak se Virtual WAN liší od existující brány virtuální sítě Azure?

Síť VPN brány virtuální sítě je omezená na 30 tunelů. Pro připojení byste měli pro rozsáhlé sítě VPN používat Virtual WAN. V centru pro všechny oblasti můžete připojit až 1 000 připojení větví s 20 GB/s. Připojení je tunel typu aktivní-aktivní z místního zařízení VPN do virtuálního rozbočovače. Můžete mít jeden rozbočovač na oblast, což znamená, že můžete připojit více než 1 000 větví napříč rozbočovači.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Jak virtuální síť WAN podporuje zařízení SD-WAN?

Virtuální partneři sítě WAN automatizují připojení IPsec k koncovým bodům VPN Azure. Pokud je adaptér virtuální sítě WAN poskytovatelem SD-WAN, předpokládá se, že řadič SD-WAN spravuje automatizaci a připojení IPsec k koncovým bodům VPN Azure. Pokud zařízení SD-WAN vyžaduje svůj vlastní koncový bod místo Azure VPN pro jakékoli proprietární funkce SD-WAN, můžete koncový bod SD-WAN ve virtuální síti Azure nasadit a koexistovat s Azure Virtual WAN.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Vyžaduje tato služba Virtual WAN ExpressRoute z každé lokality?

Ne, Virtual WAN nevyžaduje ExpressRoute z každé lokality. Využívá standardní připojení IPsec typu Site-to-Site prostřednictvím internetových linek ze zařízení do rozbočovače Azure Virtual WAN. Vaše lokality můžou být připojené k síti poskytovatele prostřednictvím okruhu ExpressRoute. V případě lokalit, které jsou připojené pomocí okruhu ExpressRoute ve virtuálním rozbočovači (ve verzi Preview), je možné využívat provoz pobočka-pobočka mezi VPN a ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Platí při použití Azure Virtual WAN nějaké omezení propustnosti sítě?

Počet větví je omezený na 1000 připojení na střed a oblast a celkem 20 GB/s v centru.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Na portálu se nezobrazuje nastavení 20 GB pro virtuální rozbočovač. Návody nakonfigurovat?

V současné době můžete nakonfigurovat jednotky škálování brány na 20 GB/s pomocí rutiny [Update-AzVpnGateway](https://docs.microsoft.com/powershell/module/az.network/update-azvpngateway) . Toto nastavení je v plánu, který bude dostupný na portálu.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Kolik připojení k síti VPN podporuje virtuální centrum WAN?

Rozbočovač Azure Virtual WAN může podporovat až 1 000 připojení S2S a 10 000 připojení P2S současně.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Jaká je celková propustnost sítě VPN tunelu VPN a připojení?

Celková propustnost sítě VPN rozbočovače je až 20 GB/s na základě zvolené jednotky škálování. Propustnost sdílí všechna existující připojení.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Umožňuje Virtual WAN místním zařízením využívat paralelně několik poskytovatelů internetových služeb, nebo jde vždycky o jeden tunel VPN?

Připojení přicházející do virtuální sítě WAN VPN je vždy aktivní aktivní tunelové propojení (kvůli odolnosti v rámci stejného rozbočovače nebo oblasti) pomocí odkazu, který je k dispozici ve větvi. Tento odkaz může být odkazem poskytovatele internetových služeb na místní větvi. Virtual WAN neposkytuje žádnou speciální logiku pro paralelní nastavení více poskytovatelů internetových služeb. Správa převzetí služeb při selhání přes poskytovatele internetových služeb ve větvi je zcela síťová operace zaměřené na větve. K výběru cest ve větvi můžete použít své oblíbené řešení SD-WAN.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Jak se směruje provoz v páteřní síti Azure?

Provoz se řídí vzorem: > zařízení – poskytovatel internetových služeb > Microsoft Edge – > Microsoft Edge (virtuální síť centra) – > zařízení Microsoft Edge – > ISP – > pobočky

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Co je při použití tohoto modelu potřeba na jednotlivých lokalitách? Jenom připojení k internetu?

Ano. Připojení k Internetu a fyzické zařízení, které podporuje protokol IPsec, nejlépe z našich integrovaných [partnerů](https://go.microsoft.com/fwlink/p/?linkid=2019615). Volitelně můžete konfiguraci a připojení k Azure z vašeho preferovaného zařízení spravovat ručně.
