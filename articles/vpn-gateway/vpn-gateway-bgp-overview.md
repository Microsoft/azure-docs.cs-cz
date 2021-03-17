---
title: 'BGP a Azure VPN Gateway: Přehled'
description: Přečtěte si o protokolu BGP (Border Gateway Protocol) v Azure VPN, což je standardní internetový protokol pro výměnu informací o směrování a dostupnosti mezi sítěmi.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 464d00cbeddbacd617b1d2c88f9e5f68cc5d996e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400870"
---
# <a name="about-bgp-with-azure-vpn-gateway"></a>Informace o protokolu BGP s Azure VPN Gateway
Tento článek poskytuje přehled podpory protokolu BGP (Border Gateway Protocol) v Azure VPN Gateway.

BGP je standardní směrovací protokol, na internetu běžně používaný k výměně informací o směrování a dostupnosti mezi dvěma nebo více sítěmi. Pokud protokol BGP použijete v rámci virtuálních sítí Azure, umožní službám Azure VPN Gateway a místním zařízením VPN, která se nazývají partnerské uzly protokolu BGP nebo sousedé BGP, výměnu „tras“ informujících obě brány o dostupnosti a dosažitelnosti předpon, které procházejí těmito bránami nebo trasami. Protokol BGP také umožňuje směrování přenosu mezi více sítěmi pomocí šíření tras, které brána s protokolem BGP zjistí od jednoho partnerského uzlu protokolu BGP, do všech dalších partnerských uzlů protokolu BGP. 

## <a name="why-use-bgp"></a><a name="why"></a>Proč používat protokol BGP?
Protokol BGP je volitelná funkce, kterou můžete použít s trasovými bránami Azure VPN. Dříve než povolíte tuto funkci byste se měli ujistit, že vaše místní zařízení VPN podporuje protokol BGP. Brány Azure VPN a místní zařízení VPN můžete nadále používat i bez protokolu BGP. Je to stejné jako používání statických tras (bez protokolu BGP) *oproti* používání dynamického směrování s protokolem BGP mezi vaší sítí a Azure.

Existuje několik výhod a nových schopností při použití protokolu BGP:

### <a name="support-automatic-and-flexible-prefix-updates"></a><a name="prefix"></a>Podpora automatických a flexibilních aktualizací předpon
U protokolu BGP musíte pouze deklarovat minimální předponu určitému partnerskému uzlu protokolu BGP přes tunel S2S VPN s protokolem IPsec. Ta může být malá jako předpona hostitele (/32) IP adresy partnerského uzlu protokolu BGP vašeho místního zařízení VPN. Můžete určit, které předpony místní sítě chcete inzerovat do Azure pro umožnění přístupu službě Azure Virtual Network.

Můžete také inzerovat větší předpony, které mohou obsahovat některé předpony adres vaší virtuální sítě, jako je například velký adresní prostor privátních IP adres (například 10.0.0.0/8). Všimněte si, že předpony nemůžou být stejné jako žádné z předpon vaší virtuální sítě. Trasy shodné s předponami vaší virtuální sítě budou odmítnuty.

### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a><a name="multitunnel"></a>Podpora více tunelů mezi virtuální sítí a místním webem s automatickým převzetím služeb při selhání na základě protokolu BGP
Můžete vytvořit více připojení mezi virtuální sítí Azure a místními zařízeními VPN ve stejném umístění. Tato schopnost poskytuje více tunelů (cest) mezi těmito dvěma sítěmi v konfiguraci aktivní-aktivní. Pokud je jeden z tunelů odpojený, odpovídající trasy se odeberou prostřednictvím protokolu BGP a přenos se automaticky přesune na zbývající tunely.

Následující diagram ukazuje jednoduchý příklad tohoto vysoce dostupného nastavení:

![Více aktivních cest](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a><a name="transitrouting"></a>Podpora směrování přenosu mezi místními sítěmi a více virtuálními sítěmi Azure
Protokol BGP umožňuje více branám zjišťovat a šířit předpony z různých sítí, ať jsou připojeny přímo nebo nepřímo. To umožňuje směrování přenosu pomocí bran Azure VPN mezi vašimi místními weby nebo napříč více službami Azure Virtual Network.

Následující diagram ukazuje příklad topologie vícenásobného předávání s více cestami, které mohou přenášet provoz mezi dvěma místními sítěmi přes brány Azure VPN v rámci služby MSN:

![Vícenásobné předávání přenosu](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faq"></a><a name="faq"></a>NEJČASTĚJŠÍ DOTAZY K PROTOKOLU BGP
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>Další kroky
V tématu [Začínáme s protokolem BGP na branách Azure VPN](vpn-gateway-bgp-resource-manager-ps.md) najdete postup konfigurace protokolu BGP pro připojení mezi různými místy a pro připojení mezi virtuálními sítěmi (VNet-to-VNet).

