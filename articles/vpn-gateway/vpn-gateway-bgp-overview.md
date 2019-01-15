---
title: Přehled protokolu BGP a sítě Azure VPN Gateway | Dokumentace Microsoftu
description: Tento článek obsahuje přehled protokolu BGP se službou Azure VPN Gateways.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/12/2017
ms.author: yushwang
ms.openlocfilehash: 91e9fe1eb6b3df0b64d05f2b1e300403a9e01db9
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306518"
---
# <a name="about-bgp-with-azure-vpn-gateway"></a>Informace o protokolu BGP se službou Azure VPN Gateway
Tento článek poskytuje přehled podpory protokolu BGP (Border Gateway Protocol) ve službě Azure VPN Gateway.

BGP je standardní směrovací protokol, na internetu běžně používaný k výměně informací o směrování a dostupnosti mezi dvěma nebo více sítěmi. Pokud protokol BGP použijete v rámci virtuálních sítí Azure, umožní službám Azure VPN Gateway a místním zařízením VPN, která se nazývají partnerské uzly protokolu BGP nebo sousedé BGP, výměnu „tras“ informujících obě brány o dostupnosti a dosažitelnosti předpon, které procházejí těmito bránami nebo trasami. Protokol BGP také umožňuje směrování přenosu mezi více sítěmi pomocí šíření tras, které brána s protokolem BGP zjistí od jednoho partnerského uzlu protokolu BGP, do všech dalších partnerských uzlů protokolu BGP. 

## <a name="why"></a>Proč používat protokol BGP?
Protokol BGP je volitelná funkce, kterou můžete použít s trasovými bránami Azure VPN. Dříve než povolíte tuto funkci byste se měli ujistit, že vaše místní zařízení VPN podporuje protokol BGP. Brány Azure VPN a místní zařízení VPN můžete nadále používat i bez protokolu BGP. Je to stejné jako používání statických tras (bez protokolu BGP) *oproti* používání dynamického směrování s protokolem BGP mezi vaší sítí a Azure.

Existuje několik výhod a nových schopností při použití protokolu BGP:

### <a name="prefix"></a>Podpora automatických a flexibilních aktualizací předpon
U protokolu BGP musíte pouze deklarovat minimální předponu určitému partnerskému uzlu protokolu BGP přes tunel S2S VPN s protokolem IPsec. Ta může být malá jako předpona hostitele (/32) IP adresy partnerského uzlu protokolu BGP vašeho místního zařízení VPN. Můžete určit, které předpony místní sítě chcete inzerovat do Azure pro umožnění přístupu službě Azure Virtual Network.

Můžete také inzerovat delší předpony, které mohou obsahovat některé předpony adres vaší virtuální sítě, jako je například velké privátní adresní prostor IP adres (např. 10.0.0.0/8). Mějte na paměti, i když předpony nesmí shodovat s žádnými předponami vaší virtuální sítě. Trasy shodné s předponami vaší virtuální sítě budou odmítnuty.

### <a name="multitunnel"></a>Podpora více tunelů mezi virtuální sítí a místní lokality pomocí automatického převzetí služeb při selhání na základě protokolu BGP
Můžete vytvořit více připojení mezi virtuální sítí Azure a místními zařízeními VPN ve stejném umístění. Tato schopnost poskytuje více tunelů (cest) mezi těmito dvěma sítěmi v konfiguraci aktivní-aktivní. Pokud se odpojí jednoho, odpovídající trasy se odvolají přes protokol BGP a provoz automaticky přesune na zbývající tunely.

Následující diagram ukazuje jednoduchý příklad tohoto vysoce dostupného nastavení:

![Více aktivních cest](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="transitrouting"></a>Podpora směrování přenosu mezi místními sítěmi a více virtuálními sítěmi Azure
Protokol BGP umožňuje více branám zjišťovat a šířit předpony z různých sítí, ať jsou připojeny přímo nebo nepřímo. To umožňuje směrování přenosu pomocí bran Azure VPN mezi vašimi místními weby nebo napříč více službami Azure Virtual Network.

Následující diagram ukazuje příklad topologie vícenásobného předávání s více cestami, které mohou přenášet provoz mezi dvěma místními sítěmi přes brány Azure VPN v rámci služby MSN:

![Vícenásobné předávání přenosu](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="faq"></a>PROTOKOL BGP – NEJČASTĚJŠÍ DOTAZY
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>Další postup
V tématu [Začínáme s protokolem BGP na branách Azure VPN](vpn-gateway-bgp-resource-manager-ps.md) najdete postup konfigurace protokolu BGP pro připojení mezi různými místy a pro připojení mezi virtuálními sítěmi (VNet-to-VNet).

