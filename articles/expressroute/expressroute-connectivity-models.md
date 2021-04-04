---
title: 'Azure ExpressRoute: modely připojení'
description: Zkontrolujte připojení mezi sítí zákazníka, Microsoft Azure a službami Microsoft 365. Zákazníci můžou používat poskytovatele MPLS, cloudové výměny a ethernetové.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: duau
ms.openlocfilehash: fb35a03b5dd8780445eb27f485966e3c3452feea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978746"
---
# <a name="expressroute-connectivity-models"></a>Modely připojení ExpressRoute
Propojení mezi vaší místní sítí a cloudem Microsoftu můžete vytvořit čtyřmi různými způsoby, [CloudExchange společné](#CloudExchange)připojení [Ethernet typu Point-to-Point](#Ethernet) [(IPVPN)](#IPVPN)a [ExpressRoute Direct](#Direct). Poskytovatelé připojení můžou nabízet jeden nebo víc modelů připojení. Můžete ve spolupráci s poskytovatelem připojení vybrat model, který vám bude nejlépe vyhovovat.
<br><br>

:::image type="content" source="./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png" alt-text="Diagram modelů připojení ExpressRoute":::

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Společně umístěné v cloudové výměně
Pokud jste společně umístěni v rámci zařízení s cloudovým výměnou, můžete objednat virtuální křížové připojení ke cloudu Microsoftu prostřednictvím ethernetové výměny poskytovatele ve společném umístění. Poskytovatelé ve společném umístění můžou nabízet křížová připojení vrstvy 2 nebo spravovaná křížová připojení vrstvy 3 mezi vaší infrastrukturou ve společném umístění a cloudem Microsoftu.

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>Ethernetová připojení typu Point-to-Point
Místní datová centra nebo pobočky můžete připojit ke cloudu Microsoftu prostřednictvím ethernetových propojení typu point-to-point. Poskytovatelé ethernetových pripojení typu point-to-point můžou nabízet připojení vrstvy 2 nebo spravovaná připojení vrstvy 3 mezi vaší lokalitou a cloudem Microsoftu.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Sítě typu any-to-any (IPVPN)
S cloudem Microsoftu můžete integrovat vaši síť WAN. Poskytovatelé IPVPN (obvykle MPLS VPN) nabízejí připojení typu any-to-any mezi pobočkami a datovými centry. Cloud Microsoftu může být připojen do vaší sítě WAN, aby vypadal stejně jako jiné pobočky. Poskytovatelé sítě WAN obvykle nabízejí spravované připojení vrstvy 3. Funkce a možnosti ExpressRoute jsou u všech výše uvedených modelů připojení identické.

## <a name="direct-from-expressroute-sites"></a><a name="Direct"></a>Přímý odkaz z webů ExpressRoute
K globální síti společnosti Microsoft se můžete připojit přímo v umístění partnerského vztahu, který je strategicky distribuován po celém světě. [ExpressRoute Direct](expressroute-erdirect-about.md) poskytuje připojení Dual 100 GB/s nebo 10 GB, které podporuje škálování aktivní/aktivní.

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o připojeních ExpressRoute a doménách směrování. Viz [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Seznamte se s funkcemi ExpressRoute. [Technický přehled ExpressRoute](expressroute-introduction.md)
* Vyhledejte poskytovatele služeb. Viz [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* Přečtěte si požadavky pro [Směrování](expressroute-routing.md), [Překlad adres (NAT)](expressroute-nat.md)a [technologii QoS](expressroute-qos.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurace směrování](expressroute-howto-routing-portal-resource-manager.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)