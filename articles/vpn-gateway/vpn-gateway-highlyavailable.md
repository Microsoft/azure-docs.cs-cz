---
title: Přehled konfigurací s vysokou dostupností s bránami Azure VPN Gateway | Dokumentace Microsoftu
description: Tento článek obsahuje přehled konfigurací s vysokou dostupností se službami Azure VPN Gateway.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: ''
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2016
ms.author: yushwang
ms.openlocfilehash: c510bb060d5c0dc866c3802fab751c1cbeff3745
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42058027"
---
# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Připojení s vysokou dostupností mezi jednotlivými místy a VNet-to-VNet
Tento článek obsahuje přehled konfigurací s vysokou dostupností pro vaše propojení mezi jednotlivými místy a propojení VNet-to-VNet se službami Azure VPN Gateway.

## <a name = "activestandby"></a>O redundanci Azure VPN gateway
Každá Azure VPN Gateway se skládá ze dvou instancí v konfiguraci aktivní-pohotovostní. Při jakékoli plánované údržbě nebo neplánovaném přerušení, které vyřadí aktivní instanci, pohotovostní instance automaticky převezme službu (převzetí služeb při selhání) a obnoví spojení S2S VPN nebo VNet-to-VNet. Přepnutí způsobí jen velmi krátké přerušení služeb. Při plánované údržbě by se spojení mělo obnovit během 10 až 15 sekund. U neplánovaných problémů bude obnovení spojení trvat déle, v nejhorším případě minutu až minutu a půl. V případě připojení klienta P2S VPN k bráně budou spojení P2S odpojena a uživatelé se budou muset na klientských počítačích znovu připojit.

