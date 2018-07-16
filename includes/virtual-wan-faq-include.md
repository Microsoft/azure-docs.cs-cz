---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/10/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5cb1360731eeabe4963330210ba6fe090f0e088a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008822"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Jaký je rozdíl mezi bránou virtuální sítě Azure (VPN Gateway) a Azure Virtual WAN vpngateway?

Služba Virtual WAN poskytuje možnosti připojení typu Site-to-Site ve velkém měřítku a je určená pro zajištění propustnosti, škálovatelnosti a snadného použití. Pobočková zařízení CPE automaticky zřizují službu Azure Virtual WAN a připojují se k ní. Tato zařízení jsou dostupná od stále rostoucího ekosystému partnerů pro SD-WAN a VPN.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Kteří poskytovatelé zařízení (partneři pro Virtual WAN) se podporují při uvedení na trh? 

Plně automatizované prostředí Virtual WAN v současnosti podporují Citrix a Riverbed. V následujících měsících k nim přibudou další partneři, mimo jiné Nokia Nuage, Palo Alto a Checkpoint. Další informace najdete v tématu [Partneři Virtual WAN](https://aka.ms/virtualwan).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Musím použít preferované partnerské zařízení?

Ne. Můžete použít libovolné zařízení s podporou VPN, které vyhovuje požadavkům verze Preview na podporu IKEv2 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partneři pro Virtual WAN automatizují možnosti připojení s využitím Azure Virtual WAN?

Softwarově definovaná řešení možností připojení obvykle spravují svá pobočková zařízení s využitím kontroleru nebo centra pro zřizování zařízení. Kontroler může k automatizaci připojení k Azure Virtual WAN využívat rozhraní API Azure. Další informace o tom, jak to funguje, najdete v tématu věnovaném [automatizaci partnerů pro Virtual WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Mění Virtual WAN stávající funkce připojení?   

Stávající funkce připojení Azure se nemění.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké nové prostředky Resource Manageru?
  
Ano, Virtual WAN zavádí nové prostředky Resource Manageru. Další informace najdete v [přehledu](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Platí pro zapojení do verze Preview nějaké speciální požadavky? 

Abyste mohli nakonfigurovat Azure Virtual WAN, musíte nejdřív zaregistrovat vaše předplatné ve verzi Preview. Pokud se chcete zaregistrovat, pošlete e-mail s ID předplatného na adresu <azurevirtualwan@microsoft.com>. Jakmile se vaše předplatné zaregistruje, dostanete e-mail s potvrzením. Dokud tento e-mail s potvrzením o registraci vašeho předplatného nedostanete, nebudete moct se službou Virtual WAN na portálu pracovat.

Požadavky:

* Verze Preview je omezená jenom na veřejné oblasti Azure.
* Podporuje se nejvýš 100 připojení na jeden virtuální rozbočovač. Každé připojení se skládá ze 2 tunelů, které jsou v konfiguraci aktivní–aktivní. Tyto tunely končí v Azure Virtual Hub vpngateway.
* Použití této verze Preview zvažte v následujících případech:
  * Chcete nasadit agregovanou šířku pásma menší než 1 Gb/s na virtuální rozbočovač.
  * Máte zařízení VPN, které podporuje konfiguraci na základě trasy a možnosti připojení IKEv2 IPsec.
  * Chcete použít pobočková zařízení a SD-WAN od partnerů pro uvedení na trh (Riverbed a Citrix).<br>nebo<br>Chcete mezi pobočkami nebo z poboček do Azure nastavit možnosti připojení, které zahrnují správu konfigurace vašich místních zařízení (samoobslužná konfigurace).

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Podporuje se pro Azure Virtual WAN globální VNet Peering? 

 Ne.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Mohou paprskové virtuální sítě připojené k virtuálnímu rozbočovači vzájemně komunikovat?

Ano. Mezi paprsky připojenými v virtuálnímu rozbočovači je možné přímo využít VNet Peering. Další informace najdete v tématu [VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Můžu při použití Azure Virtual WAN nasadit a použít moje oblíbené síťové virtuální zařízení (ve virtuální síti NVA)?

No, oblíbené síťové virtuální zařízení (NVA) můžete připojit ke službě Azure Virtual WAN, ale bude to v rozbočovači vyžadovat možnosti směrování, které budou k dispozici ve fázi GA. Všechny paprsky připojené k virtuální síti NVA musí být navíc připojené k virtuálnímu rozbočovači. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Může virtuální síť NVA mít bránu virtuální sítě?

Ne. Virtuální síť připojená k virtuálnímu rozbočovači nemůže mít bránu virtuální sítě. 

### <a name="is-there-support-for-bgp"></a>Podporuje se BGP?

Ano, BGP se podporuje. Pro zajištění toho, aby se trasy z virtuální sítě NVA odpovídajícím způsobem inzerovaly, musí paprsky připojené k virtuální síti NVA, která je připojená k virtuálnímu rozbočovači, zakázat BGP. Navíc musíte paprskové virtuální sítě připojit k virtuálnímu rozbočovači.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Dá se ve virtuálním rozbočovači směrovat provoz s využitím UDR?

Funkce směrování a UDR budou dostupné ve fázi GA.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké informace o cenách nebo licencích?
 
Neúčtují se žádné další poplatky. Aktuální [ceny za přenos dat a Azure VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) zůstávají během verze Preview v platnosti.