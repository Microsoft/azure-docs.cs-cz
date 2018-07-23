---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/18/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d059cab5668eef8d4dafc1442ca9749a7dcf8c9d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162510"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Jaký je rozdíl mezi bránou virtuální sítě Azure (VPN Gateway) a Azure Virtual WAN vpngateway?

Služba Virtual WAN poskytuje možnosti připojení typu Site-to-Site ve velkém měřítku a je určená pro zajištění propustnosti, škálovatelnosti a snadného použití. Pobočková zařízení CPE automaticky zřizují službu Azure Virtual WAN a připojují se k ní. Tato zařízení jsou dostupná od stále rostoucího ekosystému partnerů pro SD-WAN a VPN.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Kteří poskytovatelé zařízení (partneři pro Virtual WAN) se podporují při uvedení na trh? 

Plně automatizované prostředí Virtual WAN v současnosti podporují Citrix a Riverbed. Další informace najdete v tématu [Partneři Virtual WAN](https://aka.ms/virtualwan).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Musím použít preferované partnerské zařízení?

Ne. Můžete použít libovolné zařízení s podporou VPN, které vyhovuje požadavkům verze Preview na podporu IKEv2 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partneři pro Virtual WAN automatizují možnosti připojení s využitím Azure Virtual WAN?

Softwarově definovaná řešení možností připojení obvykle spravují svá pobočková zařízení s využitím kontroleru nebo centra pro zřizování zařízení. Kontroler může k automatizaci připojení k Azure Virtual WAN využívat rozhraní API Azure. Další informace najdete v tématu věnovaném [automatizaci partnerů pro Virtual WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Mění Virtual WAN stávající funkce připojení?   

Stávající funkce připojení Azure se nemění.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké nové prostředky Resource Manageru?
  
Ano, Virtual WAN zavádí nové prostředky Resource Manageru. Další informace najdete v [přehledu](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Platí pro zapojení do verze Preview nějaké speciální požadavky? 

Abyste mohli nakonfigurovat Azure Virtual WAN, musíte nejdřív zaregistrovat vaše předplatné ve verzi Preview. Pokud se chcete zaregistrovat, pošlete e-mail s ID předplatného na adresu <azurevirtualwan@microsoft.com>. Jakmile se vaše předplatné zaregistruje, dostanete e-mail s potvrzením. Dokud tento e-mail s potvrzením o registraci vašeho předplatného nedostanete, nebudete moct se službou Virtual WAN na portálu pracovat.

Požadavky:

* Verze Preview je omezená jenom na veřejné oblasti Azure.
* Podporuje se nejvýš 100 připojení na jeden virtuální rozbočovač. Každé připojení se skládá ze dvou tunelů, které jsou v konfiguraci aktivní–aktivní. Tyto tunely končí v Azure Virtual Hub vpngateway.
* Použití této verze Preview zvažte v následujících případech:
  * Chcete nasadit agregovanou šířku pásma menší než 1 Gb/s na virtuální rozbočovač.
  * Máte zařízení VPN, které podporuje konfiguraci na základě trasy a možnosti připojení IKEv2 IPsec.
  * Chcete použít pobočková zařízení a SD-WAN od partnerů pro uvedení na trh (Riverbed a Citrix).<br>nebo<br>Chcete mezi pobočkami nebo z poboček do Azure nastavit možnosti připojení, které zahrnují správu konfigurace vašich místních zařízení (samoobslužná konfigurace).

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Podporuje se pro Azure Virtual WAN globální VNet Peering? 

 Ne.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Mohou paprskové virtuální sítě připojené k virtuálnímu rozbočovači vzájemně komunikovat?

Ano. Mezi paprsky připojenými v virtuálnímu rozbočovači je možné přímo využít VNet Peering. Další informace najdete v tématu [VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Můžu při použití Azure Virtual WAN nasadit a použít moje oblíbené síťové virtuální zařízení (ve virtuální síti NVA)?

Ano, oblíbené síťové virtuální zařízení (NVA) můžete připojit ke službě Azure Virtual WAN, ale bude to v rozbočovači vyžadovat možnosti směrování, které máme v plánu. Všechny paprsky připojené k virtuální síti NVA musí být navíc připojené k virtuálnímu rozbočovači. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Může virtuální síť NVA mít bránu virtuální sítě?

Ne. Virtuální síť připojená k virtuálnímu rozbočovači nemůže mít bránu virtuální sítě. 

### <a name="is-there-support-for-bgp"></a>Podporuje se BGP?

Ano, BGP se podporuje. Pro zajištění toho, aby se trasy z virtuální sítě NVA odpovídajícím způsobem inzerovaly, musí paprsky připojené k virtuální síti NVA, která je připojená k virtuálnímu rozbočovači, zakázat BGP. Navíc musíte paprskové virtuální sítě připojit k virtuálnímu rozbočovači.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Dá se ve virtuálním rozbočovači směrovat provoz s využitím UDR?

Máme to v plánu. Sledujte nejnovější informace!

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké informace o cenách nebo licencích?
 
Během platnosti verze Preview se neúčtují žádné další poplatky. Aktuální [ceny za přenos dat a Azure VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) zůstávají během verze Preview v platnosti.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Jak se onboardují noví partneři, kteří nejsou uvedení ve vašem seznamu partnerů pro spouštění?

Odešlete email na adresu azurevirtualwan@microsoft.com. Ideální partner je takový, pro jehož zařízení se dá zřídit připojení IKEv2 IPSec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Je možné vytvořit Azure Virtual WAN s využitím šablony Resource Manageru?

Pracujeme na tom. V současnosti je tato služba založená na rozhraní REST a na portálu.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Jsou ve Virtual WAN povolené možnosti připojení mezi jednotlivými pobočkami?

Ano, možnosti připojení mezi jednotlivými pobočkami jsou ve Virtual WAN k dispozici.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Prochází přenos mezi pobočkami přes Azure Virtual WAN?

Ano.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Jak se Virtual WAN liší od existující brány virtuální sítě Azure?

Síť VPN brány virtuální sítě je omezená na 30 tunelů. Pro připojení byste měli pro rozsáhlé sítě VPN používat Virtual WAN. Ve verzi Public Preview je to omezené na připojení 100 poboček s 1 Gb/s v rozbočovači.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Vyžaduje tato služba Virtual WAN ExpressRoute z každé lokality?

Ne, Virtual WAN nevyžaduje ExpressRoute z každé lokality. Využívá standardní připojení IPsec typu Site-to-Site prostřednictvím internetových linek ze zařízení do rozbočovače Azure Virtual WAN.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Platí při použití Azure Virtual WAN nějaké omezení propustnosti sítě?

Ve verzi Public Preview je počet poboček omezený na 100 připojení na centrum/oblast a celkem 1 G v rozbočovači.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Umožňuje Virtual WAN místním zařízením využívat paralelně několik poskytovatelů internetových služeb, nebo jde vždycky o jeden tunel VPN?

Ano, v závislosti na zařízení pobočky můžete mít z jedné pobočky tunely typu aktivní-aktivní (2 tunely = 1 připojení Azure Virtual WAN).

### <a name="how-is-traffic-is-routed-on-the-azure-backbone"></a>Jak se směruje provoz v páteřní síti Azure?

Přenos probíhá takto: zařízení pobočky -> poskytovatel internetových služeb -> Microsoft Edge -> datové centrum Microsoftu -> Microsoft Edge -> poskytovatel internetových služeb -> zařízení pobočky.

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Co je při použití tohoto modelu potřeba na jednotlivých lokalitách? Jenom připojení k internetu?

Ano. Připojení k Internetu a fyzické zařízení, ideálně od některého z našich integrovaných partnerů. Pokud ovšem nechcete konfiguraci a připojení z vašeho preferovaného zařízení do Azure spravovat ručně.