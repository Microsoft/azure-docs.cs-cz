---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/18/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3fa261c6cb76cb95cffc602e5018e480afbb5dae
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58890903"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Jaký je rozdíl mezi bránou virtuální sítě Azure (VPN Gateway) a Azure Virtual WAN vpngateway?

Virtual WAN poskytuje možnosti připojení typu Site-to-Site ve velkém měřítku a je určená pro zajištění propustnosti, škálovatelnosti a snadného použití. Funkce připojení ExpressRoute a Point-to-Site jsou v současnosti ve verzi Preview. CPE větev autoprovision zařízení a připojení v Azure virtuální sítě WAN. Tato zařízení jsou dostupná od stále rostoucího ekosystému partnerů pro SD-WAN a VPN. Zobrazit [Upřednostňovaný seznam partnerů](https://go.microsoft.com/fwlink/p/?linkid=2019615).

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

Ano, BGP se podporuje. Při vytváření serveru VPN, můžete zadat parametry protokolu BGP v ní. To bude znamenat, že budou všechna připojení vytvořené v Azure pro tuto lokalitu povolené pro protokol BGP. Kromě toho pokud máte virtuální síť, která síťové virtuální zařízení a této virtuální síti VNet síťové virtuální zařízení byla připojena k rozbočovači virtuální sítě WAN, aby se zajistilo, že jsou odpovídajícím způsobem, Inzerovat trasy z virtuální sítě síťové virtuální zařízení paprsků, které jsou připojené k virtuální síti síťového virtuálního zařízení nutné zakázat protokol BGP. Navíc tyto paprsku, který se rozšíří virtuální sítě k virtuální rozbočovač typu VNet-to-Ujistěte se trasy virtuální sítě paprsků připojení k místním systémům.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Dá se ve virtuálním rozbočovači směrovat provoz s využitím UDR?

Ano, provoz do virtuální sítě můžete směrovat pomocí tabulky směrování virtuálního rozbočovače.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké informace o cenách nebo licencích?
 
Ano. Prohlédněte si stránku s [cenami](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Výpočet ceny rozbočovače
 
Zaplatíte služby v centru. Například 10 větve nebo na místním zařízení, která vyžadují pro připojení k Azure virtuální sítě WAN by vyžadovalo připojení k síti VPN koncové body v centru. Umožňuje to je VPN jednotky škálování 1 = 500 MB/s, to se účtuje 0.361 $/ hod. Každé připojení se účtuje 0,08 USD/hod. Pro 10 připojení, celková cena služby/hod by 0.361 $ + $. 8 / hod. Data pro provoz opouští azure účtují poplatky. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Jak se onboardují noví partneři, kteří nejsou uvedení ve vašem seznamu partnerů pro spouštění?

Odešlete email na adresu azurevirtualwan@microsoft.com. Ideální partner je takový, pro jehož zařízení se dá zřídit připojení IKEv1 nebo IKEv2 protokolu IPSec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Co když používám zařízení i se nenachází v seznamu partnerské virtuální sítě WAN? Můžu ji mohou dál používat pro připojení k virtuální síti WAN VPN Azure?

Ano, tak dlouho, dokud zařízení podporuje protokol IPsec IKEv1 nebo IKEv2. Virtuální sítě WAN partneři automatizovat připojení ze zařízení do koncových bodů Azure VPN. To znamená automatizuje kroky, jako je například "větev informace nahrávání", "IPsec a konfiguraci" a "připojení". Protože zařízení není z virtuální sítě WAN ekosystém partnerů, je potřeba udělat rutinní ručně trvá konfigurace Azure a aktualizaci vašich zařízení k nastavení připojení protokolu IPsec. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Je možné vytvořit Azure Virtual WAN s využitím šablony Resource Manageru?

Jednoduchá konfigurace jedné virtuální sítě WAN s jednom centru a jeden vpnsite lze vytvořit pomocí [šablona Azure quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtual WAN je služba, která je primárně založená na rozhraní REST nebo na portálu.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Jsou ve Virtual WAN povolené možnosti připojení mezi jednotlivými pobočkami?

Ano, možnosti připojení mezi jednotlivými pobočkami jsou dostupné v síti Virtual WAN u připojení VPN a u připojení VPN na ExpressRoute. Připojení VPN typu Site-to-Site je obecně dostupné, připojení ExpressRoute a Point-to-Site jsou momentálně ve verzi Preview.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Prochází přenos mezi pobočkami přes Azure Virtual WAN?

Ano.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Jak se Virtual WAN liší od existující brány virtuální sítě Azure?

Síť VPN brány virtuální sítě je omezená na 30 tunelů. Pro připojení byste měli pro rozsáhlé sítě VPN používat Virtual WAN. Ve všech oblastech kromě oblasti USA – středozápad můžete v centru připojit až 1000 připojení poboček s rychlostí 2 GB/s. Pro oblast USA – středozápad je dostupná rychlost 20 GB/s. V budoucnu budeme postupně zavádět rychlost 20 GB/s i do dalších oblastí. Připojení je tunel typu aktivní-aktivní z místního zařízení VPN do virtuálního rozbočovače. V každé oblasti můžete mít jeden rozbočovač, což znamená, že můžete připojit více než 1000 poboček mezi různými rozbočovači.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Jak se virtuální sítě WAN podporu SD-WAN zařízení?

Virtuální sítě WAN partneři automatizovat připojení protokolu IPsec pro koncové body Azure VPN. Pokud virtuální sítě WAN partner poskytovatele SD-WAN, pak se předpokládá, že SD-WAN kontrolér spravuje automatizace a možnosti připojení protokolu IPsec pro koncové body Azure VPN. Pokud zařízení SD-WAN vyžaduje svou vlastní koncový bod místo Azure VPN pro všechny speciální funkce SD-WAN, můžete nasadit SD-WAN koncový bod ve virtuální síti Azure a existovat současně s Azure virtuální sítě WAN.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Vyžaduje tato služba Virtual WAN ExpressRoute z každé lokality?

Ne, Virtual WAN nevyžaduje ExpressRoute z každé lokality. Využívá standardní připojení IPsec typu Site-to-Site prostřednictvím internetových linek ze zařízení do rozbočovače Azure Virtual WAN. Vaše lokality můžou být připojené k síti poskytovatele prostřednictvím okruhu ExpressRoute. V případě lokalit, které jsou připojené pomocí okruhu ExpressRoute ve virtuálním rozbočovači (ve verzi Preview), je možné využívat provoz pobočka-pobočka mezi VPN a ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Platí při použití Azure Virtual WAN nějaké omezení propustnosti sítě?

Počet poboček je omezený na 1000 připojení na rozbočovač/oblast a celkem 2 Gb/s v rozbočovači. Výjimkou je oblast USA – středozápad, která nabízí celkem 20 GB/s. Rychlost 20 GB/s budeme postupně zavádět i do jiných oblastí.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Umožňuje Virtual WAN místním zařízením využívat paralelně několik poskytovatelů internetových služeb, nebo jde vždycky o jeden tunel VPN?

Připojení přicházející do virtuální sítě WAN VPN vždy tunelové propojení typu aktivní aktivní (pro odolnost proti chybám ve stejné oblasti/rozbočovač) používá k dispozici odkaz na větev. Tento odkaz může být odkaz na poskytovatele internetových služeb ve větvi v místním prostředí. Virtuální síť WAN neposkytuje nějakou zvláštní logiku k nastavení více poskytovatele internetových služeb paralelně; správu převzetí služeb při selhání mezi poskytovatele internetových služeb ve větvi je zcela zaměřené na větev síťové operace. Provedete to výběr cesty na větev můžete použít oblíbený SD-WAN řešení.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Jak se směruje provoz v páteřní síti Azure?

Provoz má následující formát: větev zařízení -> poskytovatele internetových služeb -> Microsoft edge -> Microsoft řadič domény (virtuální síti centra) -> Microsoft -> edge poskytovatele internetových služeb -> větev zařízení

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Co je při použití tohoto modelu potřeba na jednotlivých lokalitách? Jenom připojení k internetu?

Ano. Připojení k Internetu a fyzickým zařízením, která podporuje protokol IPsec, nejlépe z našich integrovaných [partneři](https://go.microsoft.com/fwlink/p/?linkid=2019615). Volitelně můžete konfiguraci a připojení k Azure z vašeho preferovaného zařízení spravovat ručně.