![Aktivní–pohotovostní konfigurace](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Připojení s vysokou dostupností mezi jednotlivými místy
Pro zajištění lepší dostupnosti pro připojení mezi jednotlivými místy existuje několik možnosti:

* Několik místních zařízení VPN
* Azure VPN Gateway v konfiguraci aktivní-aktivní
* Kombinace obojího

### <a name = "activeactiveonprem"></a>Několik místních zařízení VPN
Můžete ve své místní síti použít k připojení k Azure VPN Gateway několik zařízení VPN, viz následující diagram:

![Několik místních zařízení VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

V této konfiguraci existuje několik aktivních tunelů ze stejné Azure VPN Gateway do různých zařízení ve stejném umístění. Existuje několik požadavků a omezení:

1. Je třeba vytvořit víc spojení S2S VPN mezi vašimi zařízeními VPN a službou Azure VPN. Když připojíte několik zařízení VPN ze stejné místní sítě do Azure, je potřeba vytvořit jednu bránu místní sítě pro každé zařízení VPN a jedno připojení z Azure VPN gateway pro každé bráně místní sítě.
2. Brány místní sítě odpovídající zařízení VPN musí mít jedinečné veřejné IP adresy, zadané vlastností GatewayIpAddress.
3. Pro tuto konfiguraci se vyžaduje BGP. Každá brána místní sítě reprezentující zařízení VPN musí mít jedinečnou IP adresu partnerského uzlu protokolu BGP, zadanou vlastností BgpPeerIpAddress.
4. Pole vlastnosti AddressPrefix v každé bráně místní sítě se nesmí překrývat. Do pole AddressPrefix zadávejte vlastnost BgpPeerIpAddress ve formátu /32 CIDR, například 10.200.200.254/32.
5. Protokol BGP byste měli použít k inzerování stejných předpon stejných místních sítí pro Azure VPN Gateway, provoz pak bude přes tyto tunely směrován současně.
6. Každé spojení se započítává do maximálního počtu tunelů pro vaši službu Azure VPN Gateway – 10 pro SKU úrovně Basic a Standard a 30 pro SKU úrovně HighPerformance. 

V této konfiguraci je Azure VPN Gateway stále v režimu aktivní–pohotovostní, takže platí stejné převzetí služeb při selhání a stejné přerušení služby, jak je popsáno [nahoře](#activestandby). Tato konfigurace ale chrání proti selháním nebo přerušením, jejichž příčina je v místní síti nebo v zařízení VPN.

### <a name="active-active-azure-vpn-gateway"></a>Azure VPN Gateway v konfiguraci aktivní-aktivní
Nyní je možné vytvořit Azure VPN Gateway v konfiguraci aktivní–aktivní, kdy obě instance virtuálních počítačů brány udržují VPN tunely S2S se zařízeními VPN místní sítě, jak je znázorněno v následujícím diagramu:

![Aktivní–aktivní](./media/vpn-gateway-highlyavailable/active-active.png)

V této konfiguraci bude mít každá instance brány Azure jedinečnou veřejnou IP adresu a každá vytvoří VPN tunel IPSec/IKE S2S k vašemu místnímu zařízení VPN určenému pro bránu místní sítě a pro spojení. Všimněte si, že oba tunely VPN jsou ve skutečnosti součástí stejného připojení. I nadále bude třeba konfigurovat vaše zařízení VPN v místní síti tak, aby přijímala nebo navazovala dva VPN tunely S2S s těmito dvěma veřejnými IP adresami Azure VPN Gateway.

Protože instance brány Azure jsou v konfiguraci aktivní–aktivní, provoz mezi vaší virtuální sítí Azure a místní sítí bude směrován přes oba tunely současně, i když vaše místní zařízení VPN můžou jeden z nich upřednostňovat. Pamatujte ale, že stejný tok TCP nebo UDP bude vždy přenášen stejným tunelem, pokud na příslušné instanci nedojde k poruše.

Když na jedné z instancí dojde k plánované údržbě nebo neplánované události, tunel IPsec z této instance do vašeho místního zařízení VPN se odpojí. Odpovídající trasy pro vaše zařízení VPN by se měly uzavřít automaticky a provoz by se měl přepnout na druhý aktivní tunel IPsec. Na straně Azure se přepnutí z vyřazené instance na aktivní provede automaticky.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Dvojitá redundance: brány VPN v konfiguraci aktivní–aktivní na straně Azure i v místní síti
Nejspolehlivější možností je použití bran v konfiguraci aktivní–aktivní jak ve vaší síti, tak na straně Azure, jak je znázorněno na diagramu dole.

![Dvojitá redundance](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

V tomto případě vytvoříte Azure VPN Gateway v konfiguraci aktivní–aktivní, dvě brány místní sítě a dvě spojení pro každé z vašich dvou místních zařízení VPN, jak bylo popsáno nahoře. Výsledkem je plná síť propojení – 4 tunely IPsec mezi virtuální sítí Azure a vaší místní sítí.

Všechny brány a tunely na straně Azure jsou aktivní, takže se provoz rozdělí mezi všechny 4 tunely současně, ačkoli každý tok TCP nebo UDP bude opět využívat stejný tunel nebo trasu na straně Azure. Přestože výsledkem rozprostření provozu může být i zvýšení propustnosti tunelů IPsec, ale hlavním cílem této techniky je zajištění vysoké dostupnosti. Vzhledem k náhodné povaze tohoto rozprostření je těžké přesně změřit, jak různé podmínky přenosu aplikačních dat ovlivní agregovanou propustnost.

Tato topologie vyžaduje dvě brány místní sítě a dvě spojení na pár místních zařízení VPN. Aby bylo možné vytvořit dvě spojení s jednou místní sítí, je vyžadován protokol BGP. Tyto požadavky jsou stejné jako v případě [nahoře](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Připojení s vysokou dostupností VNet-to-VNet prostřednictvím služby Azure VPN Gateway
Stejná konfigurace aktivní–aktivní může platit i pro spojení Azure VNet-to-VNet. Můžete vytvořit brány VPN v konfigurace aktivní–aktivní pro obě virtuální sítě a jejich vzájemným propojením vytvořit stejnou úplnou síť 4 tunelů mezi oběma sítěmi VNet, jak je znázorněno na diagramu dole:

![VNet-to-VNet](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

Tím se zajistí, že vždy existuje dvojice tunelových spojení mezi oběma virtuálními sítěmi pro případ výpadků nebo plánované údržby a tím ještě lepší dostupnost. Ačkoli stejná topologie pro propojení dvou míst vyžaduje dvě spojení, topologie VNet-to-VNet znázorněná nahoře potřebuje jen jedno spojení na každou bránu. Kromě toho je protokol BGP volitelný, pokud není vyžadováno tranzitní směrování přes spojení VNet-to-VNet.

## <a name="next-steps"></a>Další postup
V tématu [Konfigurace bran VPN typu aktivní–aktivní pro spojení mezi jednotlivými místy a VNet-to-VNet](vpn-gateway-activeactive-rm-powershell.md) najdete postupy pro vytvoření konfigurace aktivní–aktivní pro spojení VPN VNet-to-VNet a spojení mezi místy.

